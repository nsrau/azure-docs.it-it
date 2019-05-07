---
title: Formato di testo delimitato in Azure Data Factory | Microsoft Docs
description: Questo argomento descrive come gestire in formato testo delimitato in Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 407b8ba2fda35d3acbf1b425bb15fe20778613d7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146013"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato di testo delimitato in Azure Data Factory

Seguire questo articolo quando si desidera **analizzare i file di testo delimitato o scrivere i dati in formato testo delimitato**. 

Formato testo delimitato è supportato per i connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), [Blob di Azure](connector-azure-blob-storage.md), [Azure Data Lake Store Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md), [archiviazione File di Azure](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di testo delimitato.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà type del set di dati deve essere impostata su **DelimitedText**. | Sì      |
| location         | Impostazioni del percorso dei file. Ogni connettore basato su file dispone del proprio tipo di posizione e supportate le proprietà in `location`. **Vedere i dettagli nell'articolo del connettore -> sezione Proprietà set di dati**. | Sì      |
| columnDelimiter  | I carattere o caratteri usati per separare le colonne in un file. Attualmente, il delimitatore di multi-char è supportato solo per il Mapping di flusso di dati, ma non l'attività di copia. <br>Il valore predefinito è **virgole `,`** , quando viene definito il delimitatore di colonna come stringa vuota, ovvero alcun delimitatore, l'intera riga non verrà considerato come una singola colonna. | No        |
| rowDelimiter     | Il singolo carattere o "\r\n" utilizzata per separare le righe in un file.<br>Il valore predefinito è uno dei seguenti valori **in fase di lettura: ["\r\n", "\r", "\n"]**, e **"\n" o "\r\n" durante la scrittura** dai dati di Mapping di flusso e attività di copia, rispettivamente. <br>Quando `rowDelimiter` è impostata su alcun delimitatore (stringa vuota), il `columnDelimiter` deve essere impostato come non come delimitatore di (una stringa vuota), ovvero considerare tutto il contenuto come valore singolo. | No        |
| quoteChar        | Il carattere singolo per racchiudere tra virgolette i valori di colonna se contiene il delimitatore di colonna. <br>Il valore predefinito è **virgolette doppie** `"`. <br>Per il Mapping di flusso di dati, `quoteChar` non può essere una stringa vuota. <br>Per attività di copia, quando `quoteChar` viene definito come una stringa vuota, significa che non vi sia alcun carattere di virgolette doppie e valore della colonna non è racchiuso tra virgolette, e `escapeChar` viene usato per eseguire l'escape il delimitatore di colonna e se stessa. | No        |
| escapeChar       | Il singolo carattere di escape delle virgolette all'interno di un valore tra virgolette.<br>Il valore predefinito è **barra rovesciata `\`** . <br>Per il Mapping di flusso di dati, `escapeChar` non può essere una stringa vuota. <br/>Per attività di copia, quando `escapeChar` viene definito come una stringa vuota, il `quoteChar` deve essere impostato come anche una stringa vuota, nel qual caso assicurarsi che tutti i valori di colonna non contengono delimitatori. | No        |
| firstRowAsHeader | Specifica se considera la prima riga/verificare come una riga di intestazione con i nomi delle colonne.<br>I valori consentiti sono **true** e **false** (impostazione predefinita). | No        |
| nullValue        | Specifica la rappresentazione di stringa del valore null. <br>Il valore predefinito è **una stringa vuota**. | No        |
| encodingName     | Il tipo di codifica utilizzato per leggere/scrivere file di test. <br>I valori consentiti sono i seguenti: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "ENCODING="GB2312","GB18030","JOHAB","SHIFT-JIS","CP875","CP866","IBM00858","IBM037","IBM273","IBM437","IBM500","IBM737","IBM775","IBM850"," IBM852 ","IBM855","IBM857","IBM860","IBM861","IBM863","IBM864","IBM865","IBM869","IBM870","IBM01140","IBM01141","IBM01142","IBM01143","IBM01144","IBM01145","IBM01146","IBM01147","IBM01148","IBM01149","ISO-2022-JP","ISO-2022-KR ","ISO-8859-1","ISO-8859-2","ISO-8859-3","ISO-8859-4","ISO-8859-5","ISO-8859-6","ISO-8859-7","ISO-8859-8","ISO-8859-9","ISO-8859-13","ISO-8859-15","WINDOWS-874","WINDOWS-1250","WINDOWS-1251","WINDOWS-1252","WINDOWS-1253"," WINDOWS-1254 ","WINDOWS-1255","WINDOWS-1256","WINDOWS-1257","WINDOWS-1258".<br>Si noti che il Mapping di flusso di dati non supporta la codifica UTF-7. | No        |
| compressionCodec | Il codec di compressione utilizzato per leggere/scrivere file di testo. <br>I valori consentiti sono **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, oppure **lz4**. da utilizzare quando si salva il file. <br>Si noti attualmente non supporta attività di copia "snappy" & "lz4" e il Mapping di flusso di dati non supporta "ZipDeflate". | No        |
| CompressionLevel | Il rapporto di compressione. <br>I valori consentiti sono **Optimal** oppure **Fastest**.<br>- **Più veloce:** l'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non viene compresso in modo ottimale.<br>- **Ottimale**: l'operazione di compressione deve comprimere il file in modo ottimale, anche se il completamento richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No        |

Di seguito è riportato un esempio di set di dati di testo delimitato nell'archiviazione Blob di Azure:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dal testo delimitato da virgole di origine e sink.

### <a name="delimited-text-as-source"></a>Testo delimitato come origine 

Nell'attività di copia sono supportate le proprietà seguenti ***\*sorgente\**** sezione.

| Proprietà       | Descrizione                                                  | Obbligatoria |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | La proprietà type dell'origine dell'attività di copia deve essere impostata su **DelimitedTextSource**. | Sì      |
| formatSettings | Un gruppo di proprietà. Fare riferimento a **Delimited text leggere impostazioni** tabella riportata di seguito. | No        |
| storeSettings  | Un gruppo di proprietà sulla lettura dei dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings`. **Vedere i dettagli nell'articolo del connettore -> sezione Proprietà attività di copia**. | No        |

È supportato **testo delimitato leggere le impostazioni** sotto `formatSettings`:

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Il tipo di formatSettings deve essere impostato su **DelimitedTextReadSetting**. | Sì      |
| skipLineCount | Indica il numero di righe **non vuote** da ignorare durante la lettura di dati da file di input. <br>Se sono specificati sia skipLineCount che firstRowAsHeader, le righe vengono ignorate e le informazioni di intestazione vengono lette dal file di input. | No        |

### <a name="delimited-text-as-sink"></a>Testo delimitato come sink

Nell'attività di copia sono supportate le proprietà seguenti ***\*sink\**** sezione.

| Proprietà       | Descrizione                                                  | Obbligatoria |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | La proprietà type dell'origine dell'attività di copia deve essere impostata su **DelimitedTextSink**. | Sì      |
| formatSettings | Un gruppo di proprietà. Fare riferimento a **Delimited text scrivere impostazioni** tabella riportata di seguito. |          |
| storeSettings  | Un gruppo di proprietà relativa alla scrittura dei dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportati in `storeSettings`. **Vedere i dettagli nell'articolo del connettore -> sezione Proprietà attività di copia**. | No        |

È supportato **impostazioni di scrittura di testo delimitato** sotto `formatSettings`:

| Proprietà      | Descrizione                                                  | Obbligatoria                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Il tipo di formatSettings deve essere impostato su **DelimitedTextWriteSetting**. | Sì                                                   |
| fileExtension | L'estensione del file usato per denominare i file di output, ad esempio `.csv`, `.txt`. Deve essere specificato quando il `fileName` non viene specificato nell'output DelimitedText set di dati. | Sì quando nome del file non è specificato nel set di dati di output |

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Informazioni dettagliate dal [trasformazione sorgente](data-flow-source.md) e [sink trasformazione](data-flow-sink.md) nel Mapping di flusso di dati.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping di flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)