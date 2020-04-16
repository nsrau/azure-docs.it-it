---
title: Formato binario in Azure Data FactoryBinary format in Azure Data Factory
description: Questo argomento descrive come gestire il formato binario in Azure Data Factory.This topic describes how to deal with Binary format in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416333"
---
# <a name="binary-format-in-azure-data-factory"></a>Formato binario in Azure Data FactoryBinary format in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il formato binario è supportato per i connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), Blob di [Azure](connector-azure-blob-storage.md), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md), Azure Data Lake Storage [Gen2](connector-azure-data-lake-storage.md), [Archiviazione file di Azure](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

È possibile utilizzare il set di dati binario in [Attività Copy](copy-activity-overview.md), [Attività GetMetadata](control-flow-get-metadata-activity.md)o [Elimina attività](delete-activity.md). Quando si usa il set di dati binario, ADF non analizza il contenuto del file, ma lo considera così com'è. 

>[!NOTE]
>Quando si usa il set di dati binari nell'attività di copia, è possibile copiare solo da un set di dati binario a un set di dati binario.

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di](concepts-datasets-linked-services.md) dati. In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati binario.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà type del dataset deve essere impostata su **Binary**. | Sì      |
| posizione         | Impostazioni di posizione dei file. Ogni connettore basato su file ha `location`il proprio tipo di posizione e le proprietà supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione delle proprietà del set di dati**. | Sì      |
| compressione | Gruppo di proprietà per configurare la compressione dei file. Configurare questa sezione quando si desidera eseguire la compressione/decompressione durante l'esecuzione dell'attività. | No |
| type | Codec di compressione utilizzato per leggere/scrivere file binari. <br>I valori consentiti sono **bzip2**, **gzip**, **deflate**, **.** da utilizzare quando si salva il file.<br>Si noti che quando si utilizza l'attività di copia per decomprimere i file zipDeflate `<path specified in dataset>/<folder named as source zip file>/`e scrivere nell'archivio dati sink basato su file, i file verranno estratti nella cartella: . | No       |
| level | Il rapporto di compressione. Applicare quando il set di dati viene usato nel sink di attività Copy.Apply when dataset is used in Copy activity sink.<br>I valori consentiti sono **Ottimale** o **Più veloce**.<br>- **Il più veloce:** L'operazione di compressione dovrebbe essere completata il più rapidamente possibile, anche se il file risultante non è compresso in modo ottimale.<br>- **Ottimale**: L'operazione di compressione deve essere compressa in modo ottimale, anche se il completamento dell'operazione richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Di seguito è riportato un esempio di set di dati binari nell'archiviazione BLOB di Azure:Below is an example of Binary dataset on Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione viene fornito un elenco delle proprietà supportate dall'origine binaria e dal sink.

>[!NOTE]
>Quando si usa il set di dati binari nell'attività di copia, è possibile copiare solo da un set di dati binario a un set di dati binario.

### <a name="binary-as-source"></a>Binario come origine

Le proprietà seguenti sono supportate nella sezione *** \*relativa all'origine\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata su **BinarySource**. | Sì      |
| StoreImpostazioni | Gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di lettura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

### <a name="binary-as-sink"></a>Binario come lavandino

Le proprietà seguenti sono supportate nella sezione del *** \*sink\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata su **BinarySink**. | Sì      |
| StoreImpostazioni | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di scrittura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Elimina attività](delete-activity.md)
