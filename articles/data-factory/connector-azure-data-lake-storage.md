---
title: Copiare e trasformare dati in Azure Data Lake Storage Gen2
description: Informazioni su come copiare dati in e da Azure Data Lake Storage Gen2 e su come trasformare i dati in Azure Data Lake Storage Gen2 tramite Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/09/2020
ms.openlocfilehash: 06c09144fc112d6f095271c510fa33b816e8f906
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612642"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiare e trasformare dati in Azure Data Lake Storage Gen2 tramite Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 (ADLS Gen2) è un set di funzionalità dedicate all'analisi dei Big Data e integrate in [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md). Consente di interagire con i dati approfittando dei paradigmi sia del file system che dell'archiviazione di oggetti.

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure Data Lake Storage Gen2 e come usare Flusso di dati per trasformare i dati in Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

>[!TIP]
>Per informazioni sullo scenario di migrazione di data lake o data warehouse, vedere [Usare Azure Data Factory per eseguire la migrazione dei dati da data lake o data warehouse in Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore Azure Data Lake Storage Gen2 è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Delete](delete-activity.md)

Per l'attività Copy, con il connettore è possibile eseguire queste operazioni:

- Copiare dati da/in Azure Data Lake Storage Gen2 tramite chiave dell'account, entità servizio o identità gestite per l'autenticazione delle risorse di Azure.
- Copiare file così come sono o analizzare o generare file con i [formati di file e i codec di compressione supportati](supported-file-formats-and-compression-codecs.md).
- [Mantenere i metadati del file durante la copia](#preserve-metadata-during-copy).
- [Mantenere gli elenchi di controllo di accesso](#preserve-acls) durante la copia da Azure Data Lake Storage Gen1/Gen2.

>[!IMPORTANT]
>Se si abilita l'opzione **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** nelle impostazioni del firewall di Archiviazione di Azure e si vuole usare Azure Integration Runtime per la connessione a Data Lake Storage Gen2, è necessario usare l'[autenticazione dell'identità gestita](#managed-identity) per ADLS Gen2.


## <a name="get-started"></a>Introduzione

>[!TIP]
>Per una procedura dettagliata su come usare il connettore Data Lake Storage Gen2, vedere [Caricare dati in Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti contengono informazioni sulle proprietà usate per definire entità di Data Factory specifiche per Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il connettore Azure Data Lake Storage Gen2 supporta i tipi di autenticazione seguenti. Per informazioni dettagliate, vedere le sezioni corrispondenti:

- [Autenticazione basata sulla chiave dell'account](#account-key-authentication)
- [Autenticazione di un'entità servizio](#service-principal-authentication)
- [Autenticazione di identità gestite per le risorse di Azure](#managed-identity)

>[!NOTE]
>Quando si usa la polibase per caricare i dati in Azure sinapsi Analytics (in precedenza SQL Data Warehouse), se il Data Lake Storage Gen2 di origine è configurato con l'endpoint di rete virtuale, è necessario usare l'autenticazione di identità gestita come richiesto da polibase. Vedere la sezione sull'[autenticazione dell'identità gestita](#managed-identity) con altri prerequisiti di configurazione.

### <a name="account-key-authentication"></a>Autenticazione basata sulla chiave dell'account

Per usare l'autenticazione basata sulla chiave dell'account di archiviazione, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello di `https://<accountname>.dfs.core.windows.net`. | Sì |
| accountKey | Chiave dell'account per Data Lake Storage Gen2. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted, se l'archivio dati si trova in una rete privata. Se questa proprietà non è specificata, come valore predefinito viene usato Azure Integration Runtime. |No |

>[!NOTE]
>L'endpoint secondario del file system di ADSL non è supportato quando si usa l'autenticazione della chiave dell'account. È possibile usare altri tipi di autenticazione.

**Esempio:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per usare l'autenticazione basata sull'entità servizio, eseguire queste operazioni.

1. Registrare un'entità applicazione in Azure Active Directory (Azure AD) seguendo i passaggi descritti in [Registrare l'applicazione con un tenant di Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio l'autorizzazione appropriata. Per gli esempi di funzionamento dell'autorizzazione in Data Lake Storage Gen2, vedere [Elenchi di controllo di accesso per file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Come origine**: in Storage Explorer, concedere almeno l'autorizzazione **Execute** per TUTTE le cartelle upstream e il file system, insieme all'autorizzazione **Read** per i file da copiare. In alternativa, in Controllo di accesso (IAM) concedere almeno il ruolo **Lettore dei dati dei BLOB di archiviazione**.
    - **Come sink**: in Storage Explorer, concedere almeno l'autorizzazione **Execute** per TUTTE le cartelle upstream e il file system, insieme all'autorizzazione **Write** per la cartella sink. In alternativa, in Controllo di accesso (IAM) concedere almeno il ruolo **Collaboratore ai dati dei BLOB di archiviazione**.

>[!NOTE]
>Se si usa l'interfaccia utente di Data Factory per la creazione e l'entità servizio non è impostata con il ruolo "Lettore/Collaboratore dei dati dei BLOB di archiviazione" in IAM, quando si esegue la connessione di test o si esplorano le cartelle, scegliere "Test connection to file path" (Testa connessione al percorso file) o "Browse from specified path" (Sfoglia da percorso specificato) e specificare un percorso con le autorizzazioni **Read + Execute** per continuare.

Queste sono le proprietà supportate dal servizio collegato:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello di `https://<accountname>.dfs.core.windows.net`. | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalCredentialType | Tipo di credenziale da utilizzare per l'autenticazione dell'entità servizio. I valori consentiti sono **ServicePrincipalKey** e **ServicePrincipalCert**. | Sì |
| servicePrincipalCredential | Credenziale dell'entità servizio. <br/> Quando si usa **ServicePrincipalKey** come tipo di credenziale, specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in data factory o [fare riferimento a un segreto archiviato nel Azure Key Vault](store-credentials-in-key-vault.md). <br/> Quando si usa **ServicePrincipalCert** come credenziale, fare riferimento a un certificato in Azure Key Vault. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in data factory o [fare riferimento a un segreto archiviato nel Azure Key Vault](store-credentials-in-key-vault.md). <br/> Questa proprietà è ancora supportata così com'è per `servicePrincipalId`  +  `servicePrincipalKey` . Quando ADF aggiunge la nuova autenticazione del certificato dell'entità servizio, il nuovo modello per l'autenticazione dell'entità servizio è `servicePrincipalId`  +  `servicePrincipalCredentialType`  +  `servicePrincipalCredential` . | No |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |
| azureCloudType | Per l'autenticazione dell'entità servizio, specificare il tipo di ambiente cloud di Azure in cui è registrata l'applicazione Azure Active Directory. <br/> I valori consentiti sono **AzurePublic**, **AzureChina**, **AzureUsGovernment**e **AzureGermany**. Per impostazione predefinita, viene usato l'ambiente cloud del data factory. | No |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted, se l'archivio dati si trova in una rete privata. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio: uso dell'autenticazione con chiave dell'entità servizio**

È anche possibile archiviare la chiave dell'entità servizio in Azure Key Vault.

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: uso dell'autenticazione del certificato dell'entità servizio**
```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Autenticazione di identità gestite per le risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione con Data Lake Storage Gen2, analogamente a come si usa l'entità servizio. Consente alla factory designata di accedere e copiare i dati da e verso Data Lake Storage Gen2.

Per usare le identità gestite per l'autenticazione delle risorse di Azure, eseguire queste operazioni.

1. [Recuperare le informazioni relative all'identità gestita di Data Factory](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore di **ID oggetto dell'identità gestita** generato con la factory.

2. Concedere l'autorizzazione appropriata per l'identità gestita. Per gli esempi di funzionamento dell'autorizzazione in Data Lake Storage Gen2, vedere [Elenchi di controllo di accesso per file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Come origine**: in Storage Explorer, concedere almeno l'autorizzazione **Execute** per TUTTE le cartelle upstream e il file system, insieme all'autorizzazione **Read** per i file da copiare. In alternativa, in Controllo di accesso (IAM) concedere almeno il ruolo **Lettore dei dati dei BLOB di archiviazione**.
    - **Come sink**: in Storage Explorer, concedere almeno l'autorizzazione **Execute** per TUTTE le cartelle upstream e il file system, insieme all'autorizzazione **Write** per la cartella sink. In alternativa, in Controllo di accesso (IAM) concedere almeno il ruolo **Collaboratore ai dati dei BLOB di archiviazione**.

>[!NOTE]
>Se si usa l'interfaccia utente di Data Factory per la creazione e l'identità gestita non è impostata con il ruolo "Lettore/Collaboratore dei dati dei BLOB di archiviazione" in IAM, quando si esegue la connessione di test o si esplorano le cartelle, scegliere "Test connection to file path" (Testa connessione al percorso file) o "Browse from specified path" (Sfoglia da percorso specificato) e specificare un percorso con le autorizzazioni **Read + Execute** per continuare.

>[!IMPORTANT]
>Se si usa la funzione polibase per caricare dati da Data Lake Storage Gen2 in Azure sinapsi Analytics (in precedenza SQL Data Warehouse), quando si usa l'autenticazione di identità gestita per Data Lake Storage Gen2, assicurarsi di seguire anche i passaggi 1 e 2 in [questa guida](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) a 1) registrare il con Azure Active Directory (Azure ad) e 2) assegnare il ruolo di collaboratore dati BLOB di archiviazione al server. il resto viene gestito da Data Factory. Se Data Lake Storage Gen2 è configurato con l'endpoint di Rete virtuale di Azure, per usare PolyBase per il caricamento dei dati dall'endpoint, è necessario usare l'autenticazione dell'identità gestita come richiesto da PolyBase.

Queste sono le proprietà supportate dal servizio collegato:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello di `https://<accountname>.dfs.core.windows.net`. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted, se l'archivio dati si trova in una rete privata. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Data Lake Storage Gen2 nelle impostazioni di `location` nel set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà type in `location` nel set di dati deve essere impostata su **AzureBlobFSLocation**. | Sì      |
| fileSystem | Nome del file system di Data Lake Storage Gen2.                              | No       |
| folderPath | Percorso della cartella nel file system specificato. Se si intende usare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome file nel percorso cartella e nel file system specificati. Se si intende usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Configurazioni dell'attività di copia](copy-activity-overview.md#configuration) e [Pipeline e attività](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 come tipo di origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Sono disponibili diverse opzioni per copiare i dati da ADLS Gen2:

- Copia dal percorso specificato nel set di dati.
- Filtro con caratteri jolly per il percorso della cartella o il nome del file, vedere `wildcardFolderPath` e `wildcardFileName`.
- Copia dei file definiti in un file di testo specificato come set di file, vedere `fileListPath`.

Le proprietà seguenti sono supportate per Data Lake Storage Gen2 nelle impostazioni di `storeSettings` nell'origine della copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà type in `storeSettings` deve essere impostata su **AzureBlobFSReadSettings**. | Sì                                           |
| ***Individuare i file da copiare:*** |  |  |
| OPZIONE 1: percorso statico<br> | Copia dal file system o dal percorso della cartella/del file specificato nel set di dati. Se si vogliono copiare tutti i file da una cartella o da un file system, specificare anche `wildcardFileName` come `*`. |  |
| OPZIONE 2: carattere jolly<br>- wildcardFolderPath | Percorso della cartella con caratteri jolly nel file system specificato configurato nel set di dati per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| OPZIONE 2: carattere jolly<br>- wildcardFileName | Nome file con caratteri jolly nel file system + percorso folderPath/wildcardFolderPath specificato per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì |
| OPZIONE 3: un elenco di file<br>- fileListPath | Indica di copiare un determinato set di file. Puntare a un file di testo che include un elenco di file da copiare, un file per riga, che rappresenta il percorso relativo del percorso configurato nel set di dati.<br/>Quando si usa questa opzione, non specificare il nome del file nel set di dati. Per altri esempi, vedere [Esempi di elenco di file](#file-list-examples). |No |
| ***Impostazioni aggiuntive:*** |  | |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. <br>I valori consentiti sono **true** (predefinito) e **false**.<br>Questa proprietà non è applicabile quando si configura `fileListPath`. |No |
| deleteFilesAfterCompletion | Indica se i file binari verranno eliminati dall'archivio di origine dopo che è stato eseguito il passaggio all'archivio di destinazione. L'eliminazione del file è per file, pertanto quando l'attività di copia ha esito negativo, si noterà che alcuni file sono già stati copiati nella destinazione ed eliminati dall'origine, mentre altri ancora rimangono nell'archivio di origine. <br/>Questa proprietà è valida solo nello scenario di copia binaria, in cui gli archivi di origini dati sono BLOB, ADLS Gen1, ADLS Gen2, S3, Google Cloud Storage, file, file di Azure, SFTP o FTP. Valore predefinito: false. |No |
| modifiedDatetimeStart    | Filtro di file basato sull'attributo: Ultima modifica. <br>I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, il che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.<br/>Questa proprietà non è applicabile quando si configura `fileListPath`. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| enablePartitionDiscovery | Per i file partizionati, specificare se analizzare le partizioni dal percorso del file e aggiungerle come colonne di origine aggiuntive.<br/>I valori consentiti sono **false** (impostazione predefinita) e **true**. | No                                            |
| partitionRootPath | Quando è abilitata l'individuazione delle partizioni, specificare il percorso radice assoluto per leggere le cartelle partizionate come colonne di dati.<br/><br/>Se non viene specificato, per impostazione predefinita<br/>-Quando si usa il percorso del file in un set di dati o un elenco di file nell'origine, il percorso radice della partizione è il percorso configurato nel set di dati.<br/>-Quando si usa il filtro di cartelle con caratteri jolly, il percorso radice della partizione è il percorso secondario prima del primo carattere jolly.<br/><br/>Si supponga, ad esempio, di configurare il percorso nel set di dati come "root/folder/Year = 2020/month = 08/Day = 27":<br/>-Se si specifica il percorso radice della partizione come "root/folder/Year = 2020", l'attività di copia genererà altre due colonne `month` e `day` con il valore "08" e "27", oltre alle colonne all'interno dei file.<br/>-Se il percorso radice della partizione non è specificato, non verrà generata alcuna colonna aggiuntiva. | No                                            |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi alla risorsa di archiviazione. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No                                            |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 come tipo di sink

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Le proprietà seguenti sono supportate per Data Lake Storage Gen2 nelle impostazioni di `storeSettings` nel sink della copia basato sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà type in `storeSettings` deve essere impostata su **AzureBlobFSWriteSettings**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| blockSizeInMB | Specificare le dimensioni del blocco in MB usate per scrivere i dati in ADLS Gen2. Altre informazioni sui [BLOB in blocchi](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Il valore consentito è **compreso tra 4 MB e 100 MB**. <br/>Per impostazione predefinita, Azure Data Factory determina automaticamente le dimensioni del blocco in base al tipo e ai dati dell'archivio di origine. Per la copia non binaria in ADLS Gen2, le dimensioni predefinite del blocco sono pari a 100 MB in modo da contenere al massimo i dati da 4,95 TB. Potrebbe non essere una soluzione ottimale quando i dati non sono di grandi dimensioni, soprattutto quando si usa il runtime di integrazione self-hosted con prestazioni di rete scarse, causando il timeout dell'operazione o un problema di prestazioni. È possibile specificare in modo esplicito le dimensioni del blocco, assicurandosi che blockSizeInMB*50000 sia sufficientemente grande per archiviare i dati. In caso contrario, l'esecuzione dell'attività di copia avrà esito negativo. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi all'archivio dati. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No       |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

Questa sezione descrive il comportamento risultante del percorso cartella e del nome del file con i filtri con caratteri jolly.

| folderPath | fileName | ricorsiva | Struttura delle cartelle di origine e risultato del filtro (i file in **grassetto** sono stati recuperati)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vuoto, usare il valore predefinito) | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vuoto, usare il valore predefinito) | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Esempi di elenco di file

Questa sezione descrive il comportamento risultante dall'uso del percorso di elenco file nell'origine dell'attività di copia.

Si supponga di disporre della struttura di cartelle di origine seguente e di voler copiare i file in grassetto:

| Esempio di struttura di origine                                      | Contenuto in FileListToCopy.txt                             | Configurazione di Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| file system<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadati<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sottocartella1/File3.csv<br>Sottocartella1/File5.csv | **Nel set di dati:**<br>- File system: `filesystem`<br>- Percorso cartella: `FolderA`<br><br>**Nell'origine dell'attività Copy:**<br>- Percorso elenco file: `filesystem/Metadata/FileListToCopy.txt` <br><br>Il percorso dell'elenco di file fa riferimento a un file di testo nello stesso archivio dati che include un elenco di file da copiare, un file per riga con il percorso relativo del percorso configurato nel set di dati. |


### <a name="some-recursive-and-copybehavior-examples"></a>Alcuni esempi dei valori recursive e copyBehavior

Questa sezione descrive il comportamento derivante dall'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome di file generato automaticamente. Nome generato automaticamente per File1<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |

## <a name="preserve-metadata-during-copy"></a>Mantenere i metadati durante la copia

Quando si copiano i file da Amazon S3/BLOB di Azure/Azure Data Lake Storage Gen2 ad Azure Data Lake Storage Gen2/BLOB di Azure, è possibile scegliere di mantenere i metadati del file insieme ai dati. Altre informazioni in [Mantenere i metadati](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> Mantenere gli elenchi di controllo di accesso da Data Lake Storage Gen1/Gen2

Quando si copiano i file da Azure Data Lake Storage Gen1/Gen2 a Gen2, è possibile scegliere di mantenere gli elenchi di controllo di accesso (ACL) POSIX insieme ai dati. Altre informazioni in [Mantenere gli elenchi di controllo di accesso da Data Lake Storage Gen1/Gen2 a Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Per copiare i dati da Azure Data Lake Storage Gen1 in Gen2 in generale, vedere la procedura dettagliata e le procedure consigliate in [Copiare dati da Azure Data Lake Storage Gen1 a Gen2 con Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Quando si trasformano i dati nei flussi di dati di mapping, è possibile leggere e scrivere file da Azure Data Lake Storage Gen2 nei formati seguenti:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Common Data Model (anteprima)](format-common-data-model.md#mapping-data-flow-properties)
* [Testo delimitato](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Le impostazioni specifiche del formato si trovano nella documentazione relativa a tale formato. Per ulteriori informazioni, vedere [trasformazione origine nel flusso di dati di mapping](data-flow-source.md) e [trasformazione sink nel flusso di dati del mapping](data-flow-sink.md).

### <a name="source-transformation"></a>Trasformazione origine

Nella trasformazione origine è possibile leggere da un contenitore, una cartella o un singolo file in Azure Data Lake Storage Gen2. La scheda **Source options** (Opzioni origine) consente di gestire la modalità di lettura dei file. 

![Opzioni origine](media/data-flow/sourceOptions1.png "Opzioni origine")

**Percorso con caratteri jolly**: l'uso di una sequenza con caratteri jolly indica ad Azure Data Factory di scorrere ogni cartella e file corrispondente in un'unica trasformazione origine. Si tratta di un modo efficace per elaborare più file all'interno di un singolo flusso. Aggiungere più sequenze di corrispondenza con caratteri jolly con il segno + visualizzato quando si passa il mouse sulla sequenza con caratteri jolly esistente.

Nel contenitore di origine scegliere una serie di file che corrispondono a un criterio. Nel set di dati è possibile specificare solo il contenitore. Il percorso con caratteri jolly deve quindi includere anche il percorso della cartella a partire dalla cartella radice.

Esempi di caratteri jolly:

* ```*``` Rappresenta qualsiasi set di caratteri
* ```**``` Rappresenta l'annidamento delle directory ricorsive
* ```?``` Sostituisce un carattere
* ```[]``` Trova la corrispondenza di uno o più caratteri nelle parentesi quadre

* ```/data/sales/**/*.csv``` Ottiene tutti i file con estensione csv in /data/sales
* ```/data/sales/20??/**/``` Ottiene tutti i file presenti in /data/sales del ventesimo secolo
* ```/data/sales/*/*/*.csv``` Ottiene i file con estensione csv due livelli sotto /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv``` Ottiene tutti i file con estensione csv del 2004 a dicembre a partire da X o Y preceduto da un numero a due cifre

**Partition Root Path** (Percorso radice partizione): se nell'origine file sono presenti cartelle partizionate con un formato ```key=value``` (ad esempio anno = 2019), è possibile assegnare il livello principale dell'albero delle cartelle di tale partizione a un nome di colonna nel flusso di dati.

Impostare innanzitutto un carattere jolly per includere tutti i percorsi che rappresentano le cartelle partizionate, nonché i file foglia da leggere.

![Impostazioni file di origine partizione](media/data-flow/partfile2.png "Impostazione del file di partizione")

Usare l'impostazione Partition Root Path (Percorso radice partizione) per definire il livello superiore della struttura di cartelle. Quando si visualizza il contenuto dei dati tramite un'anteprima, si noti che Azure Data Factory aggiunge le partizioni risolte presenti in ogni livello di cartelle.

![Partition Root Path](media/data-flow/partfile1.png "Anteprima percorso radice partizione") (Percorso radice partizione)

**Elenco di file**: set di file. Creare un file di testo che includa un elenco di file di percorsi relativi da elaborare. Puntare a questo file di testo.

**Column to store file name** (Colonna in cui archiviare il nome del file): archiviare il nome del file di origine in una colonna relativa ai dati. Immettere un nuovo nome di colonna per archiviare la stringa del nome file.

**After completion** (Dopo il completamento): scegliere di non eseguire alcuna operazione con il file di origine dopo l'esecuzione del flusso di dati, eliminare il file di origine oppure spostare il file di origine. I percorsi per lo spostamento sono relativi.

Per spostare i file di origine in un altro percorso dopo l'elaborazione, selezionare "Sposta" come operazione sul file. Impostare quindi la directory "da". Se non si usano caratteri jolly per il percorso, l'impostazione "da" sarà la stessa cartella della cartella di origine.

Se si dispone di un percorso di origine con carattere jolly, la sintassi avrà un aspetto simile al seguente:

```/data/sales/20??/**/*.csv```

È possibile specificare "da" come

```/data/sales```

E "a" come

```/backup/priorSales```

In questo caso, tutti i file originati in/data/sales vengono spostati in /backup/priorSales.

> [!NOTE]
> Le operazioni sui file vengono eseguite solo quando si avvia il flusso di dati da un'esecuzione di pipeline (esecuzione del debug o esecuzione della pipeline) che usa l'attività di esecuzione del flusso di dati in una pipeline. Le operazioni sui file *non* vengono eseguite in modalità di debug del flusso di dati.

**Filter by last modified** (Filtra in base a ultima modifica): è possibile filtrare i file elaborati specificando un intervallo di date relative all'ultima modifica. Tutte le ore e le date sono in formato UTC. 

### <a name="sink-properties"></a>Proprietà sink

Nella trasformazione sink è possibile scrivere in un contenitore oppure in una cartella in Azure Data Lake Storage Gen2. La scheda **Settings** (Impostazioni) consente di gestire la modalità di scrittura dei file.

![Opzioni sink](media/data-flow/file-sink-settings.png "Opzioni sink")

**Clear the folder** (Cancellare la cartella): determina se la cartella di destinazione viene cancellata prima della scrittura dei dati.

**File name option** (Opzione nome file): determina la modalità di denominazione dei file finali nella cartella di destinazione. Le opzioni di nomi di file sono indicate di seguito:
   * **Predefinita**: consente a Spark di denominare i file basati sulle impostazioni predefinite di PART.
   * **Pattern** (Modello): consente di immettere un modello che enumera i file di output per partizione. **loans[n].csv**, ad esempio, creerà loans1.csv, loans2.csv e così via.
   * **Per partition** (Per partizione): consente di immettere un nome di file per partizione.
   * **As data in column** (Come dati in colonna): consente di impostare il file di output sul valore di una colonna. Il percorso è relativo al contenitore del set di dati e non alla cartella di destinazione. Se nel set di dati è presente un percorso di cartella, quest'ultimo verrà ignorato.
   * **Output to a single file** (Output in un singolo file): consente di combinare i file di output partizionati in un singolo file denominato. Il percorso è relativo alla cartella del set di dati. Tenere presente che l'operazione di merge può avere esito negativo in base alla dimensione del nodo. Questa opzione non è consigliata per i set di dati di grandi dimensioni.

**Quote all** (Inserisci tra virgolette): determina se racchiudere tutti i valori tra virgolette.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per altre informazioni sulle proprietà, vedere [Attività GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Proprietà dell'attività Delete

Per altre informazioni sulle proprietà, vedere [Attività Delete](delete-activity.md)

## <a name="legacy-models"></a>Modalità legacy

>[!NOTE]
>I modelli seguenti sono ancora supportati così come sono per la compatibilità con le versioni precedenti. In futuro, è consigliabile usare il nuovo modello citato nelle sezioni precedenti, tenendo presente che l'interfaccia utente di creazione di Azure Data Factory è passata alla generazione del nuovo modello.

### <a name="legacy-dataset-model"></a>Modello di set di dati legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureBlobFSFile**. |Sì |
| folderPath | Percorso della cartella in Data Lake Storage Gen2. Se il valore non è specificato, punta alla radice. <br/><br/>Il filtro con caratteri jolly è supportato. I caratteri jolly consentiti sono `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per applicare una sequenza di escape se il nome effettivo della cartella include caratteri jolly o tale carattere di escape. <br/><br/>Esempi: filesystem/folder/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |No |
| fileName | Filtro con nome o carattere jolly per i file nell'elemento "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per un filtro, i caratteri jolly consentiti sono `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Per applicare una sequenza di escape se il nome effettivo del file include caratteri jolly o tale carattere di escape, usare `^`.<br/><br/>Se non si specifica fileName per un set di dati di output e non si specifica **preserveHierarchy** nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il criterio seguente: "*Data.[GUID ID esecuzione attività].[GUID se FlattenHierarchy].[formato se configurato].[compressione se configurata]* ", ad esempio "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se si esegue la copia da un'origine in formato tabulare usando un nome di tabella anziché una query, il modello di nome è " *[nome tabella].[formato].[compressione se configurata]* ", ad esempio "MiaTabella.csv". |No |
| modifiedDatetimeStart | Filtro di file basato sull'ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo compreso tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata in base al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si intende applicare un filtro a grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file. Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| modifiedDatetimeEnd | Filtro di file basato sull'ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo compreso tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata in base al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si intende applicare un filtro a grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file. Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato OCR](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un determinato nome, specificare **folderPath** con un percorso di cartella e **fileName** con un nome di file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con un percorso di cartella e **fileName** con un filtro con caratteri jolly. 

**Esempio:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **AzureBlobFSSource**. |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink.<br/>I valori consentiti sono **true** (predefinito) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi all'archivio dati. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Modello di sink dell'attività di copia legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **AzureBlobFSSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi all'archivio dati. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
