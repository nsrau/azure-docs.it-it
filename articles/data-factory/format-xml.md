---
title: Formato XML in Azure Data Factory
description: In questo argomento viene descritto come gestire il formato XML in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: 2ca13ef3c6fa5bbd40ee239c50a023beee85a977
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637174"
---
# <a name="xml-format-in-azure-data-factory"></a>Formato XML in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Seguire questo articolo quando si desidera **analizzare i file XML** . 

Il formato XML è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md). È supportato come origine ma non come sink.

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati XML.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **XML** . | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location` . **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati** . | Sì      |
| encodingName     | Tipo di codifica utilizzato per leggere/scrivere file di test. <br>I valori consentiti sono i seguenti: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| No       |
| nullValue | Specifica la rappresentazione di stringa del valore null.<br/>Il valore predefinito è una **stringa vuota** . | No |
| compressione | Gruppo di proprietà per configurare la compressione dei file. Configurare questa sezione quando si desidera eseguire la compressione/decompressione durante l'esecuzione dell'attività. | No |
| type<br>( *in `compression`* ) | Codec di compressione utilizzato per leggere/scrivere file XML. <br>I valori consentiti sono **bzip2** , **gzip** , **deflate** , **ZipDeflate** , **TarGzip** , **Snapper** o **LZ4** . Il valore predefinito non è compresso.<br>**Nota** attualmente l'attività di copia non supporta "blocco" & "LZ4" e il flusso di dati di mapping non supporta "ZipDeflate".<br>**Nota** quando si usa l'attività di copia per decomprimere i file TarGzip di **ZipDeflate** / **TarGzip** e scrivere nell'archivio dati sink basato su file, per impostazione predefinita i file vengono estratti nella cartella: `<path specified in dataset>/<folder named as source compressed file>/` , usare `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` nell' [origine dell'attività di copia](#xml-as-source) per controllare se mantenere il nome dei file compressi come struttura di cartelle. | No.  |
| livello<br/>( *in `compression`* ) | Rapporto di compressione. <br>I valori consentiti sono **ottimali** o più **veloci** .<br>- Più **veloce:** L'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non è compresso in modo ottimale.<br>- **Ottimale** : l'operazione di compressione deve essere compressa in modo ottimale, anche se il completamento dell'operazione richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](/dotnet/api/system.io.compression.compressionlevel) . | No       |

Di seguito è riportato un esempio di set di dati XML nell'archivio BLOB di Azure:

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione viene fornito un elenco delle proprietà supportate dall'origine XML.

Informazioni su come eseguire il mapping dei dati XML e del formato/archivio dati sink dal [mapping dello schema](copy-activity-schema-and-type-mapping.md). Quando si visualizza in anteprima i file XML, i dati vengono visualizzati con la gerarchia JSON e si usa il percorso JSON per puntare ai campi.

### <a name="xml-as-source"></a>XML come origine

Nella sezione attività di copia **_ \_ source \*** * sono supportate le proprietà seguenti. Scopri di più sul [comportamento del connettore XML](#xml-connector-behavior).

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **xmlSource** . | Sì      |
| formatSettings | Gruppo di proprietà. Vedere la tabella **delle impostazioni di lettura XML** riportata di seguito. | No       |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia** . | No       |

**Impostazioni di lettura XML** supportate in `formatSettings` :

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Il tipo di formatSettings deve essere impostato su **Proprietà XmlReadSettings** . | Sì      |
| validationMode | Specifica se convalidare il XML Schema.<br>I valori consentiti sono **None** (impostazione predefinita, nessuna convalida), **xsd** (Validate Using XSD), **DTD** (Validate Using DTD). | No |
| spazi dei nomi | Indica se abilitare lo spazio dei nomi durante l'analisi dei file XML. I valori consentiti sono: **true** (predefinito), **false** . | No |
| namespacePrefixes | URI dello spazio dei nomi da anteporre al mapping, che viene usato per assegnare un nome ai campi durante l'analisi del file XML.<br/>Se per un file XML sono abilitati lo spazio dei nomi e lo spazio dei nomi, per impostazione predefinita il nome del campo corrisponde a quello presente nel documento XML.<br>Se è presente un elemento definito per l'URI dello spazio dei nomi in questa mappa, il nome del campo è `prefix:fieldName` . | No |
| detectDataType | Indica se rilevare i tipi di dati Integer, Double e Boolean. I valori consentiti sono: **true** (predefinito), **false** .| No |
| compressionProperties | Gruppo di proprietà su come decomprimere i dati per un determinato codec di compressione. | No       |
| preserveZipFileNameAsFolder<br>( *in `compressionProperties` -> `type` As `ZipDeflateReadSettings`* )  | Si applica quando il set di dati di input viene configurato con la compressione **ZipDeflate** . Indica se mantenere il nome del file zip di origine come struttura di cartelle durante la copia.<br>-Se impostato su **true (impostazione predefinita)** , Data Factory scrive file decompressi in `<path specified in dataset>/<folder named as source zip file>/` .<br>-Se impostato su **false** , Data Factory scrive i file decompressi direttamente in `<path specified in dataset>` . Assicurarsi che non siano presenti nomi di file duplicati in file zip di origine diversi per evitare la competizione o un comportamento imprevisto.  | No |
| preserveCompressionFileNameAsFolder<br>( *in `compressionProperties` -> `type` As `TarGZipReadSettings`* ) | Si applica quando il set di dati di input viene configurato con la compressione **TarGzip** . Indica se mantenere il nome file compresso di origine come struttura di cartelle durante la copia.<br>-Se impostato su **true (impostazione predefinita)** , Data Factory scrive i file decompressi in `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Se impostato su **false** , Data Factory scrive i file decompressi direttamente in `<path specified in dataset>` . Assicurarsi che non siano presenti nomi di file duplicati in file di origine diversi per evitare la competizione o un comportamento imprevisto. | No |

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

In mapping di flussi di dati è possibile leggere e scrivere in formato XML negli archivi dati seguenti: [archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). È possibile puntare a file XML usando un set di dati XML o un [set di dati inline](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Proprietà origine

Nella tabella seguente sono elencate le proprietà supportate da un'origine XML. È possibile modificare queste proprietà nella scheda **Opzioni di origine** . Scopri di più sul [comportamento del connettore XML](#xml-connector-behavior). Quando si usa il set di dati inline, verranno visualizzate impostazioni file aggiuntive, che corrispondono alle proprietà descritte nella sezione [Proprietà set di dati](#dataset-properties) . 

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Percorsi Wild Card | Verranno elaborati tutti i file corrispondenti al percorso con caratteri jolly. Sostituisce la cartella e il percorso del file impostati nel set di dati. | No | String[] | wildcardPaths |
| Partition Root Path (Percorso radice partizione) | Per i dati di file partizionati, è possibile immettere un percorso radice della partizione per leggere le cartelle partizionate come colonne | No | string | partitionRootPath |
| Elenco di file | Indica se l'origine sta puntando a un file di testo che elenca i file da elaborare | No | `true` o `false` | fileList |
| Colonna in cui archiviare il nome del file | Crea una nuova colonna con il nome e il percorso del file di origine | No | string | rowUrlColumn |
| Al termine | Elimina o sposta i file dopo l'elaborazione. Il percorso del file inizia dalla radice del contenitore | No | Elimina: `true` o `false` <br> Spostare `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| Filtra per Ultima modifica | Scegliere di filtrare i file in base alla data dell'Ultima modifica | No | Timestamp | modifiedAfter <br>modifiedBefore |
| Modalità di convalida | Specifica se convalidare il XML Schema. | No | `None` (impostazione predefinita, nessuna convalida)<br>`xsd` (convalida con XSD)<br>`dtd` (convalida tramite DTD). | validationMode |
| Spazi dei nomi | Indica se abilitare lo spazio dei nomi durante l'analisi dei file XML. | No | `true` (impostazione predefinita) o `false` | spazi dei nomi |
| Coppie di prefisso dello spazio dei nomi | URI dello spazio dei nomi da anteporre al mapping, che viene usato per assegnare un nome ai campi durante l'analisi del file XML.<br/>Se per un file XML sono abilitati lo spazio dei nomi e lo spazio dei nomi, per impostazione predefinita il nome del campo corrisponde a quello presente nel documento XML.<br>Se è presente un elemento definito per l'URI dello spazio dei nomi in questa mappa, il nome del campo è `prefix:fieldName` . | No | Matrice con modello`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| Consenti nessun file trovato | Se true, non viene generato alcun errore se non viene trovato alcun file | no | `true` o `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>Esempio di script di origine XML

Lo script seguente è un esempio di configurazione di un'origine XML nel mapping dei flussi di dati tramite la modalità del set di dati.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

Lo script seguente è un esempio di configurazione di un'origine XML che usa la modalità del set di dati inline.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>Comportamento del connettore XML

Quando si usa XML come origine, tenere presente quanto segue.

- Attributi XML:

    - Gli attributi di un elemento vengono analizzati come sottocampi dell'elemento nella gerarchia.
    - Il nome del campo attributo segue il modello `@attributeName` .

- Convalida XML Schema:

    - È possibile scegliere di non convalidare lo schema o di convalidare lo schema utilizzando XSD o DTD.
    - Quando si utilizza XSD o DTD per convalidare i file XML, è necessario fare riferimento alla definizione XSD/DTD all'interno dei file XML tramite il percorso relativo.

- Gestione dello spazio dei nomi:

    - Lo spazio dei nomi può essere disabilitato quando si usa il flusso di dati, nel qual caso gli attributi che definiscono lo spazio dei nomi verranno analizzati come attributi normali.
    - Quando lo spazio dei nomi è abilitato, i nomi degli elementi e degli attributi seguono il criterio       `namespaceUri,elementName` e `namespaceUri,@attributeName` per impostazione predefinita. È possibile definire il prefisso dello spazio dei nomi per ogni URI dello spazio dei nomi nell'origine, nel qual caso i nomi dell'elemento e degli attributi seguono invece il modello `definedPrefix:elementName` `definedPrefix:@attributeName` .

- Colonna valore:

    - Se un elemento XML dispone sia di un valore di testo semplice che di attributi/elementi figlio, il valore di testo semplice viene analizzato come valore di una "colonna valore" con il nome del campo predefinito `_value_` . Eredita anche lo spazio dei nomi dell'elemento, se applicabile.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)