---
title: Copiare dati da e sul server SFTP
description: Informazioni su come copiare dati da e nel server SFTP usando Azure Data Factory.
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
ms.date: 08/28/2020
ms.openlocfilehash: f4b78c6cb2af8d18dc761e9bfc78740a845f54fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91252936"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Copiare i dati da e verso il server SFTP usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Versione 1](v1/data-factory-sftp-connector.md)
> * [Versione corrente](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come copiare dati da e nel server FTP sicuro (SFTP). Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore SFTP è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Delete](delete-activity.md)

In particolare, il connettore SFTP supporta:

- Copia di file da e verso il server SFTP usando l'autenticazione di *base* o *SshPublicKey* .
- La copia di file così come sono o tramite l'analisi o la generazione di file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per SFTP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato SFTP sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su *SFTP*. |Sì |
| host | Nome o indirizzo IP del server SFTP. |Sì |
| port | Porta su cui il server SFTP è in ascolto.<br/>Il valore consentito è un numero intero e il valore predefinito è *22*. |No |
| skipHostKeyValidation | Specificare se si desidera ignorare la convalida tramite della chiave host.<br/>I valori consentiti sono *true* e *false* (impostazione predefinita).  | No |
| hostKeyFingerprint | Specificare l'impronta digitale della chiave host. | Sì, se "skipHostKeyValidation" è impostato su false.  |
| authenticationType | Specificare il tipo di autenticazione.<br/>I valori consentiti sono *Basic* e *SshPublicKey*. Per altre proprietà, vedere la sezione [usare l'autenticazione di base](#use-basic-authentication) . Per esempi JSON, vedere la sezione [usare l'autenticazione con chiave pubblica SSH](#use-ssh-public-key-authentication) . |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per ulteriori informazioni, vedere la sezione [prerequisiti](#prerequisites) . Se il runtime di integrazione non è specificato, il servizio usa il Azure Integration Runtime predefinito. |No |

### <a name="use-basic-authentication"></a>Usare l'autenticazione di base

Per usare l'autenticazione di base, impostare la proprietà *AuthenticationType* su *Basic*e specificare le proprietà seguenti oltre alle proprietà generiche del connettore SFTP introdotte nella sezione precedente:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| userName | Utente che ha accesso al server SFTP. |Sì |
| password | Password per l'utente (userName). Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro nel data factory oppure [fare riferimento a un segreto archiviato in un insieme di credenziali delle chiavi di Azure](store-credentials-in-key-vault.md). | Sì |

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>Usare l'autenticazione con chiave pubblica SSH

Per usare l'autenticazione basata su chiave pubblica SSH, impostare la proprietà "authenticationType" su **SshPublicKey** e specificare le proprietà seguenti oltre a quelle generiche del connettore SFTP introdotte nell'ultima sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| userName | Utente che ha accesso al server SFTP. |Sì |
| privateKeyPath | Specificare il percorso assoluto del file di chiave privata a cui il runtime di integrazione può accedere. Si applica solo quando il tipo self-hosted del runtime di integrazione viene specificato in "connectVia". | Specificare `privateKeyPath` o `privateKeyContent`.  |
| privateKeyContent | Contenuto della chiave privata SSH con codifica Base64. La chiave privata SSH deve essere in formato OpenSSH. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro nel data factory oppure [fare riferimento a un segreto archiviato in un insieme di credenziali delle chiavi di Azure](store-credentials-in-key-vault.md). | Specificare `privateKeyPath` o `privateKeyContent`. |
| passPhrase | Specificare la passphrase o la password per decrittografare la chiave privata se il file di chiave o il contenuto della chiave è protetto da una passphrase. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro nel data factory oppure [fare riferimento a un segreto archiviato in un insieme di credenziali delle chiavi di Azure](store-credentials-in-key-vault.md). | Sì, se il file di chiave privata o il contenuto della chiave è protetto da una passphrase. |

> [!NOTE]
> Il connettore SFTP supporta una chiave OpenSSH RSA/DSA. Verificare che il contenuto del file di chiave inizi con "-----BEGIN [RSA/DSA] chiave privata-----". Se il file di chiave privata è un file in formato PPK, usare lo strumento PuTTy per eseguire la conversione da PPK a formato OpenSSH. 

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: Autenticazione SshPublicKey con contenuto della chiave privata**

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere l'articolo [DataSets (set](concepts-datasets-linked-services.md) di impostazioni). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Per SFTP in `location` impostazioni nel set di dati basato su formato, sono supportate le proprietà seguenti:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà *Type* `location` nel set di dati deve essere impostata su *SftpLocation*. | Sì      |
| folderPath | Percorso della cartella. Se si vuole usare un carattere jolly per filtrare la cartella, ignorare questa impostazione e specificare il percorso nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file sotto il folderPath specificato. Se si vuole usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificare il nome del file in Impostazioni origine attività. | No       |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md) . Questa sezione presenta un elenco delle proprietà supportate dall'origine SFTP.

### <a name="sftp-as-source"></a>SFTP come origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per SFTP nelle `storeSettings` impostazioni dell'origine della copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà *Type* in `storeSettings` deve essere impostata su *SftpReadSettings*. | Sì                                           |
| ***Individuare i file da copiare*** |  |  |
| OPZIONE 1: percorso statico<br> | Copiare il percorso del file o della cartella specificato nel set di dati. Se si vogliono copiare tutti i file da una cartella, specificare anche `wildcardFileName` come `*`. |  |
| OPZIONE 2: carattere jolly<br>- wildcardFolderPath | Percorso della cartella con caratteri jolly per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere); usare `^` per eseguire l'escape se il nome della cartella effettivo contiene un carattere jolly o il carattere di escape. <br>Per altri esempi, vedere [esempi di filtro di file e cartelle](#folder-and-file-filter-examples). | No                                            |
| OPZIONE 2: carattere jolly<br>- wildcardFileName | Nome file con caratteri jolly sotto il folderPath/wildcardFolderPath specificato per filtrare i file di origine. <br>I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere); utilizzare `^` per eseguire l'escape se il nome della cartella effettivo contiene caratteri jolly o il carattere di escape.  Per altri esempi, vedere [esempi di filtro di file e cartelle](#folder-and-file-filter-examples). | Sì |
| OPZIONE 3: un elenco di file<br>- fileListPath | Indica la copia di un set di file specificato. Puntare a un file di testo che include un elenco di file da copiare, ovvero un file per riga, con il percorso relativo del percorso configurato nel set di dati.<br/>Quando si usa questa opzione, non specificare il nome del file nel set di dati. Per altri esempi, vedere [esempi di elenco di file](#file-list-examples). |No |
| ***Impostazioni aggiuntive*** |  | |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. <br>I valori consentiti sono *true* (predefinito) e *false*.<br>Questa proprietà non è applicabile quando si configura `fileListPath`. |No |
| deleteFilesAfterCompletion | Indica se i file binari verranno eliminati dall'archivio di origine dopo che è stato eseguito il passaggio all'archivio di destinazione. L'eliminazione del file è per file, pertanto quando l'attività di copia ha esito negativo, si noterà che alcuni file sono già stati copiati nella destinazione ed eliminati dall'origine, mentre altri ancora rimangono nell'archivio di origine. <br/>Questa proprietà è valida solo nello scenario di copia di file binari. Valore predefinito: false. |No |
| modifiedDatetimeStart    | I file vengono filtrati in base all' *Ultima modifica*apportata all'attributo. <br>I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di `modifiedDatetimeStart` a `modifiedDatetimeEnd` . L'ora viene applicata al fuso orario UTC nel formato *2018-12-01T05:00:00Z*. <br> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.<br/>Questa proprietà non è applicabile quando si configura `fileListPath`. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| enablePartitionDiscovery | Per i file partizionati, specificare se analizzare le partizioni dal percorso del file e aggiungerle come colonne di origine aggiuntive.<br/>I valori consentiti sono **false** (impostazione predefinita) e **true**. | No                                            |
| partitionRootPath | Quando è abilitata l'individuazione delle partizioni, specificare il percorso radice assoluto per leggere le cartelle partizionate come colonne di dati.<br/><br/>Se non viene specificato, per impostazione predefinita<br/>-Quando si usa il percorso del file in un set di dati o un elenco di file nell'origine, il percorso radice della partizione è il percorso configurato nel set di dati.<br/>-Quando si usa il filtro di cartelle con caratteri jolly, il percorso radice della partizione è il percorso secondario prima del primo carattere jolly.<br/><br/>Si supponga, ad esempio, di configurare il percorso nel set di dati come "root/folder/Year = 2020/month = 08/Day = 27":<br/>-Se si specifica il percorso radice della partizione come "root/folder/Year = 2020", l'attività di copia genererà altre due colonne `month` e `day` con il valore "08" e "27", oltre alle colonne all'interno dei file.<br/>-Se il percorso radice della partizione non è specificato, non verrà generata alcuna colonna aggiuntiva. | No                                            |
| maxConcurrentConnections | Il numero di connessioni che possono connettersi all'archivio di archiviazione simultaneamente. Specificare un valore solo quando si desidera limitare la connessione simultanea all'archivio dati. | No                                            |

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

### <a name="sftp-as-a-sink"></a>SFTP come sink

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Per SFTP in `storeSettings` impostazioni in un sink di copia basato sul formato sono supportate le proprietà seguenti:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà *Type* in `storeSettings` deve essere impostata su *SftpWriteSettings*. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| maxConcurrentConnections | Il numero di connessioni che possono connettersi all'archivio di archiviazione simultaneamente. Specificare un valore solo quando si desidera limitare la connessione simultanea all'archivio dati. | No       |
| useTempFileRename | Indicare se caricare i file temporanei e rinominarli oppure scrivere direttamente nella cartella o nel percorso del file di destinazione. Per impostazione predefinita, Azure Data Factory scrive prima nei file temporanei e li rinomina al termine del caricamento. Questa sequenza consente a (1) di evitare conflitti che potrebbero generare un file danneggiato se si hanno altri processi che scrivono nello stesso file e (2) assicurarsi che la versione originale del file esista durante il trasferimento. Se il server SFTP non supporta un'operazione di ridenominazione, disabilitare questa opzione e assicurarsi che non sia presente una scrittura simultanea nel file di destinazione. Per ulteriori informazioni, vedere la sezione relativa alla risoluzione dei problemi alla fine della tabella. | No. Il valore predefinito è *true*. |
| operationTimeout | Tempo di attesa prima del timeout di ogni richiesta di scrittura al server SFTP. Il valore predefinito 60 min(01:00:00).|No |

>[!TIP]
>Se viene visualizzato l'errore "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" o "SftpOperationFail" quando si scrivono dati in SFTP e l'utente *SFTP usato ha* le autorizzazioni appropriate, verificare se il server SFTP supporta l'operazione di ridenominazione del file. In caso contrario, disabilitare l'opzione **carica con file temporaneo** ( `useTempFileRename` ) e riprovare. Per ulteriori informazioni su questa proprietà, vedere la tabella precedente. Se si usa un runtime di integrazione self-hosted per l'attività di copia, assicurarsi di usare la versione 4,6 o successiva.

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

In questa sezione viene descritto il comportamento risultante dall'utilizzo di filtri con caratteri jolly con i percorsi di cartelle e i nomi di file.

| folderPath | fileName | ricorsiva | Struttura delle cartelle di origine e risultato del filtro (i file in **grassetto** sono stati recuperati)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vuoto, usare valore predefinito) | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vuoto, usare valore predefinito) | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Esempi di elenco di file

Questa tabella descrive il comportamento risultante dall'uso di un percorso elenco file nell'origine dell'attività di copia. Si presuppone che sia presente la struttura di cartelle di origine seguente e si desidera copiare i file in grassetto:

| Esempio di struttura di origine                                      | Contenuto in FileListToCopy.txt                             | Configurazione di Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadati<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sottocartella1/File3.csv<br>Sottocartella1/File5.csv | **Nel set di dati:**<br>- Percorso cartella: `root/FolderA`<br><br>**Nell'origine dell'attività di copia:**<br>- Percorso elenco file: `root/Metadata/FileListToCopy.txt` <br><br>Il percorso dell'elenco dei file punta a un file di testo nello stesso archivio dati che include un elenco di file da copiare, ovvero un file per riga, con il percorso relativo del percorso configurato nel set di dati. |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per informazioni sulle proprietà dell'attività Lookup, vedere [attività Lookup in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni sulle proprietà dell'attività GetMetadata, vedere [attività GetMetadata in Azure Data Factory](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Proprietà dell'attività Delete

Per informazioni sulle proprietà dell'attività Delete, vedere [attività Delete in Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Modalità legacy

>[!NOTE]
>I modelli seguenti sono ancora supportati come per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello illustrato in precedenza, poiché l'interfaccia utente di creazione Azure Data Factory ha cambiato la generazione del nuovo modello.

### <a name="legacy-dataset-model"></a>Modello di set di dati legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà *Type* del set di dati deve essere impostata su *FileShare*. |Sì |
| folderPath | Percorso della cartella. È supportato un filtro con caratteri jolly. I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere); utilizzare `^` per eseguire l'escape se il nome file effettivo dispone di un carattere jolly o di questo carattere di escape all'interno di. <br/><br/>Esempi: cartellaradice/sottocartella/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |Sì |
| fileName |  **Nome o filtro con caratteri jolly** per i file sotto il "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per il filtro, i caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per applicare una sequenza di escape se il nome effettivo della cartella include caratteri jolly o tale carattere di escape. |No |
| modifiedDatetimeStart | I file vengono filtrati in base all' *Ultima modifica*apportata all'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di `modifiedDatetimeStart` a `modifiedDatetimeEnd` . L'ora viene applicata al fuso orario UTC nel formato *2018-12-01T05:00:00Z*. <br/><br/> Le prestazioni complessive dello spostamento dei dati saranno influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro file da un numero elevato di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.| No |
| modifiedDatetimeEnd | I file vengono filtrati in base all' *Ultima modifica*apportata all'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di `modifiedDatetimeStart` a `modifiedDatetimeEnd` . L'ora viene applicata al fuso orario UTC nel formato *2018-12-01T05:00:00Z*. <br/><br/> Le prestazioni complessive dello spostamento dei dati saranno influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro file da un numero elevato di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si desidera analizzare i file con un formato specifico, sono supportati i tipi di formato di file *seguenti:* TextFormat, *JsonFormat*, *AvroFormat*, *OrcFormat*e *ParquetFormat*. Impostare la proprietà *type* nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni formato [testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [formato parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono *GZip*, *Deflate*, *BZip2* e *ZipDeflate*.<br/>I livelli supportati sono *Ottimale* e *Più veloce*. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo *folderPath*.<br>Per copiare un singolo file con un nome specificato, specificare *folderPath* con la parte della cartella e *filename* con il nome del file.<br>Per copiare un subset di file in una cartella, specificare *folderPath* con la parte della cartella e il *nome file* con il filtro con caratteri jolly.

>[!NOTE]
>Se si utilizza la proprietà *FileFilter* per il filtro file, è ancora supportata così com'è, ma è consigliabile utilizzare la nuova funzionalità di filtro aggiunta al *nome file* da ora in poi.

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
| type | La proprietà *Type* dell'origine dell'attività di copia deve essere impostata su *FileSystemSource* |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando ricorsivo è impostato su *true* e il sink è un archivio basato su file, le cartelle e le sottocartelle vuote non verranno copiate o create nel sink.<br/>I valori consentiti sono *true* (impostazione predefinita) e *false* | No |
| maxConcurrentConnections | Il numero di connessioni che possono connettersi a un archivio di archiviazione simultaneamente. Specificare un numero solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No |

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
Per un elenco di archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
