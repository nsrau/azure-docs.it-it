---
title: Formato avro in Azure Data Factory
description: Questo argomento descrive come gestire il formato avro in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 7d61121b4c80b7b89ec29ade4ab1bfab91a660d9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010555"
---
# <a name="avro-format-in-azure-data-factory"></a>Formato avro in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Seguire questo articolo quando si vuole **analizzare i file avro o scrivere i dati in formato avro**. 

Il formato avro è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati avro.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **avro**. | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location` . **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati**. | Sì      |
| avroCompressionCodec | Codec di compressione da usare durante la scrittura nei file avro. Durante la lettura da file Avro, Data Factory determina automaticamente il codec di compressione basato sui metadati del file.<br>I tipi supportati sono "**None**" (impostazione predefinita), "**deflate**", "**Snapper**". Nota Attualmente l'attività di copia non supporta l'allineamento quando i file avro di lettura/scrittura. | No       |

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

Nella sezione attività di copia **_ \_ source \**** sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **AvroSource**. | Sì      |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

### <a name="avro-as-sink"></a>Avro come sink

Le proprietà seguenti sono supportate nella sezione relativa all'attività di copia **_ \_ sink \****.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **AvroSink**. | Sì      |
| formatSettings          | Gruppo di proprietà. Vedere la tabella **delle impostazioni di scrittura avro** riportata di seguito.| No      |
| storeSettings | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

**Impostazioni di scrittura avro** supportate in `formatSettings` :

| Proprietà      | Descrizione                                                  | Obbligatoria                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Il tipo di formatSettings deve essere impostato su **AvroWriteSettings**. | Sì                                                   |
| maxRowsPerFile | Quando si scrivono dati in una cartella, è possibile scegliere di scrivere in più file e specificare il numero massimo di righe per ogni file.  | No |
| fileNamePrefix | Applicabile quando `maxRowsPerFile` è configurato.<br> Specificare il prefisso del nome file durante la scrittura di dati in più file. questo modello è risultato: `<fileNamePrefix>_00000.<fileExtension>` . Se non è specificato, il prefisso del nome file verrà generato automaticamente. Questa proprietà non viene applicata quando l'origine è un archivio dati basato su file o sull' [opzione partition-Enabled](copy-activity-performance-features.md).  | No |

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Per eseguire il mapping dei flussi di dati, è possibile leggere e scrivere nel formato avro negli archivi dati seguenti: [archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Proprietà origine

La tabella seguente elenca le proprietà supportate da un'origine avro. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Percorsi Wild Card | Verranno elaborati tutti i file corrispondenti al percorso con caratteri jolly. Sostituisce la cartella e il percorso del file impostati nel set di dati. | No | String[] | wildcardPaths |
| Partition Root Path (Percorso radice partizione) | Per i dati di file partizionati, è possibile immettere un percorso radice della partizione per leggere le cartelle partizionate come colonne | No | string | partitionRootPath |
| Elenco di file | Indica se l'origine sta puntando a un file di testo che elenca i file da elaborare | No | `true` o `false` | fileList |
| Colonna in cui archiviare il nome del file | Crea una nuova colonna con il nome e il percorso del file di origine | No | string | rowUrlColumn |
| Al termine | Elimina o sposta i file dopo l'elaborazione. Il percorso del file inizia dalla radice del contenitore | No | Elimina: `true` o `false` <br> Spostare `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtra per Ultima modifica | Scegliere di filtrare i file in base alla data dell'Ultima modifica | No | Timestamp | modifiedAfter <br> modifiedBefore |
| Consenti nessun file trovato | Se true, non viene generato alcun errore se non viene trovato alcun file | No | `true` o `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>Proprietà sink

La tabella seguente elenca le proprietà supportate da un sink avro. È possibile modificare queste proprietà nella scheda **Impostazioni** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Cancella la cartella | Se la cartella di destinazione viene cancellata prima della scrittura | No | `true` o `false` | truncate |
| Opzione nome file | Formato di denominazione dei dati scritti. Per impostazione predefinita, un file per partizione è nel formato `part-#####-tid-<guid>` | No | Modello: stringa <br> Per partizione: stringa [] <br> Come dati in column: String <br> Output in un singolo file: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Virgolette tutte | Racchiudere tutti i valori tra virgolette | No | `true` o `false` | quoteAll |

## <a name="data-type-support"></a>Supporto dei tipi di dati

### <a name="copy-activity"></a>Attività di copia
I [tipi di dati complessi](https://avro.apache.org/docs/current/spec.html#schema_complex) avro non sono supportati (record, enumerazioni, matrici, mappe, unioni e fisse) nell'attività di copia.

### <a name="data-flows"></a>Flussi di dati
Quando si lavora con i file avro nei flussi di dati, è possibile leggere e scrivere tipi di dati complessi, ma assicurarsi di cancellare prima lo schema fisico dal set di dati. Nei flussi di dati è possibile impostare la proiezione logica e derivare colonne che sono strutture complesse, quindi eseguire il mapping automatico di tali campi a un file avro.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
