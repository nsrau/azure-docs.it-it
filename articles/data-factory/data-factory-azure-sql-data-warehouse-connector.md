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
	ms.date="09/20/2016" 
	ms.author="spelluru"/>

# Spostare dati da e verso Azure SQL Data Warehouse mediante Data factory di Azure

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare dati tra Azure SQL Data Warehouse e un altro archivio dati.

È possibile specificare se si desidera usare PolyBase durante il caricamento dei dati in Azure SQL Data Warehouse. È consigliabile usare PolyBase per ottenere prestazioni ottimali quando si caricano i dati in Azure SQL Data Warehouse. Vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) per i dettagli.


## Copia di dati guidata
Il modo più semplice di creare una pipeline che copia i dati in/da Azure SQL Data Warehouse consiste nell'usare la procedura Copia di dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tali esempi mostrano come copiare dati in e da Azure SQL Data Warehouse e in e dall'archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.


> [AZURE.NOTE] 
Per una panoramica del servizio Azure Data Factory, vedere [Introduzione al servizio Azure Data Factory](data-factory-introduction.md).
> 
> Questo articolo fornisce esempi JSON, ma non include istruzioni dettagliate per la creazione di una data factory. Per una procedura con istruzioni dettagliate sull'uso dell'attività di copia in Azure Data Factory, vedere l'esercitazione [Copiare dati da un archivio BLOB al database SQL usando Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## Esempio: Copiare i dati da Azure SQL Data Warehouse al BLOB di Azure

L'esempio definisce le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio copia ogni ora i dati di una serie temporale (con frequenza oraria, giornaliera e così via) da una tabella del database di Azure SQL Data Warehouse a un BLOB. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

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
 
Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
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

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **SqlDWSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

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

> [AZURE.NOTE] Nell'esempio, la proprietà **sqlReaderQuery** è specificata per SqlDWSource. L'attività di copia esegue questa query nell'origine dell’SQL Data Warehouse di Azure per ottenere i dati.
>  
> In alternativa, è possibile specificare una stored procedure specificando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).
>  
> Se non si specifica il parametro sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura del set di dati JSON vengono usate per compilare una query (selezionare column1, column2 da mytable) da eseguire nell’SQL Data Warehouse. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

## Esempio: Copiare i dati dal BLOB di Azure ad Azure SQL Data Warehouse

L'esempio definisce le entità di Data Factory seguenti:

1.	Un servizio collegato di tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties).


L'esempio copia ogni ora i dati di una serie temporale (con frequenza oraria, giornaliera e così via) da un BLOB di Azure a una tabella del database di Azure SQL Data Warehouse. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

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

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti di anno, mese e giorno della data/ora di inizio e il nome file usa la parte relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
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

Nell’esempio vengono copiati dati in una tabella denominata "MyTable" in Azure SQL Data Warehouse. Creare la tabella in Azure SQL Data Warehouse con lo stesso numero di colonne previsto nel file CSV del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

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

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **BlobSource** e il tipo di **sink** è impostato su **SqlDWSink**.

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

Per una procedura dettagliata, vedere l'articolo [Caricare i dati con Azure Data Factory](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) nella documentazione di Azure SQL Data Warehouse.

## Proprietà del servizio collegato di Azure SQL Data Warehouse

La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di Azure SQL Data Warehouse

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà del tipo deve essere impostata su: **AzureSqlDW** | Sì
**connectionString** | Specificare le informazioni necessarie per connettersi all'istanza di Azure SQL Data Warehouse per la proprietà connectionString. | Sì

> [AZURE.IMPORTANT] Configurare il [firewall del database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e il server di database in modo da [consentire ai servizi di Azure di accedere al server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Se si copiano dati in Azure SQL Data Warehouse dall'esterno di Azure e da origini dati locali con gateway di data factory, configurare anche un intervallo di indirizzi IP appropriato per il computer che invia dati ad Azure SQL Data Warehouse.

## Proprietà del tipo di set di dati Azure SQL Data Warehouse

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **AzureSqlDWTable** presenta le proprietà seguenti.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella nell'istanza del database SQL Data Warehouse di Azure a cui fa riferimento il servizio collegato. | Sì |

## Proprietà del tipo di attività di copia di Azure SQL Data Warehouse

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

> [AZURE.NOTE]
L'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

### SqlDWSource

In caso di origine di tipo **SqlDWSource**, nella sezione **typeProperties** sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Usare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare * da MyTable. | No |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. | Nome della stored procedure. | No |
| storedProcedureParameters | Parametri per la stored procedure. | Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |

Se la proprietà **sqlReaderQuery** è specificata per SqlDWSource, l'attività di copia esegue questa query nell'origine SQL Data Warehouse di Azure per ottenere i dati.

In alternativa, è possibile specificare una stored procedure specificando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura del set di dati JSON vengono usate per compilare una query da eseguire su Azure SQL Data Warehouse. Esempio: `select column1, column2 from mytable`. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

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
| writeBatchSize | Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. | Numero intero (numero di righe) | No (valore predefinito: 10000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. | Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). | No | 
| sqlWriterCleanupScript | Specificare una query da eseguire nell'attività di copia per pulire i dati di una sezione specifica. Per informazioni dettagliate, vedere la sezione relativa alla [ripetibilità](#repeatability-during-copy). | Istruzione di query. | No |
| allowPolyBase | Indica se usare PolyBase, quando applicabile, invece del meccanismo BULKINSERT per caricare i dati in Azure SQL Data Warehouse. <br/><br/>Attualmente, come set di dati di origine è supportato solo un set di dati **BLOB di Azure** con la proprietà **format** impostata su **TextFormat**. <br/><br/>Per informazioni su vincoli e dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse). | True <br/>False (valore predefinito) | No |  
| polyBaseSettings | Gruppo di proprietà che è possibile specificare quando la proprietà **allowPolybase** è impostata su **true**. | & nbsp; | No |  
| rejectValue | Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo. <br/><br/>Per altre informazioni sulle opzioni di rifiuto di PolyBase, vedere la sezione **Arguments** (Argomenti) in [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). | 0 (impostazione predefinita), 1, 2, … | No |  
| rejectType | Indica se l'opzione rejectValue viene specificata come valore letterale o come percentuale. | Value (impostazione predefinita), Percentage | No |   
| rejectSampleValue | Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate. | 1, 2, … | Sì se **rejectType** è **percentage** |  
| useTypeDefault | Specifica come gestire i valori mancanti nei file con testo delimitato quando PolyBase recupera dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). | True, False (valore predefinito) | No | 


#### Esempio SqlDWSink


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## Usare PolyBase per caricare dati in Azure SQL Data Warehouse
**PolyBase** consente di caricare in modo efficiente grandi quantità di dati in Azure SQL Data Warehouse con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito.

Impostare la proprietà **allowPolyBase** su **true**, come illustrato nell'esempio seguente per Azure Data Factory, per usare PolyBase per copiare i dati dall'archivio BLOB di Azure in Azure SQL Data Warehouse. Quando si imposta allowPolyBase su true, è possibile specificare proprietà specifiche di PolyBase usando il gruppo di proprietà **polyBaseSettings**. Per informazioni dettagliate sulle proprietà che è possibile usare con polyBaseSettings, vedere la sezione [SqlDWSink](#SqlDWSink).


    "sink": {
        "type": "SqlDWSink",
		"allowPolyBase": true,
		"polyBaseSettings":
		{
			"rejectType": "percentage",
			"rejectValue": 10.0,
			"rejectSampleValue": 100,
			"useTypeDefault": true 
		}

    }

### Copia diretta tramite PolyBase
Se i dati di origine soddisfano i criteri descritti in questa sezione, è possibile eseguire la copia direttamente dall'archivio dati di origine ad Azure SQL Data Warehouse con PolyBase. In caso contrario è possibile usare la [copia di staging tramite PolyBase](#staged-copy-using-polybase).

Se i requisiti non vengono soddisfatti, Azure Data Factory controlla le impostazioni e usa automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1.	Il **servizio collegato di origine** è di tipo **Archiviazione di Azure** e non è configurato per l'uso dell'autenticazione con firma di accesso condiviso. Per informazioni dettagliate, vedere [Servizio collegato Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).
2. Il **set di dati di input** è di tipo **BLOB di Azure** e il tipo di formato nelle proprietà del tipo è **OrcFormat** o **TextFormat** con le configurazioni seguenti:
	1. **rowDelimiter** deve essere **\\n**.
	2. **nullValue** è impostata su **stringa vuota** ("").
	3. **encodingName** è impostata su **utf-8**, ovvero il valore **predefinito**, e non deve essere impostata su un valore diverso.
	4. **escapeChar** e **quoteChar** non sono specificate.
	5. **Compression** non è **BZIP2**.
	 
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
4.	Non è presente alcuna impostazione **sliceIdentifierColumnName** in **SqlDWSink** per l'attività di copia nella pipeline. PolyBase garantisce che tutti i dati verranno aggiornati o che nessun dato verrà aggiornato in una singola esecuzione. Per ottenere la **ripetibilità**, è possibile usare **sqlWriterCleanupScript**.
5.	Nell'attività di copia associata non viene usato alcun valore **columnMapping**.

### Copia di staging tramite PolyBase
Quando l'origine dati non soddisfa i criteri presentati nella sezione precedente, è possibile abilitare la copia dei dati tramite un archivio BLOB di Azure di staging provvisorio. In questo caso, Azure Data Factory esegue trasformazioni sui dati in modo che soddisfino i requisiti di formato dei dati di PolyBase e quindi usa PolyBase per caricare i dati in SQL Data Warehouse. Per informazioni dettagliate sul funzionamento generale della copia dei dati tramite un BLOB di Azure di staging, vedere la sezione [Copia di staging](data-factory-copy-activity-performance.md#staged-copy).

> [AZURE.IMPORTANT] Se si copiano dati da un archivio dati locale ad Azure SQL Data Warehouse usando PolyBase e la copia di staging, installare JRE 8 (Java Runtime Environment) nel computer gateway per la trasformazione dei dati di origine nel formato corretto. Un gateway a 64 bit richiede JRE a 64 bit e un gateway a 32 bit richiede JRE a 32 bit. Scaricare la versione appropriata dalla pagina [Download di Java](http://go.microsoft.com/fwlink/?LinkId=808605).

Per usare questa funzionalità, creare un [servizio collegato Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) che faccia riferimento all'account di archiviazione di Azure contenente l'archivio BLOB provvisorio e quindi specificare le proprietà **enableStaging** e **stagingSettings** per l'attività di copia come illustrato nel codice seguente:

	"activities":[  
	{
		"name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
		"type": "Copy",
		"inputs": [{ "name": "OnpremisesSQLServerInput" }],
		"outputs": [{ "name": "AzureSQLDWOutput" }],
		"typeProperties": {
			"source": {
				"type": "SqlSource",
			},
			"sink": {
				"type": "SqlDwSink",
				"allowPolyBase": true
			},
    		"enableStaging": true,
			"stagingSettings": {
				"linkedServiceName": "MyStagingBlob"
			}
		}
	}
	]


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

Se viene visualizzato l'errore seguente, potrebbe essersi verificato un problema con il valore specificato per la proprietà tableName. Per informazioni sul modo corretto di specificare i valori per la proprietà JSON tableName, vedere la relativa tabella.

	Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### Colonne con valori predefiniti
La funzionalità PolyBase in Data Factory accetta attualmente lo stesso numero di colonne disponibili nella tabella di destinazione. Se si ha una tabella con quattro colonne di cui una definita con un valore predefinito, ad esempio, i dati di input dovranno comunque contenere quattro colonne. Se si specifica un set di dati di input con 3 colonne, si verificherà un errore simile al messaggio seguente:

	All columns of the table must be specified in the INSERT BULK statement.

Il valore NULL è una forma speciale di valore predefinito. Se la colonna ammette valori Null, i dati di input (nel BLOB) per tale colonna possono essere vuoti, ma non possono essere mancanti dal set di dati di input. PolyBase inserisce NULL per tali valori in Azure SQL Data Warehouse.


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per Azure SQL Data Warehouse

Come accennato nell'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipo automatiche dai tipi di origine ai tipi di sink con l'approccio in 2 passaggi seguente:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da e verso SQL Azure, SQL Server, Sybase sono usati i mapping seguenti dal tipo SQL di tipo .NET e viceversa.

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

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0921_2016-->