---
title: Formato di testo delimitato in Azure Data FactoryDelimited text format in Azure Data Factory
description: Questo argomento descrive come gestire il formato di testo delimitato in Azure Data Factory.This topic describes how to deal with delimited text format in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 47d26ad452b8494e591ee919076e5ade8bf19cd7
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011396"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato di testo delimitato in Azure Data FactoryDelimited text format in Azure Data Factory

Seguire questo articolo quando si desidera analizzare i file di **testo delimitati o scrivere i dati in formato testo delimitato.** 

Il formato di testo delimitato è supportato per i connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), Azure Data Lake Storage [Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Archiviazione file di Azure](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di](concepts-datasets-linked-services.md) dati. In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati di testo delimitato.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà type del dataset deve essere impostata su **DelimitedText**. | Sì      |
| posizione         | Impostazioni di posizione dei file. Ogni connettore basato su file ha `location`il proprio tipo di posizione e le proprietà supportate in .  | Sì      |
| columnDelimiter  | Caratteri utilizzati per separare le colonne in un file. <br>Il valore predefinito è **virgola `,` **. Quando il delimitatore di colonna è definito come stringa vuota, ovvero nessun delimitatore, l'intera riga viene considerata come una singola colonna.<br>Attualmente, il delimitatore di colonna come stringa vuota o multi-char è supportato solo per il mapping del flusso di dati, ma non per l'attività di copia.  | No       |
| rowDelimiter     | Il carattere singolo o il carattere ""r"n" utilizzato per separare le righe in un file. <br>Il valore predefinito è uno dei valori seguenti in **lettura: ["r"n", "'r"', "'n"]** e **'n' o 'r'n"** in scrittura mappando il flusso di dati e l'attività di copia rispettivamente. <br>Quando il delimitatore di riga è impostato su nessun delimitatore (stringa vuota), il delimitatore di colonna deve essere impostato come nessun delimitatore (stringa vuota), il che significa considerare l'intero contenuto come un singolo valore.<br>Attualmente, il delimitatore di riga come stringa vuota è supportato solo per il mapping del flusso di dati ma non per l'attività di copia. | No       |
| quoteChar        | Carattere singolo per racchiudere tra virgolette i valori di colonna se contiene il delimitatore di colonna. <br>Il valore predefinito è **quotedoriche doppie** `"`. <br>Per il mapping `quoteChar` del flusso di dati, non può essere una stringa vuota. <br>Per l'attività `quoteChar` Copia, quando viene definito come stringa vuota, significa che non `escapeChar` è presente alcun carattere di virgolette e il valore della colonna non è racchiuso tra virgolette e viene utilizzato per eseguire l'escape del delimitatore di colonna e di se stesso. | No       |
| escapeChar       | Singolo carattere di cui eseguire l'escape tra virgolette all'interno di un valore tra virgolette.<br>Il valore predefinito è **backslash `\` **. <br>Per il mapping `escapeChar` del flusso di dati, non può essere una stringa vuota. <br/>Per l'attività `escapeChar` Copia, quando viene `quoteChar` definito come stringa vuota, anche la stringa deve essere impostata come stringa vuota, nel qual caso assicurarsi che tutti i valori di colonna non contengano delimitatori. | No       |
| firstRowAsHeader | Specifica se trattare/rendere la prima riga come riga di intestazione con i nomi delle colonne.<br>I valori consentiti sono **true** e **false** (impostazione predefinita). | No       |
| nullValue        | Specifica la rappresentazione di stringa del valore null. <br>Il valore predefinito è **empty string**. | No       |
| encodingName     | Tipo di codifica utilizzato per leggere/scrivere i file di test. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Nota Il flusso di dati di mapping non supporta la codifica UTF-7. | No       |
| compressionCodec (compressionCodec) | Codec di compressione utilizzato per leggere/scrivere file di testo. <br>I valori consentiti sono **bzip2**, **gzip**, **defLATE**, **.ipDeflate**, **snappy**o **lz4**. L'impostazione predefinita non è compressa. <br>**Nota:** Attualmente l'attività di copia non supporta "snappy" & "lz4" e il flusso di dati di mapping non supporta ".ipDeflate". <br>**Si noti che** quando si utilizza l'attività di copia per decomprimere i file zipDeflate `<path specified in dataset>/<folder named as source zip file>/`e scrivere nell'archivio dati sink basato su file, i file verranno estratti nella cartella: . | No       |
| Compressionlevel | Il rapporto di compressione. <br>I valori consentiti sono **Ottimale** o **Più veloce**.<br>- **Il più veloce:** L'operazione di compressione dovrebbe essere completata il più rapidamente possibile, anche se il file risultante non è compresso in modo ottimale.<br>- **Ottimale**: L'operazione di compressione deve essere compressa in modo ottimale, anche se il completamento dell'operazione richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Di seguito è riportato un esempio di set di dati di testo delimitato in Archiviazione BLOB di Azure:Below is an example of delimited text dataset on Azure Blob Storage:

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
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione viene fornito un elenco delle proprietà supportate dall'origine e dal sink di testo delimitati.

### <a name="delimited-text-as-source"></a>Testo delimitato come origine 

Le proprietà seguenti sono supportate nella sezione *** \*relativa all'origine\* *** dell'attività di copia.

| Proprietà       | Descrizione                                                  | Obbligatoria |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | La proprietà type dell'origine dell'attività di copia deve essere impostata su **DelimitedTextSource**. | Sì      |
| formatImpostazioni | Gruppo di proprietà. Fare riferimento alla tabella delle impostazioni di **lettura del testo delimitato** riportata di seguito. | No       |
| StoreImpostazioni  | Gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di lettura supportate in . | No       |

Impostazioni di lettura del `formatSettings`testo **delimitate supportate** in:

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Il tipo di formatSettings deve essere impostato su **DelimitedTextReadSettings**. | Sì      |
| skipLineCount | Indica il numero di righe **non vuote** da ignorare durante la lettura di dati da file di input. <br>Se vengono specificati sia skipLineCount che firstRowAsHeader, le righe vengono ignorate e quindi le informazioni dell'intestazione vengono lette dal file di input. | No       |

### <a name="delimited-text-as-sink"></a>Testo delimitato come sink

Le proprietà seguenti sono supportate nella sezione del *** \*sink\* *** dell'attività di copia.

| Proprietà       | Descrizione                                                  | Obbligatoria |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | La proprietà type dell'origine dell'attività di copia deve essere impostata **su DelimitedTextSink**. | Sì      |
| formatImpostazioni | Gruppo di proprietà. Fare riferimento alla tabella delle impostazioni di **scrittura del testo delimitato** riportata di seguito. |          |
| StoreImpostazioni  | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di scrittura supportate in .  | No       |

Impostazioni di scrittura del `formatSettings` **testo delimitate supportate** in:

| Proprietà      | Descrizione                                                  | Obbligatoria                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Il tipo di formatSettings deve essere impostato su **DelimitedTextWriteSettings**. | Sì                                                   |
| Fileextension | L'estensione del file utilizzata per denominare i file di output, ad esempio `.csv`, `.txt`. Deve essere specificato `fileName` quando l'oggetto non è specificato nel set di dati DelimitedText di output. Quando il nome del file è configurato nel set di dati di output, verrà utilizzato come nome del file sink e l'impostazione dell'estensione del file verrà ignorata.  | Sì quando il nome del file non è specificato nel set di dati di output |

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Informazioni dettagliate sulla [trasformazione dell'origine](data-flow-source.md) e sulla [trasformazione sink](data-flow-sink.md) nel mapping del flusso di dati.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
