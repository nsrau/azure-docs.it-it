<properties 
	pageTitle="Spostare i dati da/in Azure SQL Data Warehouse | Microsoft Azure" 
	description="Informazioni su come spostare i dati da e verso Azure SQL Data Warehouse mediante Data factory di Azure" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2016" 
	ms.author="spelluru"/>

# Spostare dati da e verso Azure SQL Data Warehouse mediante Data factory di Azure

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare dati da Azure SQL Data Warehouse in un altro archivio dati e viceversa. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia nonché delle origini dati e dei sink supportati per SQL Data Warehouse.

Gli esempi seguenti mostrano come copiare dati da e in Azure SQL Data Warehouse e nell'archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in uno qualsiasi dei sink indicati nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md#supported-data-stores), usando l'attività di copia in Azure Data Factory.

> [AZURE.NOTE] 
Per una panoramica del servizio Azure Data Factory, vedere [Introduzione al servizio Azure Data Factory](data-factory-introduction.md).
> 
> Questo articolo fornisce esempi JSON, ma non include istruzioni dettagliate per la creazione di una data factory. Per una procedura con istruzioni dettagliate sull'uso dell'attività di copia in Azure Data Factory, vedere [Esercitazione: Copiare i dati di un BLOB di Azure in database SQL di Azure](data-factory-get-started.md).


## Esempio: Copiare i dati da Azure SQL Data Warehouse al BLOB di Azure

L'esempio seguente mostra:

1. Un servizio collegato di tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties). 
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell’esempio vengono copiati dati appartenenti a una serie temporale da una tabella nel database SQL Data Warehouse di Azure a un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di Azure SQL Data Warehouse:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Servizio collegato di archiviazione BLOB di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Set di dati di input di Azure SQL Data Warehouse:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in Azure SQL Data Warehouse e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.
 
Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Data factory che la tabella è esterna e non è prodotta da un'attività al suo interno.

	{
	  "name": "AzureSqlDWInput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **SqlDWSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLDWtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSqlDWInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlDWSource",
	            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

> [AZURE.NOTE] Nell'esempio precedente, **sqlReaderQuery** è specificata per il SqlDWSource. L'attività di copia esegue questa query nell'origine dell’SQL Data Warehouse di Azure per ottenere i dati.
>  
> In alternativa, è possibile specificare una stored procedure specificando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).
>  
> Se non si specifica il parametro sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura del set di dati JSON vengono usate per compilare una query (selezionare column1, column2 da mytable) da eseguire nell’SQL Data Warehouse. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

## Esempio: Copiare i dati dal BLOB di Azure ad Azure SQL Data Warehouse

L'esempio seguente mostra:

1.	Un servizio collegato di tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties).


Nell’esempio vengono copiati dati appartenenti a una serie temporale dal BLOB di Azure a una tabella nel database SQL Data Warehouse di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di Azure SQL Data Warehouse:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Servizio collegato di archiviazione BLOB di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Set di dati di input del BLOB di Azure:**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, e giorno dell'ora di inizio e il nome del file usa la parte dell'ora di inizio relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Set di dati di output di Azure SQL Data Warehouse:**

Nell’esempio vengono copiati dati in una tabella denominata "MyTable" in Azure SQL Data Warehouse. È necessario creare la tabella nel database SQL Data Warehouse di Azure con lo stesso numero di colonne di quelle previste nel file file con estensione csv del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

	{
	  "name": "AzureSqlDWOutput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **BlobSource** e il tipo di **sink** è impostato su **SqlDWSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQLDW",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlDWOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlDWSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

Vedere l’articolo [Caricare i dati con Azure Data Factory](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) nella documentazione di SQL Data Warehouse di Azure per una procedura dettagliata.

## Proprietà del servizio collegato di Azure SQL Data Warehouse

La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di Azure SQL Data Warehouse

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà del tipo deve essere impostata su: **AzureSqlDW** | Sì
**connectionString** | Specificare le informazioni necessarie per connettersi all'istanza di Azure SQL Data Warehouse per la proprietà connectionString. | Sì

Nota: è necessario configurare il [firewall del database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). È necessario configurare il server del database per [consentire ai servizi di Azure di accedere al server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Inoltre, se si copiano dati in Azure SQL Data Warehouse dall’esterno di Azure e da origini dati locali con gateway di data factory, sarà necessario configurare un intervallo di indirizzi IP appropriato per la macchina che sta inviando dati a Azure SQL Data Warehouse.

## Proprietà del tipo di set di dati Azure SQL Data Warehouse

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, fare riferimento all'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure e così via).

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **AzureSqlDWTable** presenta le proprietà seguenti.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella nell'istanza del database SQL Data Warehouse di Azure a cui fa riferimento il servizio collegato. | Sì |

## Proprietà del tipo di attività di copia di Azure SQL Data Warehouse

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.

**Nota:** l'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

### SqlDWSource

In caso di attività di copia con origine di tipo **SqlDWSource**, sono disponibili le proprietà seguenti nella sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Usare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare * da MyTable. | No |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. | Nome della stored procedure. | No |
| storedProcedureParameters | Parametri per la stored procedure. | Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |

Se la proprietà **sqlReaderQuery** è specificata per SqlDWSource, l'attività di copia esegue questa query nell'origine SQL Data Warehouse di Azure per ottenere i dati.

In alternativa, è possibile specificare una stored procedure specificando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica il parametro sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura del set di dati JSON vengono usate per compilare una query (selezionare column1, column2 da mytable) da eseguire nell’SQL Data Warehouse. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

#### Esempio SqlDWSource

    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**Definizione della stored procedure:**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO
 

### SqlDWSink
**SqlDWSink** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. | Numero intero. (Unità = conteggio righe) | No. (Predefinito = 10000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. | (Unità = intervallo di tempo) Esempio: "00:30:00" (30 minuti). | No | 
| sqlWriterCleanupScript | Query specificata dall'utente per l'attività di copia da eseguire in modo che i dati di una sezione specifica vengano eliminati. Vedere di seguito la sezione ripetibilità per ulteriori dettagli. | Istruzione di query. | No |
| allowPolyBase | Indica se usare PolyBase, quando applicabile, invece del meccanismo BULKINSERT per caricare i dati in Azure SQL Data Warehouse. <br/><br/>Si noti che al momento è supportato solo il set di dati **BLOB di Azure** con **format** impostato su **TextFormat** come set di dati di origine. Il supporto per altri tipi di origine sarà presto disponibile. <br/><br/>Per informazioni su vincoli e dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse). | True <br/>False (valore predefinito) | No |  
| polyBaseSettings | Gruppo di proprietà che è possibile specificare quando la proprietà **allowPolybase** è impostata su **true**. | &nbsp; | No |  
| rejectValue | Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo. <br/><br/>Per altre informazioni sulle opzioni di rifiuto di PolyBase, vedere la sezione **Argomenti** dell'argomento [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). | 0 (impostazione predefinita), 1, 2, … | No |  
| rejectType | Indica se l'opzione rejectValue viene specificata come valore letterale o come percentuale. | Value (impostazione predefinita), Percentage | No |   
| rejectSampleValue | Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate. | 1, 2, … | Sì se **rejectType** è **percentage** |  
| useTypeDefault | Specifica come gestire i valori mancanti nei file con testo delimitato quando PolyBase recupera dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Argomenti in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). | True, False (valore predefinito) | No | 


#### Esempio SqlDWSink


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## Usare PolyBase per caricare dati in Azure SQL Data Warehouse
**PolyBase** consente di caricare in modo efficiente quantità elevate di dati da un archivio BLOB di Azure ad Azure SQL Data Warehouse con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito.

Se l'archivio dati di origine non è un archivio BLOB di Azure, prendere in considerazione prima di tutto la copia dei dati dall'archivio dati di origine all'archivio BLOB di Azure come fase di staging e quindi usare PolyBase per caricare i dati in Azure SQL Data Warehouse dall'archivio di staging. In questo scenario si useranno due attività di copia, con la prima attività di copia configurata per copiare i dati dall'archivio dati di origine all'archivio BLOB di Azure e la seconda attività di copia per copiare i dati dall'archivio BLOB di Azure ad Azure SQL Data Warehouse mediante PolyBase.

Impostare la proprietà **allowPolyBase** su **true**, come illustrato nell'esempio seguente per Azure Data Factory, per usare PolyBase per copiare i dati dall'archivio BLOB di Azure ad Azure SQL Data Warehouse. Quando si imposta allowPolyBase su true, è possibile specificare proprietà specifiche di PolyBase usando il gruppo di proprietà **polyBaseSettings**. Per informazioni dettagliate sulle proprietà disponibili con polyBaseSettings, vedere la sezione [SqlDWSink](#SqlDWSink).


    "sink": {
        "type": "SqlDWSink",
		"allowPolyBase": true,
		"polyBaseSettings":
		{
			"rejectType": "percentage",
			"rejectValue": 10,
			"rejectSampleValue": 100,
			"useTypeDefault": true 
		}

    }

Azure Data Factory verifica se i dati soddisfano i requisiti seguenti prima di usare PolyBase per copiare i dati in Azure SQL Data Warehouse. Se i requisiti non vengono soddisfatti, verrà usato automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1.	Il **servizio collegato di origine** è di tipo **Archiviazione di Azure** e non è configurato per l'uso dell'autenticazione con firma di accesso condiviso. Per informazioni dettagliate, vedere [Servizio collegato Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).  
2. Il **set di dati di input** è di tipo **BLOB di Azure** e le proprietà del tipo di set di dati soddisfano i criteri seguenti: 
	1. **Type** deve essere **TextFormat**. 
	2. **rowDelimiter** deve essere **\\n**. 
	3. **nullValue** è impostata su **stringa vuota** (""). 
	4. **encodingName** è impostata su **utf-8**, ovvero il valore **predefinito**, quindi non impostarla su un valore diverso. 
	5. **escapeChar** e **quoteChar** non sono specificate. 
	6. **Compression** non è **BZIP2**.
	 
			"typeProperties": {
				"folderPath": "<blobpath>",
				"format": {
					"type": "TextFormat",     
					"columnDelimiter": "<any delimiter>", 
					"rowDelimiter": "\n",       
					"nullValue": "",           
					"encodingName": "utf-8"    
				},
            	"compression": {  
                	"type": "GZip",  
	                "level": "Optimal"  
    	        }  
			},
3.	Non è disponibile alcuna impostazione **skipHeaderLineCount** in **BlobSource** per l'attività di copia nella pipeline. 
4.	Non è disponibile alcuna impostazione **sliceIdentifierColumnName** in **SqlDWSink** per l'attività di copia nella pipeline. PolyBase garantisce che tutti i dati verranno aggiornati o che nessun dato verrà aggiornato in una singola esecuzione. Per ottenere la **ripetibilità**, è possibile usare **sqlWriterCleanupScript**.
5.	Nell'attività di copia associata non viene usato alcun valore **columnMapping**. 

### Procedure consigliate per l'uso di PolyBase

#### Limitazione delle dimensioni delle righe
Polybase non supporta righe con dimensioni superiori a 32 KB. Un tentativo di caricare una tabella con righe superiori a 32 KB provocherebbe l'errore seguente:

	Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

Se sono presenti dati di origine con righe di dimensioni superiori a 32 KB, è consigliabile suddividere verticalmente le tabelle di origine in alcune tabelle più piccole, in cui le dimensioni massime delle righe di ogni tabella non supera il limite previsto. Le tabelle più piccole possono essere quindi caricate usando PolyBase e unite in Azure SQL Data Warehouse.

#### tableName in Azure SQL Data Warehouse
La tabella seguente fornisce esempi relativi a come specificare la proprietà **tableName** nel set di dati JSON per diverse combinazioni di nomi di schema e di tabella.

| Schema di database | Nome tabella | Proprietà JSON tableName |
| --------- | -----------| ----------------------- | 
| dbo | MyTable | MyTable o dbo.MyTable o [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable o [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] o [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Se viene visualizzato un errore analogo al seguente, è possibile che si sia verificato un problema con il valore specificato per la proprietà tableName. Per informazioni sul modo corretto per specificare i valori per la proprietà JSON tableName, vedere la tabella precedente.

	Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### Colonne con valori predefiniti
La funzionalità PolyBase in Data Factory accetta attualmente lo stesso numero di colonne disponibili nella tabella di destinazione. Se, ad esempio, è disponibile una tabella con 4 colonne e una di esse è definita con un valore predefinito, i dati di input conterranno comunque 4 colonne. Se si specifica un set di dati di input con 3 colonne, si verificherà un errore analogo al seguente:

	All columns of the table must be specified in the INSERT BULK statement.

Il valore NULL è una forma speciale di valore predefinito. Se la colonna ammette valori Null, i dati di input (nel BLOB) per tale colonna possono essere vuoti, ma non possono essere mancanti dal set di dati di input. PolyBase inserirà automaticamente NULL per tali valori in Azure SQL Data Warehouse.

#### Sfruttamento dei vantaggi della copia in due fasi per usare PolyBase
PolyBase ha alcune limitazioni relativi agli archivi dati e ai formati utilizzabili. Se lo scenario non soddisfa i requisiti, è necessario sfruttare i vantaggi dell'attività di copia per copiare i dati in un archivio dati supportato da PolyBase e/o convertire i dati in un formato supportato da PolyBase. Ecco alcuni esempi delle trasformazioni che è possibile eseguire:

-	Convertire i file di origine con altre codifiche nei BLOB di Azure in UTF-8.
-	Serializzare i dati in SQL Server/database SQL di Azure in BLOB di Azure in formato CSV.
-	Cambiare l'ordine delle colonne specificando la proprietà columnMapping.

Ecco alcuni suggerimenti relativi alle trasformazioni:

- Selezione di un delimitatore appropriato durante la conversione di dati tabulari nei file CSV.

	È consigliabile usare come delimitatori di colonna caratteri che molto probabilmente non compariranno dei dati. I delimitatori comuni includono virgola (,), tilde (~), barra verticale (|) e tabulazione (\\t). Se i dati contengono questi caratteri, è possibile impostare il delimitatore di colonna su caratteri non stampabili, ad esempio "\\u0001". Polybase accetta delimitatori di colonna con più caratteri. Ciò consente di creare delimitatori di colonna più complessi.	
- Formato di oggetti datetime.

	Quando gli oggetti datetime vengono serializzati, l'attività di copia usa per impostazione predefinita il formato "aaaa-MM-gg HH:mm:ss.fffffff", che, per impostazione predefinita, non è supportato da PolyBase. Per informazioni sui formati di datetime supportati, vedere [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). Se non si rispettano i requisiti di PolyBase in merito al formato di datetime, si verificherà un errore analogo al seguente:

		Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 1 rows processed.
		(/AccountDimension)Column ordinal: 97, Expected data type: DATETIME NOT NULL, Offending value: 2010-12-17 00:00:00.0000000  (Column Conversion Error), Error: Conversion failed when converting the NVARCHAR value '2010-12-17 00:00:00.0000000' to data type DATETIME.

	Per risolvere questo errore, specificare il formato di datetime come illustrato nell'esempio seguente:
	
		"structure": [
    		{ "name" : "column", "type" : "int", "format": "yyyy-MM-dd HH:mm:ss" }
		]


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per Azure SQL Data Warehouse

Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipo automatico da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da e verso SQL Azure, SQL Server, Sybase verranno usati i mapping seguenti dal tipo SQL di tipo .NET e viceversa.

Il mapping è uguale al [mapping del tipo di dati di SQL Server per ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo di motore di database di SQL Server | Tipo di .NET Framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| Bit | Boolean |
| char | String, Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimale | Decimale |
| FILESTREAM attribute (varbinary(max)) | Byte |
| Float | Double |
| immagine | Byte | 
| int | Int32 | 
| money | Decimale |
| nchar | String, Char |
| ntext | String, Char |
| numeric | Decimale |
| nvarchar | String, Char |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimale | 
| sql\_variant | Object * |
| text | String, Char |
| time | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | String, Char |
| xml | Xml |



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Prestazioni e ottimizzazione  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati (attività di copia) in Azure Data Factory e sui vari modi per ottimizzarle, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0420_2016-->