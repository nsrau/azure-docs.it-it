---
title: Copiare dati da Amazon Simple Storage Service (S3)
description: Informazioni su come copiare dati da Amazon Simple Storage Service (S3) in archivi dati di sink supportati usando Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 023d6734195dabefff12210c2e63a0a4f4f9ac93
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007674"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Copiare i dati dal servizio di archiviazione semplice di Amazon usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare la versione del servizio di Azure Data Factory in uso:"]
>
> * [Versione 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versione corrente](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come copiare dati da Amazon Simple Storage Service (Amazon S3). Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

>[!TIP]
>Per altre informazioni sullo scenario di migrazione dei dati da Amazon S3 ad archiviazione di Azure, vedere [usare Azure Data Factory per eseguire la migrazione dei dati da Amazon S3 ad archiviazione di Azure](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore Amazon S3 è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Delete](delete-activity.md)

In particolare, questo connettore Amazon S3 supporta la copia dei file così come sono o l'analisi di file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md). È anche possibile scegliere di [mantenere i metadati del file durante la copia](#preserve-metadata-during-copy). Il connettore usa [AWS Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) per autenticare le richieste a S3.

>[!TIP]
>È possibile usare questo connettore Amazon S3 per copiare i dati da *qualsiasi provider di archiviazione compatibile con S3*, ad esempio [Google Cloud Storage](connector-google-cloud-storage.md). Specificare l'URL del servizio corrispondente nella configurazione del servizio collegato.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per copiare dati da Amazon S3, assicurarsi di avere ottenuto le autorizzazioni seguenti:

- **Per l'esecuzione dell'attività di copia**: `s3:GetObject` e `s3:GetObjectVersion` per le operazioni di oggetto Amazon S3.
- **Per data factory la creazione dell'interfaccia utente grafica**: `s3:ListAllMyBuckets` e `s3:ListBucket` / `s3:GetBucketLocation` per le operazioni bucket di Amazon S3. Le autorizzazioni sono necessarie anche per operazioni quali il test delle connessioni e l'esplorazione dei percorsi di file. Se non si desidera concedere queste autorizzazioni, ignorare la connessione di test nella pagina di creazione del servizio collegato e specificare il percorso direttamente nelle impostazioni del set di dati.

Per l'elenco completo delle autorizzazioni di Amazon S3, vedere [specifica delle autorizzazioni in un criterio](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) nel sito AWS.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Amazon S3.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per un servizio collegato Amazon S3 sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **AmazonS3**. | Sì |
| accessKeyId | ID della chiave di accesso segreta. |Sì |
| secretAccessKey | La stessa chiave di accesso segreta. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| serviceUrl | Specificare l'endpoint S3 personalizzato se si copiano dati da un provider di archiviazione compatibile con S3 diverso dal servizio ufficiale Amazon S3. Per copiare dati da Google Cloud Storage, ad esempio, specificare `https://storage.googleapis.com`. | No |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, il servizio usa il runtime di integrazione di Azure predefinito. |No |

Questo connettore richiede le chiavi di accesso per un account di gestione delle identità e degli accessi AWS (IAM) per copiare i dati da Amazon S3. Le [credenziali di sicurezza temporanee](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) non sono ora supportate.

>[!TIP]
>Specificare l'URL del servizio S3 personalizzato se si copiano dati da una risorsa di archiviazione compatibile con S3 diversa dal servizio ufficiale Amazon S3.

Ecco un esempio:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Amazon S3 in `location` impostazioni in un set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà **Type** in `location` in un set di dati deve essere impostata su **AmazonS3Location**. | Sì      |
| bucketName | Il nome del bucket S3.                                          | Sì      |
| folderPath | Percorso della cartella nel bucket specificato. Se si vuole usare un carattere jolly per filtrare la cartella, ignorare questa impostazione e specificare che nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file sotto il bucket e il percorso della cartella specificati. Se si vuole usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificare che nelle impostazioni dell'origine dell'attività. | No       |
| version | La versione dell'oggetto S3 se è stato abilitato il controllo delle versioni S3. Se non è specificato, verrà recuperata la versione più recente. |No |

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Amazon S3.

### <a name="amazon-s3-as-a-source-type"></a>Amazon S3 come tipo di origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Amazon S3 in `storeSettings` impostazioni in un'origine copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | La proprietà **Type** in `storeSettings` deve essere impostata su **AmazonS3ReadSettings**. | Sì                                                         |
| ***Individuare i file da copiare:*** |  |  |
| OPZIONE 1: percorso statico<br> | Copia dal percorso del bucket o della cartella/file specificato nel set di dati. Se si desidera copiare tutti i file da un bucket o da una cartella, specificare `wildcardFileName` anche come `*` . |  |
| OPZIONE 2: prefisso S3<br>- prefix | Prefisso per il nome della chiave S3 nel bucket specificato configurato in un set di dati per filtrare i file S3 di origine. Sono selezionate le chiavi S3 i cui nomi iniziano con `bucket_in_dataset/this_prefix` . Usa il filtro lato servizio S3, che offre prestazioni migliori rispetto a un filtro con caratteri jolly. | No |
| OPZIONE 3: carattere jolly<br>- wildcardFolderPath | Percorso della cartella con caratteri jolly nel bucket specificato configurati in un set di dati per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella contiene un carattere jolly o questo carattere di escape all'interno di. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| OPZIONE 3: carattere jolly<br>- wildcardFileName | Nome file con caratteri jolly sotto il bucket e il percorso della cartella specificati (oppure percorso di cartella con caratteri jolly) per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella contiene un carattere jolly o questo carattere di escape all'interno di.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì |
| OPZIONE 4: un elenco di file<br>- fileListPath | Indica di copiare un determinato set di file. Puntare a un file di testo che include un elenco di file da copiare, un file per riga, che rappresenta il percorso relativo del percorso configurato nel set di dati.<br/>Quando si usa questa opzione, non specificare un nome di file nel set di dati. Per altri esempi, vedere [Esempi di elenco di file](#file-list-examples). |No |
| ***Impostazioni aggiuntive:*** |  | |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando l'opzione **ricorsiva** è impostata su **true** e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. <br>I valori consentiti sono **true** (predefinito) e **false**.<br>Questa proprietà non è applicabile quando si configura `fileListPath`. |No |
| deleteFilesAfterCompletion | Indica se i file binari verranno eliminati dall'archivio di origine dopo che è stato eseguito il passaggio all'archivio di destinazione. L'eliminazione del file è per file, pertanto quando l'attività di copia ha esito negativo, si noterà che alcuni file sono già stati copiati nella destinazione ed eliminati dall'origine, mentre altri ancora rimangono nell'archivio di origine. <br/>Questa proprietà è valida solo nello scenario di copia binaria, in cui gli archivi di origini dati sono BLOB, ADLS Gen1, ADLS Gen2, S3, Google Cloud Storage, file, file di Azure, SFTP o FTP. Valore predefinito: false. |No |
| modifiedDatetimeStart    | I file vengono filtrati in base all'attributo: data Ultima modifica. <br>I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata a un fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere **null**, il che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è **null**, verranno selezionati i file il cui attributo Last modified è maggiore o uguale al valore DateTime.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è **null**, verranno selezionati i file il cui attributo Last modified è minore del valore DateTime.<br/>Questa proprietà non è applicabile quando si configura `fileListPath`. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                                          |
| maxConcurrentConnections | Numero di connessioni simultanee all'archivio dati. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No                                                          |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

Questa sezione descrive il comportamento risultante del percorso cartella e del nome del file con i filtri con caratteri jolly.

| bucket | Key | ricorsiva | Struttura delle cartelle di origine e risultato del filtro (i file in grassetto sono stati recuperati)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Esempi di elenco di file

In questa sezione viene descritto il comportamento risultante dell'utilizzo di un percorso dell'elenco di file in un'origine dell'attività di copia.

Si supponga di avere la seguente struttura di cartelle di origine e di voler copiare i file in grassetto:

| Esempio di struttura di origine                                      | Contenuto in FileListToCopy.txt                             | Configurazione di Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadati<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sottocartella1/File3.csv<br>Sottocartella1/File5.csv | **Nel set di dati:**<br>- Bucket: `bucket`<br>- Percorso cartella: `FolderA`<br><br>**In origine dell'attività di copia:**<br>- Percorso elenco file: `bucket/Metadata/FileListToCopy.txt` <br><br>Il percorso dell'elenco dei file punta a un file di testo nello stesso archivio dati che include un elenco di file da copiare, un file per riga, con il percorso relativo del percorso configurato nel set di dati. |

## <a name="preserve-metadata-during-copy"></a>Mantenere i metadati durante la copia

Quando si copiano i file da Amazon S3 a Azure Data Lake Storage Gen2 o all'archivio BLOB di Azure, è possibile scegliere di mantenere i metadati del file insieme ai dati. Altre informazioni in [Mantenere i metadati](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare l' [attività GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Proprietà dell'attività Delete

Per informazioni dettagliate sulle proprietà, controllare l' [attività di eliminazione](delete-activity.md).

## <a name="legacy-models"></a>Modalità legacy

>[!NOTE]
>I modelli seguenti sono ancora supportati come per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello indicato in precedenza. L'interfaccia utente di creazione Data Factory ha cambiato la generazione del nuovo modello.

### <a name="legacy-dataset-model"></a>Modello di set di dati legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del set di dati deve essere impostata su **AmazonS3Object**. |Sì |
| bucketName | Il nome del bucket S3. Il filtro con caratteri jolly non è supportato. |Sì per l'attività di copia o ricerca, no per l'attività GetMetadata |
| Key | Il nome o il filtro con caratteri jolly della chiave dell'oggetto S3 nel bucket specificato. Si applica solo quando la proprietà **Prefix** non è specificata. <br/><br/>Il filtro con caratteri jolly è supportato sia nella parte della cartella che nella parte del nome file. I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"key": "rootfolder/subfolder/*.csv"`<br/>- Esempio 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). Usare `^` per eseguire l'escape se la cartella o il nome file effettivo contiene un carattere jolly o questo carattere di escape all'interno di. |No |
| prefix | Il prefisso per la chiave dell'oggetto S3. Vengono selezionati gli oggetti le cui chiavi iniziano con questo prefisso. Si applica solo quando la proprietà **chiave** non è specificata. |No |
| version | La versione dell'oggetto S3 se è stato abilitato il controllo delle versioni S3. Se non si specifica una versione, verrà recuperata la versione più recente. |No |
| modifiedDatetimeStart | I file vengono filtrati in base all'attributo: data Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata in base al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che l'abilitazione di questa impostazione influirà sulle prestazioni complessive dello spostamento dei dati quando si desidera filtrare grandi quantità di file. <br/><br/> Le proprietà possono essere **null**, il che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è **null**, verranno selezionati i file il cui attributo Last modified è maggiore o uguale al valore DateTime.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, verranno selezionati i file il cui attributo Last modified è minore del valore DateTime.| No |
| modifiedDatetimeEnd | I file vengono filtrati in base all'attributo: data Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata in base al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che l'abilitazione di questa impostazione influirà sulle prestazioni complessive dello spostamento dei dati quando si desidera filtrare grandi quantità di file. <br/><br/> Le proprietà possono essere **null**, il che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è **null**, verranno selezionati i file il cui attributo Last modified è maggiore o uguale al valore DateTime.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è **null**, verranno selezionati i file il cui attributo Last modified è minore del valore DateTime.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato OCR](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare **bucketname** per il bucket e il **prefisso** della parte della cartella.
>
>Per copiare un singolo file con un determinato nome, specificare **bucketname** per il bucket e la **chiave** della parte della cartella più il nome del file.
>
>Per copiare un subset di file in una cartella, specificare **bucketname** per il bucket e la **chiave** della parte della cartella più il filtro con caratteri jolly.

**Esempio: uso del prefisso**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Esempio: uso della chiave e della versione (facoltativo)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-source-model-for-the-copy-activity"></a>Modello di origine legacy per l'attività di copia

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** dell'origine dell'attività di copia deve essere impostata su **FileSystemSource**. |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. **Si noti che quando è** impostato su **true** e il sink è un archivio basato su file, una cartella o una sottocartella vuota non verrà copiata o creata nel sink.<br/>I valori consentiti sono **true** (predefinito) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi all'archivio dati. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati dall'attività di copia in Azure Data Factory come origini e sink, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
