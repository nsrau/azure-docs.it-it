---
title: Copiare dati da e nel server SFTP
description: Informazioni su come copiare dati da e nel server SFTP usando Azure Data Factory.Learn about how to copy data from and to SFTP server by using Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/02/2020
ms.openlocfilehash: 06428d4a9c4a4178212d16d42b8b3adffb5c9718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250294"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>Copiare dati da e nel server SFTP usando Azure Data FactoryCopy data from and to SFTP server using Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-sftp-connector.md)
> * [Versione corrente](connector-sftp.md)

In questo articolo viene descritto come copiare dati da e nel server SFTP. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore SFTP è supportato per le seguenti attività:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Elimina attività](delete-activity.md)

In particolare, il connettore SFTP supporta:

- Copia di file da/su SFTP utilizzando l'autenticazione **Basic** o **SshPublicKey.**
- Copia di file così come sono o analisi/generazione di file con i formati di file e i [codec di compressione supportati.](supported-file-formats-and-compression-codecs.md)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per SFTP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SFTP sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Sftp**. |Sì |
| host | Nome o indirizzo IP del server SFTP. |Sì |
| port | Porta su cui è in ascolto il server SFTP.<br/>I valori consentiti sono: integer; il valore predefinito è **22**. |No |
| skipHostKeyValidation | Specificare se si desidera ignorare la convalida tramite della chiave host.<br/>I valori consentiti sono: **true**, **false** (predefinito).  | No |
| hostKeyFingerprint | Specificare le impronte digitali della chiave host. | Sì, se "skipHostKeyValidation" è impostato su false.  |
| authenticationType | Specificare il tipo di autenticazione.<br/>I valori consentiti sono: **Di base**, **SshPublicKey**. Fare riferimento a [Utilizzo dell'autenticazione di base](#using-basic-authentication) e Utilizzo dell'autenticazione a chiave pubblica [SSH](#using-ssh-public-key-authentication) sezioni rispettivamente su più proprietà ed esempi JSON. |Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per ulteriori informazioni, vedere la sezione [Prerequisiti.](#prerequisites) Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

### <a name="using-basic-authentication"></a>Uso dell'autenticazione di base

Per usare l'autenticazione di base, impostare la proprietà "authenticationType" su **Di base** e specificare le proprietà seguenti oltre a quelle generiche del connettore SFTP introdotte nell'ultima sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| userName | Utente che ha accesso al server SFTP. |Sì |
| password | Password per l'utente (userName). Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |

**Esempio:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-ssh-public-key-authentication"></a>Uso dell'autenticazione con chiave pubblica SSH

Per usare l'autenticazione basata su chiave pubblica SSH, impostare la proprietà "authenticationType" su **SshPublicKey** e specificare le proprietà seguenti oltre a quelle generiche del connettore SFTP introdotte nell'ultima sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| userName | Utente che ha accesso al server SFTP |Sì |
| privateKeyPath | Specificare il percorso assoluto del file di chiave privato a cui può accedere il runtime di integrazione. Si applica solo quando in "connectVia" viene specificato il runtime di integrazione di tipo self-hosted. | Specificare `privateKeyPath` o `privateKeyContent`.  |
| privateKeyContent | Contenuto della chiave privata SSH con codifica Base64. La chiave privata SSH deve essere in formato OpenSSH. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Specificare `privateKeyPath` o `privateKeyContent`. |
| passPhrase | Specificare la passphrase o la password per decrittografare la chiave privata se il file della chiave è protetto da una passphrase. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì se il file della chiave privata è protetto da una passphrase. |

> [!NOTE]
> Il connettore SFTP supporta chiavi OpenSSH RSA/DSA. Verificare che il contenuto del file di codice inizi con "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Se il file di codice privato è un file in formato ppk, usare lo strumento Putty per convertirlo dal formato ppk in formato OpenSSH. 

**Esempio 1: autenticazione SshPublicKey con chiave privata filePath**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: autenticazione SshPublicKey con contenuto della chiave privata**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di](concepts-datasets-linked-services.md) dati. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le seguenti proprietà sono supportate per SFTP nelle `location` impostazioni nel set di dati basato su formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà `location` type in nel set di dati deve essere impostata su **SftpLocation**. | Sì      |
| folderPath | Percorso della cartella. Se si desidera utilizzare il carattere jolly per filtrare la cartella, ignorare questa impostazione e specificare nelle impostazioni dell'origine attività. | No       |
| fileName   | Il nome del file sotto il folderPath specificato. Se si desidera utilizzare il carattere jolly per filtrare i file, ignorare questa impostazione e specificare nelle impostazioni dell'origine attività. | No       |

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SFTP.

### <a name="sftp-as-source"></a>SFTP come origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le seguenti proprietà sono supportate per SFTP nelle `storeSettings` impostazioni nell'origine di copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà `storeSettings` type in deve essere impostata su **SftpReadSettings**. | Sì                                           |
| ricorsiva                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (impostazione predefinita) e **false**. | No                                            |
| carattere jollyPercorsoCartella       | Percorso della cartella con caratteri jolly per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| carattere jollyNomefile         | Il nome del file con caratteri jolly sotto la cartella specificataPercorso/carattere jollyFolderPath per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì `fileName` se non è specificato nel set di dati |
| modifiedDatetimeStart    | Filtro file in base all'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| maxConcurrentConnections | Numero di connessioni da connettere contemporaneamente all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No                                            |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-sink"></a>SFTP come lavandino

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le seguenti proprietà sono supportate per SFTP nelle `storeSettings` impostazioni nel sink di copia basato sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà `storeSettings` type in deve essere impostata su **SftpWriteSettings**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita):</b>mantiene la gerarchia di file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: Unisce tutti i file dalla cartella di origine in un unico file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| maxConcurrentConnections | Numero di connessioni per la connessione simultanea all'archivio dati. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No       |
| useTempFileRename (utilizzareTempFileRename) | Indicare se caricare in file temporanei e rinominare o scrivere direttamente nella cartella o nel percorso del file di destinazione. Per impostazione predefinita, ADF prima scrivere nei file temporanei, quindi eseguire la ridenominazione dei file al completamento del caricamento, al fine di 1) evitare la scrittura in conflitto con conseguente file danneggiato se si dispone di altri processi di scrittura nello stesso file e 2) assicurarsi che la versione originale del file esista durante l'intero trasferimento. Se il server SFTP non supporta l'operazione di ridenominazione, disattivare questa opzione e assicurarsi di non disporre di scrittura simultanea nel file di destinazione. Vedere il suggerimento per la risoluzione dei problemi riportata di seguito. | No. Il valore predefinito è true. |
| operationTimeout (Timeout) | Tempo di attesa prima del timeout di ogni richiesta di scrittura al server SFTP. Il valore predefinito è 60 min (01:00:00).|No |

>[!TIP]
>Se si verifica l'errore "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" o "SftpOperationFail" durante la scrittura dei dati in SFTP e l'utente SFTP utilizzato dispone dell'autorizzazione appropriata, verificare se l'operazione di ridenominazione dei file del supporto del server SFTP, in caso contrario, disattivare l'opzione "Carica con file temporaneo" ( )`useTempFileRename`e riprovare. Ulteriori informazioni su questa proprietà sono riportate nella tabella precedente. Se si utilizza Self-hosted Integration Runtime per la copia, assicurarsi di utilizzare la versione 4.6 o successiva.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

Questa sezione descrive il comportamento risultante del percorso cartella e del nome del file con i filtri con caratteri jolly.

| folderPath | fileName | ricorsiva | Struttura delle cartelle di origine e risultato del filtro (i file in **grassetto** vengono recuperati)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vuoto, usare valore predefinito) | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vuoto, usare valore predefinito) | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare [Attività GetMetadataTo](control-flow-get-metadata-activity.md) learn details about the properties, check GetMetadata activity 

## <a name="delete-activity-properties"></a>Eliminare le proprietà dell'attività

Per informazioni dettagliate sulle proprietà, [selezionare Elimina attività](delete-activity.md)

## <a name="legacy-models"></a>Modelli legacy

>[!NOTE]
>I modelli seguenti sono ancora supportati così come sono per la compatibilità con le versioni precedenti. Si consiglia di usare il nuovo modello indicato nelle sezioni precedenti in futuro e l'interfaccia utente di creazione di ADF è passata alla generazione del nuovo modello.

### <a name="legacy-dataset-model"></a>Modello di set di dati legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **FileShare** |Sì |
| folderPath | Percorso della cartella. I filtri con caratteri jolly sono supportati, i caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per il carattere escape se il nome effettivo del file include caratteri jolly o escape. <br/><br/>Esempi: cartellaradice/sottocartella/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |Sì |
| fileName |  **Filtro con nome o carattere jolly** per i file nell'elemento "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per un filtro, i caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per applicare una sequenza di escape se il nome effettivo della cartella include caratteri jolly o tale carattere di escape. |No |
| modifiedDatetimeStart | Filtro file in base all'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati saranno influenzate abilitando questa impostazione quando si desidera eseguire il filtro di file da enormi quantità di file. <br/><br/> Le proprietà possono essere NULL che indicano che nessun filtro di attributo file verrà applicato al set di dati.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| modifiedDatetimeEnd | Filtro file in base all'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati saranno influenzate abilitando questa impostazione quando si desidera eseguire il filtro di file da enormi quantità di file. <br/><br/> Le proprietà possono essere NULL che indicano che nessun filtro di attributo file verrà applicato al set di dati.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| format | Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare file con un formato specifico, sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JsonFormat](supported-file-formats-and-compression-codecs-legacy.md#json-format), [AvroFormat](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [OrcFormat](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [ParquetFormat](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono i seguenti: **G'ip**, **Deflate**, **B-ip2**e **.**<br/>I livelli supportati sono: **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un determinato nome, specificare **folderPath** con il percorso della cartella e **fileName** con il nome del file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con il percorso della cartella e **fileName** con il filtro con caratteri jolly.

>[!NOTE]
>Se si usa la proprietà "fileFilter" per il filtro dei file, è comunque supportata senza alcuna modifica, mentre in futuro verrà consigliato di usare la nuova funzionalità di filtro aggiunta a "fileName".

**Esempio:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

### <a name="legacy-copy-activity-source-model"></a>Modello di origine dell'attività di copia legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **FileSystemSource** |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che se recursive è impostata su true e il sink è un archivio basato su file, la cartella o la sottocartella vuota non verrà copiata o creata nel sink.<br/>I valori consentiti sono: true (impostazione predefinita), **falseAllowed** values are: **true** (default), false | No |
| maxConcurrentConnections | Numero di connessioni da connettere contemporaneamente all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
