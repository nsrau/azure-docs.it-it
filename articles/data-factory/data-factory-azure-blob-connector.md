<properties 
	pageTitle="Copiare dati in e da un archivio BLOB di Azure | Azure Data Factory" 
	description="Informazioni su come copiare dati BLOB in Azure Data Factory. Usare l'esempio: Come copiare dati da e verso l'archivio BLOB di Azure e il database SQL di Azure." 
    keywords="dati blob, copia di blob di azure"
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
	ms.date="08/25/2016" 
	ms.author="spelluru"/>  

# Spostare dati da e verso il BLOB di Azure mediante Azure Data Factory
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare dati da e verso BLOB di Azure acquisendo i dati BLOB da un altro archivio dati. Questo articolo si basa sull'articolo relativo alle attività di spostamento dei dati, che offre una panoramica generale dello spostamento dei dati con l'attività di copia e delle combinazioni di archivio dati supportate.

> [AZURE.NOTE] Questo connettore Blob di Azure attualmente supporta solo la copia da e verso i BLOB in blocchi. Supporta sia l'archiviazione di Azure per scopi generici sia gli archivi BLOB ad accesso frequente e sporadico.

## Copia di dati guidata
Il modo più semplice di creare una pipeline in grado di copiare i dati da/in Archiviazione BLOB di Azure consiste nell’utilizzare la procedura Copia di dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tali esempi mostrano come copiare dati da/in Archiviazione BLOB di Azure e Database SQL Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.

## Esempio: Copiare i dati dal BLOB di Azure in SQL Azure
 

L'esempio seguente mostra:

1.	Un servizio collegato di tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md)di output di tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](#azure-blob-copy-activity-type-properties) e [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

L'esempio copia i dati di una serie temporale da un BLOB di Azure in una tabella di Azure SQL ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato SQL di Azure:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Servizio collegato Archiviazione di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Azure Data Factory supporta due tipi di servizi collegati di Archiviazione di Azure, **AzureStorage** e **AzureStorageSas**. Per il primo specificare la stringa di connessione che include la chiave dell'account e per il secondo specificare l'URI di firma di accesso condiviso. Per informazioni dettagliate, vedere la sezione [Servizi collegati](#linked-services).

**Set di dati di input del BLOB di Azure:**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, e giorno dell'ora di inizio e il nome del file usa la parte dell'ora di inizio relativa all'ora. L'impostazione di "external" su "true" comunica a Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

**Set di dati di output SQL Azure:**

L'esempio copia i dati in una tabella denominata "MyTable" in un database SQL Azure. Creare la tabella nel database SQL di Azure con lo stesso numero di colonne di quelle previste nel file CSV del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline con un’attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **BlobSource** e il tipo di **sink** è impostato su **SqlSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink"
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

## Esempio: Copiare i dati da SQL Azure al BLOB di Azure
L'esempio seguente mostra:

1.	Un servizio collegato di tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) e [BlobSink](#azure-blob-copy-activity-type-properties).


L'esempio copia i dati di una serie temporale da una tabella di Azure SQL in un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato SQL di Azure:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Servizio collegato Archiviazione di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Azure Data Factory supporta due tipi di servizi collegati di Archiviazione di Azure, **AzureStorage** e **AzureStorageSas**. Per il primo specificare la stringa di connessione che include la chiave dell'account e per il secondo specificare l'URI di firma di accesso condiviso. Per informazioni dettagliate, vedere la sezione [Servizi collegati](#linked-services).


**Set di dati di input SQL Azure:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Azure e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" si comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
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
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

**Pipeline con l’attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **SqlSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.


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
		    	        "name": "AzureSQLInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "SqlSource",
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

## Servizi collegati
Esistono due tipi di servizi collegati, che consentono di collegare un archivio BLOB di Azure a una data factory di Azure. I due tipi di servizi sono il servizio collegato **AzureStorage** e il servizio collegato **AzureStorageSas**. Il servizio collegato Archiviazione di Azure garantisce alla data factory l'accesso globale ad Archiviazione di Azure. Invece il servizio collegato Firma di accesso condiviso di Archiviazione di Azure garantisce alla data factory l'accesso limitato o a scadenza ad Archiviazione di Azure. Non esistono altre differenze tra questi due servizi collegati. Scegliere il servizio collegato più adatto alle proprie esigenze. Le sezioni seguenti forniscono altri dettagli su questi due servizi collegati.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## Proprietà type del set di dati BLOB di Azure

Per un elenco completo delle proprietà e delle sezioni JSON disponibili per la definizione dei set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione, il formato dei dati e così via nell'archivio dati. La sezione typeProperties per il set di dati di tipo **AzureBlob** presenta le proprietà seguenti.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| folderPath | Percorso del contenitore e della cartella nell'archivio BLOB. Esempio: myblobcontainer\\myblobfolder\\ | Sì |
| fileName | Nome del BLOB. La proprietà fileName è facoltativa e fa distinzione tra maiuscole e minuscole.<br/><br/>Se si specifica un nome file, l'attività (inclusa la copia) funziona sul BLOB specifico.<br/><br/>Quando la proprietà fileName non è specificata, la copia include tutti i BLOB nella proprietà folderPath per il set di dati di input.<br/><br/>Quando la proprietà fileName non è specificata per un set di dati di output, il nome del file generato sarà nel formato seguente: Data.<Guid>.txt. Ad esempio, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | No |
| partitionedBy | partitionedBy è una proprietà facoltativa. Può essere utilizzata per specificare una proprietà folderPath dinamica e un nome file per i dati della serie temporale. Ad esempio, è possibile includere parametri per ogni ora di dati in folderPath. Per informazioni dettagliate ed esempi, vedere la sezione [Uso della proprietà partitionedBy](#using-partitionedBy-property). | No
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **AvroFormat**, **JsonFormat** e **OrcFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per informazioni dettagliate, vedere le sezioni [Specifica di TextFormat](#specifying-textformat), [Specifica di AvroFormat](#specifying-avroformat), [Specifica di JsonFormat](#specifying-jsonformat) e [Specifica di OrcFormat](#specifying-orcformat). Se si desidera copiare i file così come sono tra archivi basati su file (copia binaria), è possibile saltare la sezione formato sia nella definizione del set di dati di output che in quella di input.| No
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono: **GZip**, **Deflate** e **BZip2** e i livelli supportati sono: **Ottimale** e **Più veloce**. Le impostazioni di compressione non sono attualmente supportate per i dati **AvroFormat** o **OrcFormat**. Per altre informazioni, vedere la sezione [Supporto della compressione](#compression-support). | No |

### Uso della proprietà partitionedBy
Come indicato nella sezione precedente, è possibile specificare una proprietà folderPath dinamica e il nome file per i dati di una serie temporale con la sezione **partitionedBy**, macro Data factory e variabili di sistema, SliceStart e SliceEnd, che indicano l'ora di inizio e di fine per una sezione dati specificata.

Vedere [Variabili di sistema Data factory](data-factory-scheduling-and-execution.md#data-factory-system-variables) e [Riferimento alle funzioni di Data fctory](data-factory-scheduling-and-execution.md#data-factory-functions-reference) per scoprire le variabili di sistema di Data factory e funzioni che è possibile utilizzare nella sezione partitionedBy.

Per altre informazioni sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli [Set di dati in Azure Data Factory](data-factory-create-datasets.md) e [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md).

#### Esempio 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato (AAAAMMGGHH) specificato. SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. For example: wikidatagateway/wikisampledataout/2014100103 or wikidatagateway/wikisampledataout/2014100104

#### Esempio 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate che vengono usate dalle proprietà folderPath e fileName.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]  
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## Proprietà type di attività di copia BLOB di Azure  
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Proprietà quali nome, descrizione, criteri e set di dati di input e di output sono disponibili per tutti i tipi di attività.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

**BlobSource** supporta le seguenti proprietà della sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- | 
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. | True (valore predefinito), False | No | 

**BlobSink** supporta le proprietà della sezione **typeProperties** seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Definisce il comportamento di copia quando l'origine è BlobSource o FileSystem. | **PreserveHierarchy:** mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/>**FlattenHierarchy:** tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente. <br/><br/>**MergeFiles: (valore predefinito)** unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente. | No |

**BlobSource** supporta anche le due proprietà seguenti, che verranno presto deprecate.

- **treatEmptyAsNull**: specifica se considerare una stringa vuota o Null come valore Null.
- **skipHeaderLineCount**: specifica il numero di righe che devono essere ignorate. È applicabile solo quando il set di dati di input usa TextFormat.

Analogamente, **BlobSink** supporta la proprietà seguente, che verrà presto deprecata.

- **blobWriterAddHeader**: specifica se aggiungere un'intestazione di definizioni di colonna durante la scrittura di un set di dati di output.

Ora i set di dati supportano le proprietà seguenti che implementano la stessa funzionalità: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

La tabella seguente vengono fornisce indicazioni sull'uso delle nuove proprietà del set di dati al posto delle proprietà del sink o dell'origine BLOB che verranno presto deprecate.

| Proprietà dell'attività di copia | Proprietà del set di dati |
| :---------------------- | :---------------- | 
| skipHeaderLineCount in BlobSource | skipLineCount e firstRowAsHeader. Le righe vengono prima ignorate e poi la prima riga viene letta come intestazione. |
| treatEmptyAsNull in BlobSource | treatEmptyAsNull nel set di dati di input |
| blobWriterAddHeader in BlobSink | firstRowAsHeader nel set di dati di output | 

Per informazioni dettagliate su queste proprietà, vedere la sezione [Specifica di TextFormat](#specifying-textformat).

### esempi ricorsivi e copyBehavior
In questa sezione viene descritto il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

ricorsiva | copyBehavior | Comportamento risultante
--------- | ------------ | --------
true | preserveHierarchy | Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la stessa struttura dell'origine: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5  
true | flattenHierarchy | Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File5
true | mergeFiles | Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome file generato automaticamente
false | preserveHierarchy | Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Sottocartella1 con File3, File4 e File5 non considerati
false | flattenHierarchy | Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/><br/><br/>Sottocartella1 con File3, File4 e File5 non considerati
false | mergeFiles | Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il contenuto di File1 + File2 viene unito in un file con un nome file generato automaticamente. Nome generato automaticamente per File1<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati

  


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0831_2016-->