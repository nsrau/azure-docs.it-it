---
title: Spostare i dati dal servizio di archiviazione semplice di Amazon usando Data Factory | Microsoft Docs
description: Informazioni su come spostare i dati dal servizio di archiviazione semplice di Amazon (S3) usando Data Factory di Azure.
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
ms.date: 02/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: bf5010537d222045b5bf13d85cab6c48c9659ed0
ms.openlocfilehash: b055342d8e11e4d38688681b36ef9a1f717fdf15
ms.lasthandoff: 02/23/2017


---
# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Spostare i dati dal servizio di archiviazione semplice di Amazon usando Data Factory di Azure
Questo articolo illustra come usare l'attività di copia in un data factory di Azure per spostare dati dal servizio di archiviazione semplice di Amazon (S3) a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati e un elenco di archivi dati di origine/sink.  

Data Factory supporta attualmente solo lo spostamento di dati da Amazon S3 ad altri archivi dati, non da altri archivi dati a Amazon S3.

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per copiare i dati da Amazon S3, assicurarsi di avere le autorizzazioni indicate di seguito:

* **s3:GetObject** e **s3:GetObjectVersion** per le operazioni oggetto di Amazon S3
* **S3:ListBucket** per le operazioni sui bucket di Amazon S3. Se si usa Copia guidata, è necessario anche **s3:ListAllMyBuckets**.

È possibile trovare l'elenco completo delle autorizzazioni di Amazon S3 con tutti i dettagli in [Specificando le autorizzazioni in un criterio](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="copy-data-wizard"></a>Copia di dati guidata
Il modo più semplice per creare una pipeline che copia i dati da Amazon S3 consiste nell'usare la procedura Copia di dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

L'esempio seguente fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra come copiare i dati da Amazon S3 all'archiviazione BLOB di Azure. Tuttavia, i dati possono essere copiati in tutti i sink indicati [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>Esempio: copiare i dati da Amazon S3 al BLOB di Azure
Questo esempio illustra come copiare i dati da Amazon S3 all'archiviazione BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.  

L'esempio include le entità di Data Factory seguenti:

* Un servizio collegato di tipo [AwsAccessKey](#linked-service-properties).
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [AmazonS3](#dataset-type-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio i dati vengono copiati da Amazon S3 a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato Amazon S3**

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

**Servizio collegato Archiviazione di Azure**

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

**Set di dati di input Amazon S3**

Impostando **"external": true** si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno. Impostare questa proprietà su true in un set di dati di input non generato da un'attività nella pipeline.

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


**Set di dati di output del BLOB di Azure**

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


**Pipeline con attività di copia**

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


## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato Amazon S3 (**AwsAccessKey**).

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| accessKeyID |ID della chiave di accesso segreta. |string |Sì |
| secretAccessKey |La stessa chiave di accesso segreta. |La stringa segreta crittografata |Sì |

## <a name="dataset-type-properties"></a>Proprietà del tipo di set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **AmazonS3** , che include il set di dati Amazon S3, presenta le proprietà seguenti

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| bucketName |Il nome del bucket S3. |string |Sì |
| key |La chiave dell'oggetto S3. |string |No |
| prefix |Il prefisso per la chiave dell'oggetto S3. Vengono selezionati gli oggetti le cui chiavi iniziano con questo prefisso. Si applica solo quando la chiave è vuota. |string |No |
| version |La versione dell'oggetto S3 se è stato abilitato il controllo delle versioni S3. |string |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](#specifying-textformat), [JsonFormat](#specifying-jsonformat), [AvroFormat](#specifying-avroformat), [OrcFormat](#specifying-orcformat) e [ParquetFormat](#specifying-parquetformat). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No | |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**, mentre i livelli supportati sono **Optimal** (Ottimale) **Fastest** (Più veloce). Per altre informazioni, vedere la sezione [Specificare la compressione](#specifying-compression). |No | |


> [!NOTE]
> bucketName + chiave specifica la posizione dell'oggetto S3 in cui il bucket è il contenitore radice per gli oggetti S3 e la chiave rappresenta il percorso completo all'oggetto S3.
>
>

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
### <a name="sample-data-set-with-version"></a>Set di dati di esempio (con versione)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
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
Nell'esempio, vengono utilizzati dei valori fissi per le proprietà chiave e bucketName nel set di dati Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

È possibile fare in modo che Data Factory calcoli la chiave e il bucketName dinamicamente in fase di runtime utilizzando le variabili di sistema, come SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

È possibile eseguire la stessa operazione per la proprietà prefix di un set di dati di Amazon S3. Per un elenco delle funzioni e delle variabili di sistema supportate da Data Factory, vedere l'articolo [Funzioni e variabili di sistema di Data Factory](data-factory-functions-variables.md) .

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="copy-activity-type-properties"></a>Proprietà del tipo di attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Se l'origine nell'attività di copia è di tipo **FileSystemSource** (che comprende Amazon S3), sono disponibili le proprietà seguenti nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Specifica se elencare in modo ricorsivo gli oggetti S3 sotto la directory. |true/false |No |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

* [Esercitazione dell'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate della creazione di una pipeline con un'attività di copia.

