---
title: Copiare dati da Google Cloud Storage usando Azure Data Factory
description: Informazioni su come copiare dati da Google Cloud Storage in archivi dati di sink supportati usando Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: jingwang
ms.openlocfilehash: a916da121c8ffee1729ede6dd700ca4f6872fbf7
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043494"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>Copiare dati da Google Cloud Storage usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come copiare dati da Google Cloud Storage (GCS). Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore Google Cloud Storage è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Delete](delete-activity.md)

In particolare, questo connettore Google Cloud Storage supporta la copia dei file così come sono o l'analisi di file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md). Sfrutta i vantaggi dell'interoperabilità compatibile con S3 di GC.

## <a name="prerequisites"></a>Prerequisiti

L'installazione seguente è obbligatoria per l'account di archiviazione cloud di Google:

1. Abilitare l'interoperabilità per l'account di Google Cloud Storage
2. Impostare il progetto predefinito che contiene i dati che si desidera copiare dal bucket cataloghi globali di destinazione.
3. Creare un account del servizio e definire i livelli corretti di autorizzazioni usando la funzione IAM di cloud in GCP. 
4. Generare le chiavi di accesso per l'account del servizio.

![Recuperare la chiave di accesso per Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per copiare dati da Google Cloud Storage, assicurarsi di disporre delle autorizzazioni seguenti per le operazioni sugli oggetti: ` storage.objects.get` e ` storage.objects.list` .

Se si usa Data Factory interfaccia utente per creare, ` storage.buckets.list` è necessaria un'ulteriore autorizzazione per operazioni quali il test della connessione al servizio collegato e l'esplorazione dalla radice. Se non si vuole concedere questa autorizzazione, è possibile scegliere le opzioni "Test connessione a percorso file" o "Sfoglia da percorso specificato" dall'interfaccia utente.

Per l'elenco completo dei ruoli di archiviazione cloud di Google e delle autorizzazioni associate, vedere [ruoli IAM per l'archiviazione cloud](https://cloud.google.com/storage/docs/access-control/iam-roles) nel sito Google Cloud.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità specifiche di Data Factory per il connettore Google Cloud Storage.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per i servizi collegati di Google Cloud storage sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **GoogleCloudStorage**. | Sì |
| accessKeyId | ID della chiave di accesso segreta. Per trovare la chiave di accesso e il segreto, vedere [Prerequisiti](#prerequisites). |Sì |
| secretAccessKey | La stessa chiave di accesso segreta. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in data factory o [fare riferimento a un segreto archiviato nel Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| serviceUrl | Specificare l'endpoint GCS personalizzato come `https://storage.googleapis.com` . | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, il servizio usa il runtime di integrazione di Azure predefinito. |No |

Ecco un esempio:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Google Cloud storage in `location` impostazioni in un set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà **Type** in `location` nel set di dati deve essere impostata su **GoogleCloudStorageLocation**. | Sì      |
| bucketName | Nome del bucket GCS.                                          | Sì      |
| folderPath | Percorso della cartella nel bucket specificato. Se si vuole usare un carattere jolly per filtrare la cartella, ignorare questa impostazione e specificare che nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file sotto il bucket e il percorso della cartella specificati. Se si vuole usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificare che nelle impostazioni dell'origine dell'attività. | No       |

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
                "type": "GoogleCloudStorageLocation",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di archiviazione cloud di Google.

### <a name="google-cloud-storage-as-a-source-type"></a>Google Cloud Storage come tipo di origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Google Cloud storage in `storeSettings` impostazioni in un'origine copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | La proprietà **Type** in `storeSettings` deve essere impostata su **GoogleCloudStorageReadSettings**. | Sì                                                         |
| ***Individuare i file da copiare:*** |  |  |
| OPZIONE 1: percorso statico<br> | Copia dal percorso del bucket o della cartella/file specificato nel set di dati. Se si desidera copiare tutti i file da un bucket o da una cartella, specificare `wildcardFileName` anche come `*` . |  |
| OPZIONE 2: Prefisso GCS<br>- prefisso | Prefisso per il nome della chiave cataloghi globali nel bucket specificato configurata nel set di dati per filtrare i file GC di origine. Vengono selezionate le chiavi GC i cui nomi iniziano con `bucket_in_dataset/this_prefix` . Usa il filtro del lato servizio di GC, che offre prestazioni migliori rispetto a un filtro con caratteri jolly. | No |
| OPZIONE 3: carattere jolly<br>- wildcardFolderPath | Percorso della cartella con caratteri jolly nel bucket specificato configurati in un set di dati per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella contiene un carattere jolly o questo carattere di escape all'interno di. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| OPZIONE 3: carattere jolly<br>- wildcardFileName | Nome file con caratteri jolly sotto il bucket e il percorso della cartella specificati (oppure percorso di cartella con caratteri jolly) per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella contiene un carattere jolly o questo carattere di escape all'interno di.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì |
| OPZIONE 3: un elenco di file<br>- fileListPath | Indica di copiare un determinato set di file. Puntare a un file di testo che include un elenco di file da copiare, un file per riga, che rappresenta il percorso relativo del percorso configurato nel set di dati.<br/>Quando si usa questa opzione, non specificare il nome del file nel set di dati. Per altri esempi, vedere [Esempi di elenco di file](#file-list-examples). |No |
| ***Impostazioni aggiuntive:*** |  | |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando l'opzione **ricorsiva** è impostata su **true** e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. <br>I valori consentiti sono **true** (predefinito) e **false**.<br>Questa proprietà non è applicabile quando si configura `fileListPath`. |No |
| deleteFilesAfterCompletion | Indica se i file binari verranno eliminati dall'archivio di origine dopo che è stato eseguito il passaggio all'archivio di destinazione. L'eliminazione del file è per file, pertanto quando l'attività di copia ha esito negativo, si noterà che alcuni file sono già stati copiati nella destinazione ed eliminati dall'origine, mentre altri ancora rimangono nell'archivio di origine. <br/>Questa proprietà è valida solo nello scenario di copia di file binari. Valore predefinito: false. |No |
| modifiedDatetimeStart    | I file vengono filtrati in base all'attributo: data Ultima modifica. <br>I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata in base al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere **null**, il che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è **null**, verranno selezionati i file il cui attributo Last modified è maggiore o uguale al valore DateTime.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è **null**, verranno selezionati i file il cui attributo Last modified è minore del valore DateTime.<br/>Questa proprietà non è applicabile quando si configura `fileListPath`. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                                          |
| enablePartitionDiscovery | Per i file partizionati, specificare se analizzare le partizioni dal percorso del file e aggiungerle come colonne di origine aggiuntive.<br/>I valori consentiti sono **false** (impostazione predefinita) e **true**. | No                                            |
| partitionRootPath | Quando è abilitata l'individuazione delle partizioni, specificare il percorso radice assoluto per leggere le cartelle partizionate come colonne di dati.<br/><br/>Se non viene specificato, per impostazione predefinita<br/>-Quando si usa il percorso del file in un set di dati o un elenco di file nell'origine, il percorso radice della partizione è il percorso configurato nel set di dati.<br/>-Quando si usa il filtro di cartelle con caratteri jolly, il percorso radice della partizione è il percorso secondario prima del primo carattere jolly.<br/><br/>Si supponga, ad esempio, di configurare il percorso nel set di dati come "root/folder/Year = 2020/month = 08/Day = 27":<br/>-Se si specifica il percorso radice della partizione come "root/folder/Year = 2020", l'attività di copia genererà altre due colonne `month` e `day` con il valore "08" e "27", oltre alle colonne all'interno dei file.<br/>-Se il percorso radice della partizione non è specificato, non verrà generata alcuna colonna aggiuntiva. | No                                            |
| maxConcurrentConnections | Numero di connessioni simultanee all'archiviazione. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No                                                          |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "GoogleCloudStorageReadSettings",
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

In questa sezione viene descritto il comportamento risultante dell'utilizzo di un percorso elenco file nell'origine dell'attività di copia.

Si supponga di avere la seguente struttura di cartelle di origine e di voler copiare i file in grassetto:

| Esempio di struttura di origine                                      | Contenuto in FileListToCopy.txt                             | Configurazione di Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadati<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sottocartella1/File3.csv<br>Sottocartella1/File5.csv | **Nel set di dati:**<br>- Bucket: `bucket`<br>- Percorso cartella: `FolderA`<br><br>**Nell'origine dell'attività Copy:**<br>- Percorso elenco file: `bucket/Metadata/FileListToCopy.txt` <br><br>Il percorso dell'elenco dei file punta a un file di testo nello stesso archivio dati che include un elenco di file da copiare, un file per riga, con il percorso relativo del percorso configurato nel set di dati. |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare l' [attività GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Proprietà dell'attività Delete

Per informazioni dettagliate sulle proprietà, controllare l' [attività di eliminazione](delete-activity.md).

## <a name="legacy-models"></a>Modalità legacy

Se si usa un connettore Amazon S3 per copiare dati da Google Cloud Storage, questo è ancora supportato come per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello indicato in precedenza. L'interfaccia utente di creazione Data Factory ha cambiato la generazione del nuovo modello.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati dall'attività di copia in Azure Data Factory come origini e sink, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
