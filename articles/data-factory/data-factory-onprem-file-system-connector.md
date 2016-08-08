<properties 
	pageTitle="Spostare i dati da e verso il file system | Azure Data Factory" 
	description="Informazioni su come spostare i dati da e verso il file system locale usando Azure Data Factory" 
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
	ms.date="07/13/2016" 
	ms.author="spelluru"/>

# Spostare i dati da e nel file system locale usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in una data factory per spostare dati da e verso un file system locale. Per un elenco di archivi dati che possono essere usati come origini o sink con il file system locale, vedere [Archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores). Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Data Factory supporta la connessione da e verso il file system locale tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento dei dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate su come configurare il gateway.

> [AZURE.NOTE] 
Tranne il Gateway di gestione dati, non è necessario installare altri file binari per la comunicazione da e verso il file system locale.
> 
> Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere la sezione [Risoluzione dei problemi di gateway](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting).

## Condivisione file Linux 

Eseguire i due passaggi seguenti per usare una condivisione di file Linux con il servizio collegato del file server:

- Installare [Samba](https://www.samba.org/) sul server Linux.
- Installare e configurare il Gateway di gestione dati su un server Windows. L'installazione di un gateway su un server Linux non è supportata.
 
## Esempio: Copiare i dati da un file system locale a un BLOB di Azure

Questo esempio illustra come copiare dati da un file system locale a un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in uno qualsiasi dei sink indicati [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.
 
L'esempio include le entità di Data Factory seguenti:

- Un servizio collegato di tipo [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
- Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [set di dati](data-factory-create-datasets.md) di input di tipo [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
- Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio seguente copia i dati appartenenti a una serie temporale dal file system locale a BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Per prima cosa, impostare il Gateway di gestione dati seguendo le istruzioni contenute nell'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Servizio collegato del file server locale:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Per l'host, è possibile specificare **Local** o **localhost** se la condivisione file è nel computer gateway stesso. Si consiglia di usare la proprietà **encryptedCredential** anziché le proprietà **userid** e **password**. Per informazioni dettagliate su questo servizio collegato, vedere l'articolo relativo al [servizio collegato del file system](#onpremisesfileserver-linked-service-properties).

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

Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Azure Data Factory che la tabella è esterna e non è prodotta da un'attività al suo interno.

	{
	  "name": "OnpremisesFileSystemInput",
	  "properties": {
	    "type": " FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

##Esempio: Copiare i dati da Azure SQL Azure in un file system locale 

L'esempio seguente mostra:

- Un servizio collegato di tipo AzureSqlDatabase.
- Un servizio collegato di tipo OnPremisesFileServer.
- Un set di dati di input di tipo AzureSqlTable.
- Un set di dati di output di tipo FileShare.
- La pipeline con attività di copia che usa SqlSource e FileSystemSink.

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
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Per l'host, è possibile specificare **Local** o **localhost** se la condivisione file è nel computer gateway stesso. Si consiglia di usare la proprietà **encryptedCredential** anziché le proprietà **userid** e **password**. Per informazioni dettagliate su questo servizio collegato, vedere l'articolo relativo al [servizio collegato del file system](#onpremisesfileserver-linked-service-properties).

**Set di dati di input SQL Azure:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Azure e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Data Factory che la tabella è esterna e non è prodotta da un'attività al suo interno.

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
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Pipeline con un'attività di copia:** la pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **SqlSource** e il tipo **sink** è impostato su **FileSystemSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

	
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
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
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
host | Percorso radice della cartella che si desidera copiare. Usare il carattere di escape "\" per i caratteri speciali nella stringa. Per esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions). | Sì
userid | Specificare l'ID dell'utente che ha accesso al server | No (se si sceglie encryptedCredential)
password | Specificare la password per l'utente (userid) | No (se si sceglie encryptedCredential) 
encryptedCredential | Specifica le credenziali crittografate che è possibile ottenere eseguendo il cmdlet New-AzureRmDataFactoryEncryptValue<br/><br/>**Nota:** è necessario Azure PowerShell 0.8.14 o versione successiva per usare cmdlet come New-AzureRmDataFactoryEncryptValue con il parametro type impostato su OnPremisesFileSystemLinkedService | No (se si sceglie di specificare ID utente e password in testo normale)
gatewayName | Nome del gateway che il servizio Data Factory deve usare per connettersi al database del file server locale | Sì

Per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati file system locale, vedere [Impostazione delle credenziali e della sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

### Servizio collegato di esempio e definizioni del set di dati 
Scenario | Host nella definizione del servizio collegato | folderPath nella definizione del set di dati
-------- | --------------------------------- | --------------------- |
Cartella locale nel computer del gateway di gestione dati: <br/><br/>ad esempio D:\\* o D:\\cartella\\sottocartella\\* | D:\\\ (per versioni del gateway 2.0 e successive) <br/><br/> localhost (per versioni del gateway precedenti a 2.0) | .\\\ o cartella\\\sottocartella (per versioni del gateway 2.0 e successive) <br/><br/>D:\\\ o D:\\\cartella\\\sottocartella (per versioni del gateway precedenti a 2.0)
Cartella condivisa remota: <br/><br/>ad esempio. \\\myserver\\share\\* o \\\myserver\\share\\cartella\\sottocartella\\* | \\\\\\myserver\\\share | .\\\ o cartella\\\sottocartella

È possibile trovare la **versione** del gateway installato avviando [Gestione configurazione di Gateway di gestione dati](data-factory-data-management-gateway.md#data-management-gateway-configuration-manager) sul computer in uso e passando alla scheda **?**.

> [AZURE.NOTE] Per lo scenario della cartella locale, specificando la proprietà "host" come "localhost", l'esecuzione dell'attività di copia funzionerà con qualsiasi versione del gateway, ma è possibile usare la procedura guidata per l'impostazione. Per usare questo scenario, è consigliabile [aggiornare il gateway alla versione 2.0 o successiva](data-factory-data-management-gateway.md#update-data-management-gateway) al fine di usare le nuove configurazioni indicate in precedenza tramite JSON e la procedura guidata per la copia.

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure, file system locale e così via).

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione, il formato dei dati e così via nell'archivio dati. La sezione typeProperties per il set di dati di tipo **FileShare** presenta le proprietà seguenti.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
folderPath | Sottopercorso alla cartella. Usare il carattere di escape "\" per i caratteri speciali nella stringa. Per esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions).<br/><br/>È possibile combinare il servizio con **partitionBy** per ottenere percorsi alla cartella basati su data e ora di inizio/fine delle sezioni. | Sì
fileName | Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato ha il formato seguente: <br/><br/>Data.<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | No
partitionedBy | partitionedBy può essere usato per specificare un valore folderPath dinamico, filename per i dati di una serie temporale. Ad esempio, folderPath con parametri per ogni ora di dati. | No
Format | Sono supportati i tipi di formato seguenti: **TextFormat**, **AvroFormat**, **JsonFormat** e **OrcFormat**. La proprietà **type** in format deve essere impostata su uno di tali valori. Per informazioni dettagliate, vedere le sezioni [Specifica di TextFormat](#specifying-textformat), [Specifica di AvroFormat](#specifying-avroformat), [Specifica di JsonFormat](#specifying-jsonformat) e [Specifica di OrcFormat](#specifying-orcformat). Se si desidera copiare i file così come sono tra archivi basati su file (copia binaria), è possibile saltare la sezione formato sia nella definizione del set di dati di output che in quella di input. | No
fileFilter | Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file. <br/><br/>I valori consentiti sono: * (più caratteri) e ? (carattere singolo).<br/><br/>Esempio 1: "fileFilter": "*.log"<br/>Esempio 2: "fileFilter": 2014-1-?.txt"<br/><br/>**Nota:** fileFilter è applicabile per un set di dati di input FileShare. | No
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono: **GZip**, **Deflate** e **BZip2** e i livelli supportati sono: **Ottimale** e **Più veloce**. Si noti che le impostazioni di compressione non sono attualmente supportate per i dati **AvroFormat** o **OrcFormat**. Per altre informazioni, vedere la sezione [Supporto della compressione](#compression-support). | No |

> [AZURE.NOTE] filename e fileFilter non possono essere usati contemporaneamente.

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

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]  
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Proprietà del tipo di attività di copia di una condivisione file

**FileSystemSource** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. | True, False (valore predefinito)| No | 

**FileSystemSink** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Definisce il comportamento di copia quando l'origine è BlobSource o FileSystem. | **PreserveHierarchy:** mantiene la gerarchia dei file nella cartella di destinazione. In altri termini, il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/>**FlattenHierarchy:** tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I file di destinazione hanno un nome generato automaticamente.<br/><br/>**MergeFiles:** unisce tutti i file della cartella di origine in un file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente. | No |

### esempi ricorsivi e copyBehavior
In questa sezione viene descritto il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

ricorsiva | copyBehavior | Comportamento risultante
--------- | ------------ | --------
true | preserveHierarchy | Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 avrà la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5  
true | flattenHierarchy | Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 avrà la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File5
true | mergeFiles | Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 avrà la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il contenuto di File1 + File2 + File3 + File4 + File 5 verrà unito in un file con nome del file generato automaticamente
false | preserveHierarchy | Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 avrà la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati.
false | flattenHierarchy | Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 avrà la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati.
false | mergeFiles | Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 avrà la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il contenuto di File1 + File2 verrà unito in un file con nome del file generato automaticamente. Nome generato automaticamente per File1<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati.


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).






 

<!---HONumber=AcomDC_0727_2016-->