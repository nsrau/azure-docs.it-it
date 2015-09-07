<properties 
	pageTitle="Spostare i dati da e verso il file system | Data factory di Azure"
	description="Informazioni su come spostare i dati da e verso il file system locale usando Data factory di Azure"
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
	ms.date="08/26/2015"
	ms.author="spelluru"/>

# Spostare i dati da e verso il file system locale usando Data factory di Azure

Questo articolo illustra come usare l'attività di copia in una data factory per spostare dati da e verso un file system locale. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Data factory supporta la connessione da e verso il file system locale tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento dei dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate su come configurare il gateway.

**Nota:** tranne il Gateway di gestione dati, non è necessario installare altri file binari per la comunicazione da e verso il file system locale.

## Condivisione file Linux 

Eseguire i due passaggi seguenti per usare una condivisione di file Linux con il servizio collegato del file server:

- Installare [Samba](https://www.samba.org/) sul server Linux.
- Installare e configurare il Gateway di gestione dati su un server Windows. L'installazione di un gateway su un server Linux non è supportata. 
 
## Esempio: Copiare i dati da un file system locale a BLOB di Azure

L'esempio seguente mostra:

1.	Un servizio collegato di tipo [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

L'esempio seguente copia i dati appartenenti a una serie temporale dal file system locale a BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Innanzitutto, impostare il Gateway di gestione dati in base alle istruzioni contenute nell'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Servizio collegato del file server locale:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
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

**Set di dati di input del file system locale:**

I dati vengono prelevati da un nuovo file ogni ora e il percorso e il nome file riflettono la data e l'ora specifiche con granularità oraria.

Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Data factory di Azure che la tabella è esterna e non è prodotta da un'attività al suo interno.

	{
	  "name": "OnpremisesFileSystemInput",
	  "properties": {
	    "type": " FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
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
	      ]
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
	            "format": "%HH"
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

**Attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **FileSystemSource** e il tipo di **sink** è impostato su **BlobSink**.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T19:00:00",
	    "description":"Pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "OnpremisesFileSystemtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "OnpremisesFileSystemInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "FileSystemSource"
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

##Esempio: Copiare i dati da SQL Azure a un file system locale 

L'esempio seguente mostra:

1.	Un servizio collegato di tipo AzureSqlDatabase.
2.	Un servizio collegato di tipo OnPremisesFileServer.
3.	Un set di dati di input di tipo AzureSqlTable. 
3.	Un set di dati di output di tipo FileShare.
4.	La pipeline con attività di copia che usa SqlSource e FileSystemSink.

L'esempio copia i dati appartenenti a una serie temporale da una tabella nel database SQL di Azure in un file system locale ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

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

**Servizio collegato del file server locale:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

**Set di dati di input SQL Azure:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Azure e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Data factory che la tabella è esterna e non è prodotta da un'attività al suo interno.

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

**Set di dati di output del file system locale:**

I dati vengono copiati in un nuovo file ogni ora e il percorso per il BLOB riflette la data e l'ora specifiche con granularità oraria.

	{
	  "name": "OnpremisesFileSystemOutput",
	  "properties": {
	    "type": "FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
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
	            "format": "%HH"
	          }
	        }
	      ]
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

**Pipeline con un'attività di copia:** la pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **SqlSource** e il tipo di **sink** è impostato su **FileSystemSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T20:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoOnPremisesFile",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OnpremisesFileSystemOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \'{0:yyyy-MM-dd}\' AND timestampcolumn < \'{1:yyyy-MM-dd}\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "FileSystemSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 3,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

## Proprietà del servizio collegato OnPremisesFileServer

È possibile collegare un file system locale a una data factory di Azure con il servizio collegato del file server locale. La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato del file server locale.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà type deve essere impostata su **OnPremisesFileServer**. | Sì 
host | Nome host del server. Usare ' \\ ' come carattere di escape, come nell'esempio seguente: se la condivisione è: \\servername, specificare \\\servername.<p>Se il file system è locale rispetto al computer gateway, usare Local o localhost. Se il file system si trova su un server diverso dal computer gateway, usare \\\servername.</p> | Sì
userid | Specificare l'ID dell'utente che ha accesso al server | No (se si sceglie encryptedCredential)
password | Specificare la password per l'utente (userid) | No (se si sceglie encryptedCredential) 
encryptedCredential | Specificare le credenziali crittografate che è possibile ottenere eseguendo il cmdlet New-AzureDataFactoryEncryptValue<p>**Nota:** è necessario usare Azure PowerShell 0.8.14 o versione successiva per usare cmdlet come New-AzureDataFactoryEncryptValue con il parametro type impostato su OnPremisesFileSystemLinkedService</p> | No (se si sceglie di specificare ID utente e password in testo normale)
gatewayName | Nome del gateway che il servizio Data factory deve usare per connettersi al database del file server locale | Sì

Per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati File System locale, vedere [Impostazione delle credenziali e della sicurezza](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).

**Esempio: Uso di nome utente e password in testo normale**
	
	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}
	
**Esempio: Uso di encryptedcredential**

	{
	  "Name": " OnPremisesFileServerLinkedService ",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "localhost",
	      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
	      "gatewayName": "mygateway"
	    }
	  }
	}

## Proprietà del tipo del set di dati del file system locale

Per un elenco completo delle proprietà e delle sezioni disponibili per la definizione dei set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure, file system locale e così via).

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione, il formato dei dati e così via nell'archivio dati. La sezione typeProperties per il set di dati di tipo **FileShare** presenta le proprietà seguenti.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
folderPath | Percorso della cartella. Esempio: myfolder<p>Usare il carattere di escape ' \\ ' per i caratteri speciali nella stringa. Ad esempio: per folder\\subfolder, specificare folder\\subfolder e per d:\\samplefolder, specificare d:\\samplefolder.</p><p>È possibile unire questi percorsi a **partitionBy** per ottenere percorsi delle cartelle basati sull'intervallo di data di inizio e fine della sezione.</p> | Sì
fileName | Specificare il nome del file in **folderPath** se si desidera che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella fa riferimento a tutti i file nella cartella.<p>Quando fileName non è specificato per un set di dati di output, il nome del file generato è nel formato seguente:</p><p>Data.<Guid>.txt (ad esempio: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p>) | No
partitionedBy | partitionedBy può essere usato per specificare un valore folderPath dinamico, filename per i dati di una serie temporale. Ad esempio, folderPath con parametri per ogni ora di dati. | No
Format | Sono supportati due tipi di formati: **TextFormat** e **AvroFormat**. È necessario impostare la proprietà type nel formato su uno di questi due valori. Quando forAvroFormat corrisponde a TextFormat, è possibile specificare proprietà facoltative per il formato. Vedere la sezione relativa al formato di seguito per ulteriori dettagli. | No
fileFilter | Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file. <p>I valori consentiti sono: * (più caratteri) e ? (carattere singolo).</p><p>Esempio 1: "fileFilter": "*.log"</p>Esempio 2: "fileFilter": 2014-1-?.txt"</p><p>**Nota**: fileFilter è applicabile per un set di dati di input FileShare</p> | No

> [AZURE.NOTE]filename e fileFilter non possono essere usati contemporaneamente.

### Uso della proprietà partionedBy

Come indicato sopra, partitionedBy può essere usato per specificare un valore folderPath dinamico, filename per i dati di una serie temporale. È possibile eseguire questa operazione con le macro della data factory e le variabili di sistema SliceStart, SliceEnd, che indicano il periodo di tempo logico per una sezione di dati specificata.

Per altri dettagli sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli [Set di dati](data-factory-create-datasets.md), [Pianificazione ed esecuzione con Data factory](data-factory-scheduling-and-execution.md) e [Informazioni su pipeline e attività](data-factory-create-pipelines.md).

#### Esempio 1.

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

Nell'esempio precedente, {Slice} viene sostituito con il valore della variabile di sistema SliceStart della data factory nel formato (AAAAMMGGHH) specificato. SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. Ad esempio: wikisampledataout/wikidatagateway/2014100103 o wikisampledataout/wikidatagateway/2014100104.

#### Esempio 2:

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

Nell'esempio precedente, anno, mese, giorno e ora di SliceStart vengono estratti in variabili separate che vengono usate dalle proprietà folderPath e fileName.

### Specifica di TextFormat

Se il formato è impostato su **TextFormat**, è possibile specificare le proprietà **facoltative** seguenti nella sezione **Format** all'interno della sezione **typeProperties**.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
columnDelimiter | Carattere/i usato/i come separatore di colonne in un file. Il valore predefinito è la virgola (,). | No
rowDelimiter | Carattere/i usato/i come separatore raw in un file. Il valore predefinito è uno dei seguenti: ["\\r\\n", "\\r"," \\n"]. | No
escapeChar | Carattere speciale usato per eseguire l'escape di un delimitatore di colonna visualizzato nel contenuto. Nessun valore predefinito. Per questa proprietà è necessario specificare non più di un carattere.<p>Ad esempio, se è presente una virgola (,) come delimitatore di colonna, ma si desidera inserire un carattere virgola nel testo (ad esempio: "Hello, world"), è possibile definire '$' come carattere di escape e usare la stringa "Hello$, world" nell'origine.</p><p>Si noti che non è possibile specificare sia escapeChar che quoteChar per una tabella.</p> | No
quoteChar | Carattere speciale usato per inserire il valore della stringa tra virgolette. I delimitatori di colonne e righe tra virgolette vengono considerati come parte del valore stringa. Nessun valore predefinito. Per questa proprietà è necessario specificare non più di un carattere .<p>Ad esempio, se è presente una virgola (,) come delimitatore di colonna, ma si desidera inserire un carattere virgola nel testo (ad esempio: <Hello  world>), è possibile definire '"' come carattere virgolette e usare la stringa <"Hello, world"> nell'origine. Questa proprietà è applicabile sia alle tabelle di input che a quelle di output.</p><p>Si noti che non è possibile specificare sia escapeChar che quoteChar per una tabella.</p> | No
nullValue | Carattere/i usato/i per rappresentare un valore null nel contenuto del file BLOB. Il valore predefinito è "\\N".> | No
encodingName | Specificare il nome della codifica. Per l'elenco di nomi di codifica validi, vedere: Proprietà Encoding.EncodingName. <p>Ad esempio: windows-1250 o shift\_jis. Il valore predefinito è UTF-8.</p> | No

#### Esempi:

L'esempio seguente illustra alcune delle proprietà del formato per **TextFormat**.

	"typeProperties":
	{
	    "folderPath": "MyFolder",
	    "fileName": "MyFileName"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

Per usare un escapeChar anziché un quoteChar, sostituire la riga con quoteChar con la stringa seguente:

	"escapeChar": "$",

### Specifica di AvroFormat

Se il formato è impostato su **AvroFormat**, non è necessario specificare proprietà nella sezione Format all'interno della sezione typeProperties. Esempio:

	"format":
	{
	    "type": "AvroFormat",
	}
	
Per usare il formato Avro in una tabella Hive successiva, fare riferimento all'[esercitazione su Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

## Proprietà del tipo per l'attività di copia di una condivisione file

**FileSystemSource** e **FileSystemSink** attualmente non supportano alcuna proprietà.

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]








 

<!---HONumber=August15_HO9-->