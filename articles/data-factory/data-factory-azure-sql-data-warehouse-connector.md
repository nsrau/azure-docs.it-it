<properties 
	pageTitle="Spostare dati da e verso Azure SQL Data Warehouse | Data factory di Azure" 
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
	ms.date="09/29/2015" 
	ms.author="spelluru"/>

# Spostare dati da e verso Azure SQL Data Warehouse mediante Data factory di Azure

Questo articolo illustra come usare l'attività di copia in una data factory per spostare dati in Azure SQL Data Warehouse da un altro archivio dati e spostare dati da un altro archivio dati a SQL Azure. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

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
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Proprietà del servizio collegato di Azure SQL Data Warehouse

La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di Azure SQL Data Warehouse

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà del tipo deve essere impostata su: **AzureSqlDW** | Sì
**connectionString** | Specificare le informazioni necessarie per connettersi all'istanza di Azure SQL Data Warehouse per la proprietà connectionString. | Sì

Nota: è necessario configurare il [firewall del database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). È necessario configurare il server del database per [consentire ai servizi di Azure di accedere al server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Inoltre, se si copiano dati in Azure SQL Data Warehouse dall’esterno di Azure e da origini dati locali con gateway di data factory, sarà necessario configurare un intervallo di indirizzi IP appropriato per la macchina che sta inviando dati a Azure SQL Data Warehouse.

## Proprietà del tipo del set di dati Azure SQL Data Warehouse

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
| sqlReaderQuery | Usare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare * da MyTable. Se non specificato, l'istruzione SQL eseguita: selezionare **colonne definite nella sezione della struttura della tabella JSON** da MyTable. | No |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. | Nome della stored procedure. | No |
| sqlReaderStoredProcedureParameters | Parametri per la stored procedure. | Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |

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
| sliceIdentifierColumnName | Nome di colonna specificato dall'utente per l'attività di copia da riempire con l'identificatore di sezione generato automaticamente, che verrà usato per eliminare i dati di una sezione specifica quando viene nuovamente eseguita. Vedere di seguito la sezione ripetibilità per ulteriori dettagli. | Nome di colonna di una colonna con tipo di dati binario (32). | No |

#### Esempio SqlDWSink


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
    }


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per Azure SQL Data Warehouse

Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

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

<!---HONumber=Oct15_HO1-->