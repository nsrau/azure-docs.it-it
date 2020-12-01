---
title: Formato binario in Azure Data Factory
description: In questo argomento viene descritto come gestire il formato binario in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: be4338942057cf52bf26b7fd1e15f77e724d49f8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348909"
---
# <a name="binary-format-in-azure-data-factory"></a>Formato binario in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il formato binario è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

È possibile usare il set di dati binario nell'attività di [copia](copy-activity-overview.md), l' [attività GetMetadata](control-flow-get-metadata-activity.md)o l' [attività Delete](delete-activity.md). Quando si usa un set di dati binario, ADF non analizza il contenuto del file ma lo considera così com'è. 

>[!NOTE]
>Quando si usa un set di dati binario nell'attività di copia, è possibile copiare solo dal set di dati binario al set di dati binario.

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati binario.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **Binary**. | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location` . **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati**. | Sì      |
| compressione | Gruppo di proprietà per configurare la compressione dei file. Configurare questa sezione quando si desidera eseguire la compressione/decompressione durante l'esecuzione dell'attività. | No |
| type | Codec di compressione utilizzato per leggere/scrivere file binari. <br>I valori consentiti sono **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **tar** o **TarGzip**. <br>**Nota** quando si usa l'attività di **ZipDeflate** copia per decomprimere i / **TarGzip** / file **tar** ZipDeflate TarGzip e scrivere nell'archivio dati sink basato su file, per impostazione predefinita i file vengono estratti nella cartella: `<path specified in dataset>/<folder named as source compressed file>/` , usare `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` nell' [origine dell'attività di copia](#binary-as-source) per controllare se mantenere il nome dei file compressi come struttura di cartelle.| No       |
| livello | Rapporto di compressione. Applicare quando il set di dati viene usato nel sink dell'attività di copia.<br>I valori consentiti sono **ottimali** o più **veloci**.<br>- Più **veloce:** L'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non è compresso in modo ottimale.<br>- **Ottimale**: l'operazione di compressione deve essere compressa in modo ottimale, anche se il completamento dell'operazione richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](/dotnet/api/system.io.compression.compressionlevel) . | No       |

Di seguito è riportato un esempio di set di dati binario nell'archivio BLOB di Azure:

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink binario.

>[!NOTE]
>Quando si usa un set di dati binario nell'attività di copia, è possibile copiare solo dal set di dati binario al set di dati binario.

### <a name="binary-as-source"></a>Binario come origine

Nella sezione attività di copia **_ \_ source \**** sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **BinarySource**. | Sì      |
| formatSettings | Gruppo di proprietà. Vedere la tabella **delle impostazioni di lettura binaria** riportata di seguito. | No       |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

**Impostazioni di lettura binarie** supportate in `formatSettings` :

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Il tipo di formatSettings deve essere impostato su **BinaryReadSettings**. | Sì      |
| compressionProperties | Gruppo di proprietà su come decomprimere i dati per un determinato codec di compressione. | No       |
| preserveZipFileNameAsFolder<br>(*in `compressionProperties` -> `type` As `ZipDeflateReadSettings`*) | Si applica quando il set di dati di input viene configurato con la compressione **ZipDeflate** . Indica se mantenere il nome del file zip di origine come struttura di cartelle durante la copia.<br>-Se impostato su **true (impostazione predefinita)**, Data Factory scrive file decompressi in `<path specified in dataset>/<folder named as source zip file>/` .<br>-Se impostato su **false**, Data Factory scrive i file decompressi direttamente in `<path specified in dataset>` . Assicurarsi che non siano presenti nomi di file duplicati in file zip di origine diversi per evitare la competizione o un comportamento imprevisto.  | No |
| preserveCompressionFileNameAsFolder<br>(*in `compressionProperties` -> `type` come `TarGZipReadSettings` o `TarReadSettings`*) | Si applica quando il set di dati di input viene configurato con la compressione **TarGzip** / **tar** . Indica se mantenere il nome file compresso di origine come struttura di cartelle durante la copia.<br>-Se impostato su **true (impostazione predefinita)**, Data Factory scrive i file decompressi in `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Se impostato su **false**, Data Factory scrive i file decompressi direttamente in `<path specified in dataset>` . Assicurarsi che non siano presenti nomi di file duplicati in file di origine diversi per evitare la competizione o un comportamento imprevisto. | No |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>Binario come sink

Le proprietà seguenti sono supportate nella sezione relativa all'attività di copia **_ \_ sink \****.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **BinarySink**. | Sì      |
| storeSettings | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Delete](delete-activity.md)