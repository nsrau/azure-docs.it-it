---
title: Spostare i dati da Amazon Simple Storage Service usando Data Factory | Microsoft Docs
description: Informazioni su come spostare i dati da Amazon Simple Storage Service (S3) usando Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 042b81cd2228ac87bcb6c654776a9c235920b77e
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Spostare i dati da Amazon Simple Storage Service usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-amazon-simple-storage-service-connector.md)
> * [Versione 2 - Anteprima](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 di Data Factory, che è disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Connettore Amazon S3 nella versione 2](../connector-amazon-simple-storage-service.md).

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da Amazon Simple Storage Service (S3). Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

È possibile copiare dati da Amazon S3 a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory supporta attualmente solo lo spostamento di dati da Amazon S3 ad altri archivi dati, non da altri archivi dati ad Amazon S3.

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per copiare i dati da Amazon S3, assicurarsi di avere le autorizzazioni indicate di seguito:

* `s3:GetObject` e `s3:GetObjectVersion` per le operazioni di oggetto Amazon S3.
* `s3:ListBucket` per le operazioni di bucket Amazon S3. Se si usa la copia guidata di Data Factory, è necessario anche `s3:ListAllMyBuckets`.

Per informazioni dettagliate sull'elenco completo delle autorizzazioni di Amazon S3 con tutti i dettagli in [Specifying Permissions in a Policy](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) (Specificare le autorizzazioni in un criterio).

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da un'origine Amazon S3 usando diversi strumenti o API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Per una procedura dettagliata, vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md).

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia.
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di Data Factory. Per un esempio con definizioni JSON per entità di Data factory usate per copiare dati da un archivio dati Amazon S3, vedere la sezione [Esempio JSON: copiare dati da Amazon S3 al BLOB di Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob) di questo articolo.

> [!NOTE]
> Per informazioni dettagliate sui formati di compressione e i file supportati per l'attività di copia, vedere [Formati di compressione e file in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di Data factory specifiche di Amazon S3.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
Un servizio collegato collega un archivio dati a una data factory. Viene creato un servizio collegato di tipo **AwsAccessKey** per collegare l'archivio dati Amazon S3 alla data factory. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato Amazon S3 (AwsAccessKey).

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| accessKeyID |ID della chiave di accesso segreta. |string |Sì |
| secretAccessKey |La stessa chiave di accesso segreta. |La stringa segreta crittografata |Sì |

Di seguito è fornito un esempio:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per specificare un set di dati per rappresentare i dati di input in un'archiviazione BLOB di Azure, impostare la proprietà del tipo del set di dati su **AmazonS3**. Impostare la proprietà **linkedServiceName** del set di dati sul nome del servizio collegato Amazon S3. Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). 

Le sezioni come struttura, disponibilità e criteri sono simili per tutti i tipi di set di dati, ad esempio database SQL, BLOB di Azure e tabelle di Azure. La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per un set di dati di tipo **AmazonS3**, che include il set di dati Amazon S3, presenta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| bucketName |Il nome del bucket S3. |string |Sì |
| key |La chiave dell'oggetto S3. |string |No |
| prefix |Il prefisso per la chiave dell'oggetto S3. Vengono selezionati gli oggetti le cui chiavi iniziano con questo prefisso. Si applica solo quando la chiave è vuota. |string |No |
| version |La versione dell'oggetto S3 se è stato abilitato il controllo delle versioni S3. |String |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](data-factory-supported-file-and-compression-formats.md#text-format), [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formato AVRO](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato OCR](data-factory-supported-file-and-compression-formats.md#orc-format) e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No | |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No | |


> [!NOTE]
> **bucketName + key** specifica la posizione dell'oggetto S3 in cui il bucket è il contenitore radice per gli oggetti S3 e key rappresenta il percorso completo all'oggetto S3.

### <a name="sample-dataset-with-prefix"></a>Set di dati di esempio con prefisso

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Set di dati di esempio (con versione)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Percorsi dinamici per S3
Nell'esempio precedente vengono usati dei valori fissi per le proprietà **key** e **bucketName** nel set di dati Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

È possibile fare in modo che Data Factory calcoli queste proprietà dinamicamente in fase di runtime usando le variabili di sistema, come SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

È possibile eseguire la stessa operazione per la proprietà **prefix** di un set di dati di Amazon S3. Per un elenco delle funzioni e delle variabili, vedere [Funzioni e variabili di sistema di Data Factory](data-factory-functions-variables.md) .

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri. Le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività. Per l'attività di copia, le proprietà variano in base ai tipi di origine e sink. Se l'origine nell'attività di copia è di tipo **FileSystemSource**, che comprende Amazon S3, sono disponibili le proprietà seguenti nella sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Specifica se elencare in modo ricorsivo gli oggetti S3 sotto la directory. |true/false |No |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Esempio JSON: copiare dati da Amazon S3 al BLOB di Azure
Questo esempio illustra come copiare i dati da Amazon S3 all'archiviazione BLOB di Azure. I dati possono tuttavia essere copiati direttamente in [uno qualsiasi dei sink supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Data Factory.

L'esempio fornisce le definizioni JSON per le entità di data factory seguenti. È possibile usare queste definizioni per creare una pipeline per copiare dati da Amazon S3 a un archivio BLOB mediante il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Un servizio collegato di tipo [AwsAccessKey](#linked-service-properties).
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [AmazonS3](#dataset-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Nell'esempio i dati vengono copiati da Amazon S3 a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

### <a name="amazon-s3-linked-service"></a>Servizio collegato Amazon S3

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="amazon-s3-input-dataset"></a>Set di dati di input Amazon S3

Impostando **"external": "true"** si comunica al servizio Data Factory che il set di dati è esterno alla data factory. Impostare questa proprietà su true in un set di dati di input non generato da un'attività nella pipeline.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Set di dati di output del BLOB di Azure

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
```


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Attività di copia in una pipeline con un'origine Amazon S3 e un sink BLOB

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **FileSystemSource** e il tipo di **sink** è impostato su **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Per eseguire il mapping dal set di dati di origine alle colonne dal set di dati sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

* Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md) .

* Per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia, vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

