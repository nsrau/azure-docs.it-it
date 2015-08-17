<properties 
	pageTitle="Connettore Oracle - Spostare i dati da e verso Oracle" 
	description="Informazioni sul connettore Oracle per il servizio Data factory che consente di spostare i dati da e verso il database Oracle locale." 
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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Connettore Oracle - Spostare i dati sul database Oracle locale 

Questo articolo illustra come usare l'attività di copia della data factory per spostare dati da Oracle a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

## Esempio: Copiare i dati da Oracle a BLOB di Azure

L'esempio seguente mostra:

1.	Un servizio collegato di tipo OnPremisesOracle.
2.	Un servizio collegato di tipo AzureStorage.
3.	Un set di dati di input di tipo OracleTable. 
4.	Un set di dati di output di tipo AzureBlob.
5.	La pipeline con attività di copia che usa OracleSource come origine e BlobSink come sink.

L'esempio copia i dati da una tabella nel database Oracle locale a un BLOB ogni ora. Per altre informazioni sulle varie proprietà usate nell'esempio riportato di seguito, fare riferimento alla documentazione sulle diverse proprietà nelle sezioni che seguono gli esempi.

**Servizio collegato Oracle:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Servizio collegato di archiviazione BLOB di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Set di dati di input Oracle:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in Oracle e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" e specificando i criteri externalData si comunica alla data factory che la tabella è esterna e non è prodotta da un'attività al suo interno.

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
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

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.
	
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

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **RelationalSource** e il tipo **sink** è impostato su **BlobSink**. La query SQL specificata con la proprietà **oracleReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Proprietà del servizio collegato Oracle

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato Oracle.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà del tipo deve essere impostata su: **OnPremisesOracle** | Sì
connectionString | Specificare le informazioni necessarie per connettersi all'istanza del database Oracle per la proprietà connectionString. | Sì 
gatewayName | Nome del gateway che verrà usato per connettersi al server Oracle locale | Sì

## Proprietà del tipo del set di dati Oracle

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, fare riferimento all'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (Oracle, BLOB di Azure, tabelle di Azure e così via).
 
La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo OracleTable presenta le proprietà seguenti.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
tableName | Nome della tabella nell'istanza del database Oracle a cui fa riferimento il servizio collegato. | Sì

## Proprietà del tipo di attività di copia Oracle

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.

**Nota:** l'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

In caso di attività di copia con origine di tipo SqlSource, sono disponibili le proprietà seguenti nella sezione typeProperties:

Proprietà | Descrizione |Valori consentiti | Obbligatorio
-------- | ----------- | ------------- | --------
oracleReaderQuery | Usare la query personalizzata per leggere i dati. | Stringa di query SQL. 
Ad esempio: selezionare * da MyTable <p>Se non specificata, l'istruzione SQL eseguita: selezionare * da MyTable</p> | No

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per Oracle

Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da Oracle, verranno usati i mapping seguenti dal tipo di dati Oracle al tipo .NET e viceversa.

Tipo di dati Oracle | Tipo di dati .NET Framework
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | String
CLOB | String
DATE | DateTime
FLOAT | Decimal
INTEGER | Decimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | Decimal
NVARCHAR2 | String
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Number
VARCHAR2 | String
XML | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=August15_HO6-->