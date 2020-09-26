---
title: Formato Excel in Azure Data Factory
description: In questo argomento viene descritto come gestire il formato Excel in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: jingwang
ms.openlocfilehash: dad1f9f232cb9d713af81f6aea57a4ffe651da19
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331965"
---
# <a name="excel-format-in-azure-data-factory"></a>Formato Excel in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Seguire questo articolo quando si desidera **analizzare i file di Excel**. Azure Data Factory supporta sia ". xls" sia ". xlsx".

Il formato Excel è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md). È supportato come origine ma non come sink.

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di Excel.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **Excel**.   | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location` . | Sì      |
| Nomefoglio        | Nome del foglio di lavoro di Excel per la lettura dei dati.                       | Sì      |
| range            | Intervallo di celle del foglio di dati specificato per individuare i dati selettivi, ad esempio:<br>-Non specificato: legge l'intero foglio di foglio come tabella dalla prima riga e colonna non vuota<br>- `A3`: legge una tabella a partire dalla cella specificata, rileva in modo dinamico tutte le righe sottostanti e tutte le colonne a destra<br>- `A3:H5`: legge questo intervallo fisso come tabella<br>- `A3:A3`: legge questa singola cella | No       |
| firstRowAsHeader | Specifica se trattare la prima riga del foglio di dati o dell'intervallo specificato come riga di intestazione con i nomi delle colonne.<br>I valori consentiti sono **true** e **false** (impostazione predefinita). | No       |
| nullValue        | Specifica la rappresentazione di stringa del valore null. <br>Il valore predefinito è una **stringa vuota**. | No       |
| compressione | Gruppo di proprietà per configurare la compressione dei file. Configurare questa sezione quando si desidera eseguire la compressione/decompressione durante l'esecuzione dell'attività. | No |
| type<br/>(*in `compression` *) | Codec di compressione usato per leggere/scrivere file JSON. <br>I valori consentiti sono **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **Snapper**o **LZ4**. Il valore predefinito non è compresso.<br>**Nota** attualmente l'attività di copia non supporta "blocco" & "LZ4" e il flusso di dati di mapping non supporta "ZipDeflate".<br>**Nota** quando si usa l'attività di copia per decomprimere i file **ZipDeflate** e scrivere nell'archivio dati sink basato su file, i file vengono estratti nella cartella: `<path specified in dataset>/<folder named as source zip file>/` . | No.  |
| livello<br/>(*in `compression` *) | Rapporto di compressione. <br>I valori consentiti sono **ottimali** o più **veloci**.<br>- Più **veloce:** L'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non è compresso in modo ottimale.<br>- **Ottimale**: l'operazione di compressione deve essere compressa in modo ottimale, anche se il completamento dell'operazione richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Di seguito è riportato un esempio di set di dati di Excel nell'archivio BLOB di Azure:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Excel.

### <a name="excel-as-source"></a>Excel come origine 

Nella sezione *** \* origine \* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **ExcelSource**. | Sì      |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings` . | No       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

In mapping dei flussi di dati è possibile leggere il formato Excel negli archivi dati seguenti: [archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). È possibile puntare a file di Excel usando il set di dati di Excel o un [set di dati inline](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Proprietà origine

La tabella seguente elenca le proprietà supportate da un'origine Excel. È possibile modificare queste proprietà nella scheda **Opzioni di origine** . Quando si usa il set di dati inline, verranno visualizzate impostazioni file aggiuntive, che corrispondono alle proprietà descritte nella sezione [Proprietà set di dati](#dataset-properties) .

| Nome                      | Descrizione                                                  | Obbligatoria | Valori consentiti                                            | Proprietà script flusso di dati         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Percorsi Wild Card           | Verranno elaborati tutti i file corrispondenti al percorso con caratteri jolly. Sostituisce la cartella e il percorso del file impostati nel set di dati. | no       | String[]                                                  | wildcardPaths                     |
| Partition Root Path (Percorso radice partizione)       | Per i dati di file partizionati, è possibile immettere un percorso radice della partizione per leggere le cartelle partizionate come colonne | no       | string                                                    | partitionRootPath                 |
| Elenco di file             | Indica se l'origine sta puntando a un file di testo che elenca i file da elaborare | no       | `true` o `false`                                         | fileList                          |
| Colonna in cui archiviare il nome del file | Crea una nuova colonna con il nome e il percorso del file di origine       | no       | string                                                    | rowUrlColumn                      |
| Al termine          | Elimina o sposta i file dopo l'elaborazione. Il percorso del file inizia dalla radice del contenitore | no       | Elimina: `true` o `false` <br> Spostare `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Filtra per Ultima modifica   | Scegliere di filtrare i file in base alla data dell'Ultima modifica | no       | Timestamp                                                 | modifiedAfter <br> modifiedBefore |
| Consenti nessun file trovato | Se true, non viene generato alcun errore se non viene trovato alcun file | no | `true` o `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Esempio di origine

L'immagine seguente è un esempio di una configurazione di origine Excel nel mapping dei flussi di dati tramite la modalità DataSet.

![Origine Excel](media/data-flow/excel-source.png)

Lo script del flusso di dati associato è:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Se si utilizza il set di dati inline, nel flusso di dati di mapping vengono visualizzate le opzioni di origine seguenti.

![Set di dati di origine Excel in linea](media/data-flow/excel-source-inline-dataset.png)

Lo script del flusso di dati associato è:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
