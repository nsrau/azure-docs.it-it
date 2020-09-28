---
title: Formato ORC in Azure Data Factory
description: In questo argomento viene descritto come gestire il formato ORC in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: 9e6b8511164cd7e9a855a70d9edba4ce6492c3a3
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91404723"
---
# <a name="orc-format-in-azure-data-factory"></a>Formato ORC in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Seguire questo articolo quando si desidera **analizzare i file ORC o scrivere i dati in formato ORC**. 

Il formato ORC è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati ORC.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **ORC**. | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location` . **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati**. | Sì      |
| compressionCodec         | Codec di compressione da usare durante la scrittura nei file ORC. Durante la lettura da file ORC, Data Factory determina automaticamente il codec di compressione basato sui metadati del file.<br>I tipi supportati sono **None**, **zlib**, **Snapper** (impostazione predefinita) e **LZO**. Nota Attualmente l'attività di copia non supporta LZO quando si leggono/scrivono file ORC. | No      |

Di seguito è riportato un esempio di set di dati ORC nell'archivio BLOB di Azure:

```json
{
    "name": "OrcDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Tenere presente quanto segue:

* I tipi di dati complessi non sono supportati (STRUCT, MAP, LIST, UNION).
* Spazi vuoti nel nome della colonna non sono supportati.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink ORC.

### <a name="orc-as-source"></a>ORC come origine

Nella sezione *** \* origine \* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **OrcSource**. | Sì      |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

### <a name="orc-as-sink"></a>ORCO come sink

Nella sezione *** \* sink \* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type del sink dell'attività di copia deve essere impostata su **OrcSink**. | Sì      |
| formatSettings | Gruppo di proprietà. Vedere la tabella **delle impostazioni di scrittura ORC** riportata di seguito. |    No      |
| storeSettings | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

**Impostazioni di scrittura ORC** supportate in `formatSettings` :

| Proprietà      | Descrizione                                                  | Obbligatoria                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Il tipo di formatSettings deve essere impostato su **OrcWriteSettings**. | Sì                                                   |
| maxRowsPerFile | Quando si scrivono dati in una cartella, è possibile scegliere di scrivere in più file e specificare il numero massimo di righe per ogni file.  | No |
| fileNamePrefix | Applicabile quando `maxRowsPerFile` è configurato.<br> Specificare il prefisso del nome file durante la scrittura di dati in più file. questo modello è risultato: `<fileNamePrefix>_00000.<fileExtension>` . Se non è specificato, il prefisso del nome file verrà generato automaticamente. Questa proprietà non viene applicata quando l'origine è un archivio dati basato su file o sull' [opzione partition-Enabled](copy-activity-performance-features.md).  | No |

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Per eseguire il mapping dei flussi di dati, è possibile leggere e scrivere nel formato ORC negli archivi dati seguenti: [archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

È possibile puntare ai file ORC usando il set di dati ORC o usando un [set di dati inline](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Proprietà origine

La tabella seguente elenca le proprietà supportate da un'origine ORC. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

Quando si usa il set di dati inline, verranno visualizzate impostazioni file aggiuntive, che corrispondono alle proprietà descritte nella sezione [Proprietà set di dati](#dataset-properties) .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Il formato deve essere `orc` | sì | `orc` | format |
| Percorsi Wild Card | Verranno elaborati tutti i file corrispondenti al percorso con caratteri jolly. Sostituisce la cartella e il percorso del file impostati nel set di dati. | no | String[] | wildcardPaths |
| Partition Root Path (Percorso radice partizione) | Per i dati di file partizionati, è possibile immettere un percorso radice della partizione per leggere le cartelle partizionate come colonne | no | string | partitionRootPath |
| Elenco di file | Indica se l'origine sta puntando a un file di testo che elenca i file da elaborare | no | `true` o `false` | fileList |
| Colonna in cui archiviare il nome del file | Crea una nuova colonna con il nome e il percorso del file di origine | no | string | rowUrlColumn |
| Al termine | Elimina o sposta i file dopo l'elaborazione. Il percorso del file inizia dalla radice del contenitore | no | Elimina: `true` o `false` <br> Spostare `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Filtra per Ultima modifica | Scegliere di filtrare i file in base alla data dell'Ultima modifica | no | Timestamp | modifiedAfter <br> modifiedBefore |
| Consenti nessun file trovato | Se true, non viene generato alcun errore se non viene trovato alcun file | no | `true` o `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Esempio di origine

Lo script del flusso di dati associato di una configurazione di origine ORC è:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'orc') ~> OrcSource
```

### <a name="sink-properties"></a>Proprietà sink

La tabella seguente elenca le proprietà supportate da un sink ORC. È possibile modificare queste proprietà nella scheda **Impostazioni** .

Quando si usa il set di dati inline, verranno visualizzate impostazioni file aggiuntive, che corrispondono alle proprietà descritte nella sezione [Proprietà set di dati](#dataset-properties) .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Il formato deve essere `orc` | sì | `orc` | format |
| Cancella la cartella | Se la cartella di destinazione viene cancellata prima della scrittura | no | `true` o `false` | truncate |
| Opzione nome file | Formato di denominazione dei dati scritti. Per impostazione predefinita, un file per partizione è nel formato `part-#####-tid-<guid>` | no | Modello: stringa <br> Per partizione: stringa [] <br> Come dati in column: String <br> Output in un singolo file: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>Esempio di sink

Lo script del flusso di dati associato di una configurazione di sink ORC è:

```
OrcSource sink(
    format: 'orc',
    filePattern:'output[n].orc',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> OrcSink
```

## <a name="using-self-hosted-integration-runtime"></a>Uso di Integration Runtime self-hosted

> [!IMPORTANT]
> Per la copia abilitata da self-hosted Integration Runtime ad esempio tra archivi dati locali e cloud, se non si esegue la copia dei file ORC **così come sono**, è necessario installare il pacchetto **64-bit JRE 8 (Java Runtime Environment) o OpenJDK** e **Microsoft Visual C++ 2010 ridistribuibile** nel computer IR. Per altri dettagli, vedere il paragrafo seguente.

Per la copia in esecuzione sul runtime di integrazione self-hosted con la serializzazione/deserializzazione dei file ORC, ADF individua il runtime Java controllando prima di tutto il registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* di sistema per JRE, se non è stato trovato, controllando in secondo luogo la variabile *`JAVA_HOME`* di sistema per OpenJDK.

- **Per usare JRE**: il runtime di integrazione a 64 bit richiede jre a 64 bit. disponibile [qui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Per usare OpenJDK**: è supportata dalla versione 3,13 di IR. Includere jvm.dll in un pacchetto con tutti gli altri assembly necessari di OpenJDK nel computer del runtime di integrazione self-hosted e impostare di conseguenza la variabile di ambiente di sistema JAVA_HOME.
- **Per installare Visual C++ pacchetto ridistribuibile 2010**: Visual C++ 2010 Redistributable Package non è installato con installazioni di runtime di integrazione self-hosted. disponibile [qui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se si copiano dati da e verso il formato ORC usando Integration Runtime indipendenti e si verifica un errore indicante che si è verificato un errore durante la chiamata a Java, messaggio: **java. lang. OutOfMemoryError: spazio heap Java**, è possibile aggiungere una variabile di ambiente `_JAVA_OPTIONS` nel computer che ospita il runtime di integrazione self-hosted per modificare le dimensioni dell'heap min/max per JVM in modo da consentire la

![Impostare le dimensioni dell'heap JVM nel runtime di integrazione self-hosted](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Esempio: impostare la variabile `_JAVA_OPTIONS` con il valore `-Xms256m -Xmx16g`. Il flag `Xms` specifica il pool di allocazione della memoria iniziale per Java Virtual Machine (JVM), mentre `Xmx` specifica il pool di allocazione della memoria massima. JVM verrà quindi avviato con una quantità di memoria pari a `Xms` e potrà usare una quantità massima di memoria pari a `Xmx`. Per impostazione predefinita, ADF USA min 64 MB e Max 1G.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
