---
title: Formato avro in Azure Data Factory
description: Questo argomento descrive come gestire il formato avro in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 9e962a0e76cdc0d51a87df3c33927c34db991fc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927403"
---
# <a name="avro-format-in-azure-data-factory"></a>Formato avro in Azure Data Factory

Seguire questo articolo quando si vuole **analizzare i file avro o scrivere i dati in formato avro**. 

Il formato avro è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati avro.

| Proprietà         | Description                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **avro**. | SÌ      |
| location         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location`. **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati**. | SÌ      |
| avroCompressionCodec | Codec di compressione da usare durante la scrittura nei file avro. Durante la lettura da file Avro, Data Factory determina automaticamente il codec di compressione basato sui metadati del file.<br>I tipi supportati sono "**None**" (impostazione predefinita), "**deflate**", "**Snapper**". | No       |

> [!NOTE]
> Lo spazio vuoto nel nome della colonna non è supportato per i file avro.

Di seguito è riportato un esempio di set di dati avro nell'archivio BLOB di Azure:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink avro.

### <a name="avro-as-source"></a>Avro come origine

Le proprietà seguenti sono supportate nella sezione ***\*origine\**** dell'attività di copia.

| Proprietà      | Description                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **AvroSource**. | SÌ      |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings`. **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

### <a name="avro-as-sink"></a>Avro come sink

Le proprietà seguenti sono supportate nella sezione ***\*sink\**** dell'attività di copia.

| Proprietà      | Description                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **AvroSink**. | SÌ      |
| storeSettings | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate in `storeSettings`. **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

## <a name="data-type-support"></a>Supporto dei tipi di dati

I [tipi di dati complessi](https://avro.apache.org/docs/current/spec.html#schema_complex) avro non sono supportati (record, enumerazioni, matrici, mappe, unioni e fissi).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
