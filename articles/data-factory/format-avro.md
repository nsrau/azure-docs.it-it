---
title: Formato Avro in Azure Data Factory
description: Questo argomento descrive come gestire il formato Avro in Azure Data Factory.This topic describes how to deal with Avro format in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416341"
---
# <a name="avro-format-in-azure-data-factory"></a>Formato Avro in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Seguire questo articolo quando si desidera **analizzare i file Avro o scrivere i dati in formato Avro**. 

Il formato Avro è supportato per i connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di](concepts-datasets-linked-services.md) dati. In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati Avro.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà type del dataset deve essere impostata su **Avro**. | Sì      |
| posizione         | Impostazioni di posizione dei file. Ogni connettore basato su file ha `location`il proprio tipo di posizione e le proprietà supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione delle proprietà del set di dati**. | Sì      |
| avroCompressionCodec (codice di compressione) | Codec di compressione da utilizzare per la scrittura nei file Avro. Durante la lettura dai file Avro, Data Factory determina automaticamente il codec di compressione in base ai metadati del file.<br>I tipi supportati sono "**none**" (predefinito), "**sgonfiano**", "**scattano**". Nota: Attualmente l'attività di copia non supporta Snappy quando i file Avro di lettura/scrittura. | No       |

> [!NOTE]
> Lo spazio vuoto nel nome della colonna non è supportato per i file Avro.

Di seguito è riportato un esempio di set di dati Avro in Archiviazione BLOB di Azure:Below is an example of Avro dataset on Azure Blob Storage:

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione viene fornito un elenco delle proprietà supportate dall'origine avro e dal sink.

### <a name="avro-as-source"></a>Avro come sorgente

Le proprietà seguenti sono supportate nella sezione *** \*relativa all'origine\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata **su AvroSource**. | Sì      |
| StoreImpostazioni | Gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di lettura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

### <a name="avro-as-sink"></a>Avro come lavandino

Le proprietà seguenti sono supportate nella sezione del *** \*sink\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata **su AvroSink**. | Sì      |
| StoreImpostazioni | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di scrittura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

## <a name="data-type-support"></a>Supporto dei tipi di dati

### <a name="copy-activity"></a>Attività di copia
I [tipi di dati complessi](https://avro.apache.org/docs/current/spec.html#schema_complex) Avro non sono supportati (record, enumerazioni, matrici, mappe, unioni e elementi fissi) in Attività di copia.

### <a name="data-flows"></a>Flussi di dati
Quando si utilizzano file Avro in flussi di dati, è possibile leggere e scrivere tipi di dati complessi, ma assicurarsi di cancellare prima lo schema fisico dal set di dati. Nei flussi di dati è possibile impostare la proiezione logica e derivare le colonne che sono strutture complesse, quindi eseguire automaticamente il mapping di tali campi a un file Avro.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
