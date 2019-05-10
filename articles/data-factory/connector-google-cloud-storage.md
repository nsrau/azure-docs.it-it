---
title: Copiare dati da Google Cloud Storage usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da Google Cloud Storage in archivi dati di sink supportati usando Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9c9a4b41dbb9b9acc2982ae2af1f3a611f9d3beb
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228270"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Copiare dati da Google Cloud Storage usando Azure Data Factory

Questo articolo illustra come copiare dati da Google Cloud Storage. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di Google Cloud Storage è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [supportata matrice di origine/sink](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

In particolare, il connettore Google Cloud Storage supporta la copia dei file così come sono e l'analisi dei file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Per la copia dei dati da Google Cloud Storage viene usato il [connettore Amazon S3](connector-amazon-simple-storage-service.md) con il corrispondente endpoint S3 personalizzato, poiché Google Cloud Storage garantisce l'interoperabilità compatibile con S3.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per copiare i dati da Google Cloud Storage, assicurarsi di avere le autorizzazioni indicate di seguito:

- **Per l'esecuzione di attività di copia**: `s3:GetObject` e `s3:GetObjectVersion` per operazioni di oggetti.
- **Per la creazione di GUI di Data Factory**: `s3:ListAllMyBuckets` e `s3:ListBucket`/`s3:GetBucketLocation` per le operazioni di bucket sono inoltre necessarie le autorizzazioni per operazioni come la connessione di test e l'esplorazione/navigazione dei percorsi dei file. Se non si vuole concedere queste autorizzazioni, ignorare la connessione di test nella pagina di creazione del servizio collegato e specificare il percorso direttamente nelle impostazioni del set di dati.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità specifiche di Data Factory per il connettore Google Cloud Storage.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Google Cloud Storage sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AmazonS3**. | Sì |
| accessKeyId | ID della chiave di accesso segreta. Per trovare la chiave e il segreto di accesso, passare a **Google Cloud Storage** > **Impostazioni** > **Interoperabilità**. |Sì |
| secretAccessKey | La stessa chiave di accesso segreta. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| serviceUrl | Specificare l'endpoint S3 personalizzato come **`https://storage.googleapis.com`**. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |N. |

Di seguito è fornito un esempio: 

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

- Per la **Parquet e del formato di testo delimitato**, fare riferimento a [set di dati di formato Parquet e testo delimitato](#parquet-and-delimited-text-format-dataset) sezione.
- Per altri formati, ad esempio **formato ORC/Avro/JSON/binario**, fare riferimento a [altri set di dati di formato](#other-format-dataset) sezione.

### <a name="parquet-and-delimited-text-format-dataset"></a>Set di dati di formato parquet e testo delimitato

Per copiare dati da Google Cloud Storage nel **Parquet o formato di testo delimitato**, fare riferimento a [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articolo nel set di dati in base al formato e impostazioni supportate. Sono supportate le proprietà seguenti per l'archiviazione Cloud di Google in `location` impostazioni nel set di dati in base al formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà del tipo sotto `location` nel set di dati deve essere impostata su **AmazonS3Location**. | Sì      |
| bucketName | Il nome del bucket S3.                                          | Sì      |
| folderPath | Percorso cartella sotto il bucket specificato. Se si desidera utilizzare con caratteri jolly alla cartella di filtro, ignorare questa impostazione e specificare nelle impostazioni di origine di attività. | No        |
| fileName   | Il nome del file sotto il bucket specificato + folderPath. Se si desidera utilizzare con caratteri jolly per filtrare i file, ignorare questa impostazione e specificare nelle impostazioni di origine di attività. | N.       |

> [!NOTE]
> **AmazonS3Object** tipo set di dati con formato Parquet, Text indicato nella sezione successiva è ancora supportata come-per attività di copia/ricerca/GetMetadata per garantire la compatibilità con le versioni precedenti. Consigliabile per usare questo nuovo modello in futuro e Azure Data factory di creazione dell'interfaccia utente è stata attivata per la generazione di questi nuovi tipi.

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
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

### <a name="other-format-dataset"></a>Altri set di dati di formato

Per copiare dati da Google Cloud Storage nel **formato ORC/Avro/JSON/binario**, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **AmazonS3Object** |Sì |
| bucketName | Il nome del bucket S3. Il filtro con caratteri jolly non è supportato. |Sì per l'attività Copy/Lookup, no per l'attività GetMetadata |
| key | Il **nome o il filtro con caratteri jolly** della chiave dell'oggetto S3 nel bucket specificato. Si applica solo se la proprietà "prefix" non è specificata. <br/><br/>Il filtro con caratteri jolly è supportato sia per la parte della cartella che per la parte del nome file. I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"key": "rootfolder/subfolder/*.csv"`<br/>- Esempio 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). Usare `^` per applicare una sequenza di escape se il nome effettivo del file o della cartella include caratteri jolly o tale carattere escape. |No  |
| prefix | Il prefisso per la chiave dell'oggetto S3. Vengono selezionati gli oggetti le cui chiavi iniziano con questo prefisso. Si applica solo se la proprietà "key" non è specificata. |No  |
| version | La versione dell'oggetto S3 se è stato abilitato il controllo delle versioni S3. |N. |
| modifiedDatetimeStart | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No  |
| modifiedDatetimeEnd | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No  |
| format | Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](supported-file-formats-and-compression-codecs.md#text-format), [JsonFormat](supported-file-formats-and-compression-codecs.md#json-format), [AvroFormat](supported-file-formats-and-compression-codecs.md#avro-format), [OrcFormat](supported-file-formats-and-compression-codecs.md#orc-format) e [ParquetFormat](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compression | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono: **Optimal** (Ottimale) e **Fastest** (Più veloce). |No  |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare **bucketName** per il bucket e **prefix** per la parte della cartella.<br>Per copiare un singolo file con un nome specifico, specificare **bucketName** per il bucket e **key** per la parte della cartella più il nome file.<br>Per copiare un subset di file in una cartella, specificare **bucketName** per il bucket e **key** per la parte della cartella più il filtro con caratteri jolly.

**Esempio: uso del prefisso**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
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

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage come origine

- Per la copia da **Parquet e del formato di testo delimitato**, fare riferimento a [Parquet e testo delimitato formato origine](#parquet-and-delimited-text-format-source) sezione.
- Per la copia da altri formati, ad esempio **formato ORC/Avro/JSON/binario**, fare riferimento a [altra origine formato](#other-format-source) sezione.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e testo delimitato formato origine

Per copiare dati da Google Cloud Storage nel **Parquet o formato di testo delimitato**, fare riferimento a [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articolo sulle attività di copia in base al formato origine e le impostazioni supportate. Sono supportate le proprietà seguenti per l'archiviazione Cloud di Google in `storeSettings` le impostazioni di origine della copia in base al formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | La proprietà del tipo sotto `storeSettings` deve essere impostata su **AmazonS3ReadSetting**. | Sì                                                         |
| recursive                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No                                                           |
| prefix                   | Prefisso per la chiave dell'oggetto S3 sotto il bucket specificato configurato nel set di dati per filtrare gli oggetti di origine. Vengono selezionati gli oggetti le cui chiavi iniziano con questo prefisso. Si applica solo quando `wildcardFolderPath` e `wildcardFileName` non siano specificate proprietà. |                                                             |
| wildcardFolderPath       | Il percorso della cartella con caratteri jolly con il bucket specificato configurato nel set di dati nelle cartelle di origine di filtro. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                                           |
| wildcardFileName         | Il nome di file con caratteri jolly con il bucket specificato + folderPath/wildcardFolderPath per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Se Sì `fileName` nel set di dati e `prefix` non sono specificati |
| modifiedDatetimeStart    | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime. | No                                                           |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                                           |
| maxConcurrentConnections | Il numero delle connessioni per connettersi all'archivio di archiviazione simultaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | N.                                                          |

> [!NOTE]
> Per formato di testo delimitato/Parquet **FileSystemSource** origine dell'attività copy tipo indicato nella sezione successiva è ancora supportata come-sia per compatibilità con le versioni precedenti. Consigliabile per usare questo nuovo modello in futuro e Azure Data factory di creazione dell'interfaccia utente è stata attivata per la generazione di questi nuovi tipi.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
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

#### <a name="other-format-source"></a>Altra origine di formato

Per copiare dati da Google Cloud Storage nel **formato ORC/Avro/JSON/binario**, nell'attività di copia sono supportate le proprietà seguenti **origine** sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **FileSystemSource** |Sì |
| recursive | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che se recursive è impostata su true e il sink è un archivio basato su file, la cartella o la sottocartella vuota non verrà copiata o creata nel sink.<br/>I valori consentiti sono: **true** (predefinito), **false** | No  |
| maxConcurrentConnections | Il numero delle connessioni per connettersi all'archivio di archiviazione simultaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No  |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

Questa sezione descrive il comportamento risultante del percorso cartella e del nome del file con i filtri con caratteri jolly.

| bucket | key | recursive | Struttura delle cartelle di origine e risultato del filtro (i file in grassetto sono stati recuperati)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
