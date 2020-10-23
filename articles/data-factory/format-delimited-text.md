---
title: Formato testo delimitato in Azure Data Factory
description: In questo argomento viene descritto come gestire il formato testo delimitato in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jingwang
ms.openlocfilehash: c491a0b5e4c4fc517368c5947fa6181201a5b5fd
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127268"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato testo delimitato in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Seguire questo articolo quando si desidera **analizzare i file di testo delimitati o scrivere i dati in un formato di testo delimitato**. 

Il formato testo delimitato è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di testo delimitato.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **DelimitedText**. | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location` .  | Sì      |
| columnDelimiter  | Carattere o caratteri utilizzati per separare le colonne in un file. <br>Il valore predefinito è **virgola `,` **. Quando il delimitatore di colonna viene definito come una stringa vuota, ovvero nessun delimitatore, l'intera riga viene considerata come una colonna singola.<br>Attualmente, il delimitatore di colonna come stringa vuota o multicarattere è supportato solo per il mapping del flusso di dati ma non per l'attività di copia.  | No       |
| rowDelimiter     | Singolo carattere o "\r\n" usato per separare le righe in un file. <br>Il valore predefinito è uno dei seguenti valori **in lettura: ["\r\n", ",", "\n"]** e **"\n" o "\r\n" in scrittura** mediante mapping del flusso di dati e dell'attività di copia rispettivamente. <br>Quando il delimitatore di riga è impostato su nessun delimitatore (stringa vuota), il delimitatore di colonna deve essere impostato come nessun delimitatore (stringa vuota), ovvero trattare l'intero contenuto come valore singolo.<br>Attualmente, il delimitatore di riga come stringa vuota è supportato solo per il mapping del flusso di dati, ma non per l'attività di copia. | No       |
| quoteChar        | Carattere singolo per citare i valori di colonna se contiene un delimitatore di colonna. <br>Il valore predefinito è **virgolette doppie** `"` . <br>Quando `quoteChar` viene definito come stringa vuota, significa che non sono presenti virgolette e il valore della colonna non è racchiuso tra virgolette e `escapeChar` viene usato per eseguire l'escape del delimitatore di colonna e di se stesso. | No       |
| escapeChar       | Carattere singolo per l'escape delle virgolette all'interno di un valore racchiuso tra virgolette.<br>Il valore predefinito è **la `\` barra rovesciata **. <br>Quando `escapeChar` viene definito come stringa vuota, `quoteChar` deve essere impostato anche come stringa vuota, nel qual caso assicurarsi che tutti i valori di colonna non contengano delimitatori. | No       |
| firstRowAsHeader | Specifica se trattare o rendere la prima riga come riga di intestazione con i nomi delle colonne.<br>I valori consentiti sono **true** e **false** (impostazione predefinita).<br>Quando la prima riga come intestazione è false, nota l'anteprima dei dati dell'interfaccia utente e l'output dell'attività di ricerca generano automaticamente i nomi di colonna come Prop_ {n} (a partire da 0), l'attività di copia richiede il [mapping esplicito](copy-activity-schema-and-type-mapping.md#explicit-mapping) dall'origine al sink e individua le colonne in base al numero ordinale (a partire da 1) ed elenca le colonne con nome come Column_  | No       |
| nullValue        | Specifica la rappresentazione di stringa del valore null. <br>Il valore predefinito è una **stringa vuota**. | No       |
| encodingName     | Tipo di codifica utilizzato per leggere/scrivere file di test. <br>I valori consentiti sono i seguenti: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Nota il flusso di dati del mapping non supporta la codifica UTF-7. | No       |
| compressionCodec | Codec di compressione utilizzato per leggere/scrivere file di testo. <br>I valori consentiti sono **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **Snapper**o **LZ4**. Il valore predefinito non è compresso. <br>**Nota** attualmente l'attività di copia non supporta "blocco" & "LZ4" e il flusso di dati di mapping non supporta "ZipDeflate". <br>**Nota** quando si usa l'attività di copia per decomprimere i file TarGzip di **ZipDeflate** / **TarGzip** e scrivere nell'archivio dati sink basato su file, per impostazione predefinita i file vengono estratti nella cartella: `<path specified in dataset>/<folder named as source compressed file>/` , usare `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` nell' [origine dell'attività di copia](#delimited-text-as-source) per controllare se mantenere il nome dei file compressi come struttura di cartelle. | No       |
| compressionLevel | Rapporto di compressione. <br>I valori consentiti sono **ottimali** o più **veloci**.<br>- Più **veloce:** L'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non è compresso in modo ottimale.<br>- **Ottimale**: l'operazione di compressione deve essere compressa in modo ottimale, anche se il completamento dell'operazione richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Di seguito è riportato un esempio di set di dati di testo delimitato nell'archivio BLOB di Azure:

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di testo delimitati.

### <a name="delimited-text-as-source"></a>Testo delimitato come origine 

Nella sezione *** \* origine \* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà       | Descrizione                                                  | Obbligatoria |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **DelimitedTextSource**. | Sì      |
| formatSettings | Gruppo di proprietà. Vedere la tabella **delle impostazioni di lettura testo delimitate** . |  No       |
| storeSettings  | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings` . | No       |

**Impostazioni di lettura testo delimitato** supportate in `formatSettings` :

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Il tipo di formatSettings deve essere impostato su **DelimitedTextReadSettings**. | Sì      |
| skipLineCount | Indica il numero di righe **non vuote** da ignorare durante la lettura di dati da file di input. <br>Se vengono specificati sia skipLineCount che firstRowAsHeader, le righe vengono ignorate e quindi le informazioni dell'intestazione vengono lette dal file di input. | No       |
| compressionProperties | Gruppo di proprietà su come decomprimere i dati per un determinato codec di compressione. | No       |
| preserveZipFileNameAsFolder<br>(*in `compressionProperties` -> `type` As `ZipDeflateReadSettings` *) |  Si applica quando il set di dati di input viene configurato con la compressione **ZipDeflate** . Indica se mantenere il nome del file zip di origine come struttura di cartelle durante la copia.<br>-Se impostato su **true (impostazione predefinita)**, Data Factory scrive file decompressi in `<path specified in dataset>/<folder named as source zip file>/` .<br>-Se impostato su **false**, Data Factory scrive i file decompressi direttamente in `<path specified in dataset>` . Assicurarsi che non siano presenti nomi di file duplicati in file zip di origine diversi per evitare la competizione o un comportamento imprevisto.  | No |
| preserveCompressionFileNameAsFolder<br>(*in `compressionProperties` -> `type` As `TarGZipReadSettings` *)  | Si applica quando il set di dati di input viene configurato con la compressione **TarGzip** . Indica se mantenere il nome file compresso di origine come struttura di cartelle durante la copia.<br>-Se impostato su **true (impostazione predefinita)**, Data Factory scrive i file decompressi in `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Se impostato su **false**, Data Factory scrive i file decompressi direttamente in `<path specified in dataset>` . Assicurarsi che non siano presenti nomi di file duplicati in file di origine diversi per evitare la competizione o un comportamento imprevisto. | No |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
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

### <a name="delimited-text-as-sink"></a>Testo delimitato come sink

Nella sezione *** \* sink \* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà       | Descrizione                                                  | Obbligatoria |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **DelimitedTextSink**. | Sì      |
| formatSettings | Gruppo di proprietà. Vedere la tabella **delle impostazioni di scrittura testo delimitate** . |    No      |
| storeSettings  | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate in `storeSettings` .  | No       |

**Impostazioni di scrittura testo delimitate** supportate in `formatSettings` :

| Proprietà      | Descrizione                                                  | Obbligatoria                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Il tipo di formatSettings deve essere impostato su **DelimitedTextWriteSettings**. | Sì                                                   |
| fileExtension | Estensione di file utilizzata per assegnare un nome ai file di output, ad esempio, `.csv` `.txt` . Deve essere specificato quando l'oggetto `fileName` non è specificato nel set di dati DelimitedText di output. Quando il nome del file viene configurato nel set di dati di output, verrà usato come nome del file di sink e l'impostazione dell'estensione di file verrà ignorata.  | Sì quando il nome file non è specificato nel set di dati di output |
| maxRowsPerFile | Quando si scrivono dati in una cartella, è possibile scegliere di scrivere in più file e specificare il numero massimo di righe per ogni file.  | No |
| fileNamePrefix | Applicabile quando `maxRowsPerFile` è configurato.<br> Specificare il prefisso del nome file durante la scrittura di dati in più file. questo modello è risultato: `<fileNamePrefix>_00000.<fileExtension>` . Se non è specificato, il prefisso del nome file verrà generato automaticamente. Questa proprietà non viene applicata quando l'origine è un archivio dati basato su file o sull' [opzione partition-Enabled](copy-activity-performance-features.md).  | No |

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Nel mapping di flussi di dati è possibile leggere e scrivere in formato testo delimitato negli archivi dati seguenti: [archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Proprietà origine

Nella tabella seguente sono elencate le proprietà supportate da un'origine testo delimitata. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Percorsi Wild Card | Verranno elaborati tutti i file corrispondenti al percorso con caratteri jolly. Sostituisce la cartella e il percorso del file impostati nel set di dati. | no | String[] | wildcardPaths |
| Partition Root Path (Percorso radice partizione) | Per i dati di file partizionati, è possibile immettere un percorso radice della partizione per leggere le cartelle partizionate come colonne | no | string | partitionRootPath |
| Elenco di file | Indica se l'origine sta puntando a un file di testo che elenca i file da elaborare | no | `true` o `false` | fileList |
| Righe su più righe | Il file di origine contiene righe che si estendono su più righe. I valori multiriga devono essere racchiusi tra virgolette. | No `true` o `false` | multiLineRow |
| Colonna in cui archiviare il nome del file | Crea una nuova colonna con il nome e il percorso del file di origine | no | string | rowUrlColumn |
| Al termine | Elimina o sposta i file dopo l'elaborazione. Il percorso del file inizia dalla radice del contenitore | no | Elimina: `true` o `false` <br> Spostare `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtra per Ultima modifica | Scegliere di filtrare i file in base alla data dell'Ultima modifica | no | Timestamp | modifiedAfter <br> modifiedBefore |
| Consenti nessun file trovato | Se true, non viene generato alcun errore se non viene trovato alcun file | no | `true` o `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Esempio di origine

L'immagine seguente è un esempio di una configurazione di origine testo delimitata nel mapping dei flussi di dati.

![Origine DelimitedText](media/data-flow/delimited-text-source.png)

Lo script del flusso di dati associato è:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

### <a name="sink-properties"></a>Proprietà sink

Nella tabella seguente sono elencate le proprietà supportate da un sink di testo delimitato. È possibile modificare queste proprietà nella scheda **Impostazioni** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Cancella la cartella | Se la cartella di destinazione viene cancellata prima della scrittura | no | `true` o `false` | truncate |
| Opzione nome file | Formato di denominazione dei dati scritti. Per impostazione predefinita, un file per partizione è nel formato `part-#####-tid-<guid>` | no | Modello: stringa <br> Per partizione: stringa [] <br> Come dati in column: String <br> Output in un singolo file: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Virgolette tutte | Racchiudere tutti i valori tra virgolette | no | `true` o `false` | quoteAll |

### <a name="sink-example"></a>Esempio di sink

Nell'immagine seguente è riportato un esempio di configurazione del sink di testo delimitato nei flussi di dati di mapping.

![Sink DelimitedText](media/data-flow/delimited-text-sink.png)

Lo script del flusso di dati associato è:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
