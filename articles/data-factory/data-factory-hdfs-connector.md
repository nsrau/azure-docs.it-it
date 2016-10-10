<properties 
	pageTitle="Spostare dati da HDFS locale | Azure Data Factory" 
	description="Informazioni su come spostare dati da un HDFS locale con Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="linda33wj" 
	manager="jhubbard" 
	editor="monicar"/>  

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/06/2016" 
	ms.author="jingwang"/>

# Spostare dati da HDFS locale con Azure Data Factory
Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati da un HDFS locale a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Data Factory supporta attualmente solo lo spostamento di dati da un HDFS ad altri archivi dati, ma non da altri archivi dati a un HDFS locale.


## Abilitazione della connettività
Il servizio Data Factory supporta la connessione a un HDFS locale tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway. Usare il gateway per connettersi a HDFS anche se è ospitato in una VM IaaS di Azure.

Anche se è possibile installare il gateway nello stesso computer locale o nella stessa VM di Azure di HDFS, è consigliabile installarlo in un diverso computer o in una diversa VM IaaS di Azure. La presenza del gateway su un computer separato riduce i conflitti di risorse e consente di ottenere prestazioni migliori. Quando si installa il gateway in un computer separato, questo deve poter accedere al computer con un HDFS.


## Copia di dati guidata
Il modo più semplice di creare una pipeline che copia i dati da HDFS locale consiste nell'usare la procedura Copia di dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da un HDFS locale in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.

## Esempio: Copiare i dati da HDFS locale al BLOB di Azure

Questo esempio illustra come copiare dati da HDFS locale all'archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.
 
L'esempio include le entità di Data Factory seguenti:

1.	Un servizio collegato di tipo [OnPremisesHdfs](#hdfs-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [FileShare](#hdfs-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](#hdfs-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio copia i dati da un HDFS locale a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio, impostare il gateway di gestione dati. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Servizio collegato HDFS**: questo esempio usa l'autenticazione di Windows. Per i diversi tipi di autenticazione disponibili, vedere la sezione [Proprietà del servizio collegato HDFS](#hdfs-linked-service-properties).

	{
	    "name": "HDFSLinkedService",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}

**Servizio collegato Archiviazione di Azure**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Set di dati di input HDFS**: questo set di dati fa riferimento alla cartella HDFS DataTransfer/UnitTest/. La pipeline copia tutti i file di questa cartella nella destinazione.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.
	
	{
	    "name": "InputDataset",
	    "properties": {
	        "type": "FileShare",
	        "linkedServiceName": "HDFSLinkedService",
	        "typeProperties": {
	            "folderPath": "DataTransfer/UnitTest/"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}




**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

	{
	    "name": "OutputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **FileSystemSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.
	
	{
	    "name": "pipeline",
	    "properties":
	    {
	        "activities":
	        [
	            {
	                "name": "HdfsToBlobCopy",
	                "inputs": [ {"name": "InputDataset"} ],
	                "outputs": [ {"name": "OutputDataset"} ],
	                "type": "Copy",
	                "typeProperties":
	                {
	                    "source":
	                    {
	                        "type": "FileSystemSource"
	                    },
	                    "sink":
	                    {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy":
	                {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Proprietà del servizio collegato HDFS

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato HDFS.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| type | La proprietà type deve essere impostata su: **Hdfs** | Sì | 
| Url | URL di HDFS | Sì |
| encryptedCredential | Output [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) delle credenziali di accesso. | No |
| userName | Nome utente per l'autenticazione di Windows | Sì (per l'autenticazione di Windows)
| password | Password per l'autenticazione di Windows | Sì (per l'autenticazione di Windows)
| authenticationType | Windows o anonima. | Sì |
| gatewayName | Nome del gateway che il servizio Data Factory deve usare per connettersi a HDFS. | Sì |   

Per informazioni dettagliate sull'impostazione delle credenziali per un HDFS locale, vedere [Impostazione delle credenziali e della sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

### Uso dell'autenticazione anonima

	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "userName": "hadoop",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### Uso dell'autenticazione di Windows
	
	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}
 


## Proprietà del tipo di set di dati HDFS

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **FileShare**, che include il set di dati HDFS, presenta le proprietà seguenti.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
folderPath | Percorso della cartella. Esempio: `myfolder`<br/><br/>usare il carattere di escape "\" per i caratteri speciali presenti nella stringa. Ad esempio, per folder\\subfolder specificare folder\\\subfolder e per d:\\samplefolder specificare d:\\\samplefolder.<br/><br/>È possibile unire questa proprietà a **partitionBy** per ottenere percorsi di cartella basati su data e ora di inizio/fine delle sezioni. | Sì
fileName | Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato è nel formato seguente: <br/><br/>Data.<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | No
partitionedBy | partitionedBy può essere usato per specificare un valore folderPath dinamico e un nome file per i dati di una serie temporale. Ad esempio, folderPath con parametri per ogni ora di dati. | No
fileFilter | Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file. <br/><br/>I valori consentiti sono: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempio 1: `"fileFilter": "*.log"`<br/>Esempio 2: `"fileFilter": 2014-1-?.txt"`<br/><br/>**Nota**: fileFilter è applicabile per un set di dati di input FileShare | No
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per informazioni dettagliate, vedere le sezioni [Specifica TextFormat](#specifying-textformat), [Specifica AvroFormat](#specifying-avroformat), [Specifica JsonFormat](#specifying-jsonformat), [Specifica OrcFormat](#specifying-orcformat) e [Specifica ParquetFormat](#specifying-parquetformat). Se si desidera copiare i file così come sono tra archivi basati su file (copia binaria), è possibile saltare la sezione formato sia nella definizione del set di dati di output che in quella di input. | No 
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono: **GZip**, **Deflate** e **BZip2** e i livelli supportati sono: **Ottimale** e **Più veloce**. Le impostazioni di compressione non sono attualmente supportate per i dati **AvroFormat** o **OrcFormat**. Per maggiori informazioni, vedere la sezione [Supporto della compressione](#compression-support). | No |



> [AZURE.NOTE] filename e fileFilter non possono essere usati contemporaneamente.


### Uso della proprietà partionedBy

Come indicato nella sezione precedente, partitionedBy può essere usato per specificare un valore folderPath dinamico e un nome file per i dati di una serie temporale. È possibile eseguire questa operazione con le macro della data factory e le variabili di sistema SliceStart, SliceEnd, che indicano il periodo di tempo logico per una sezione di dati specificata.

Per ulteriori informazioni sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli [Creazione di set di dati](data-factory-create-datasets.md), [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) e [Creazione di pipeline](data-factory-create-pipelines.md).

#### Esempio 1.

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato (AAAAMMGGHH) specificato. SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. Ad esempio: wikisampledataout/wikidatagateway/2014100103 o wikisampledataout/wikidatagateway/2014100104.

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

In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate che vengono usate dalle proprietà folderPath e fileName.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]  
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Proprietà del tipo di attività di copia HDFS

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Per l'attività di copia con origine di tipo **FileSystemSource**, nella sezione typeProperties sono disponibili le proprietà seguenti:

**FileSystemSource** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. | True, False (valore predefinito)| No |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0928_2016-->