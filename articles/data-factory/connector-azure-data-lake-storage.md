---
title: Copiare e trasformare i dati in Archiviazione dati di Azure2Copy and transform data in Azure Data Lake Storage Gen2
description: Informazioni su come copiare i dati da e verso Azure Data Lake Storage Gen2 e trasformare i dati in Azure Data Lake Storage Gen2 usando Azure Data Factory.Learn how to and from Azure Data Lake Storage Gen2, and transform data in Azure Data Lake Storage Gen2 by using Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: a061df302680488377c3006dcef57b818a101d67
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011532"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiare e trasformare i dati in Archiviazione dati di Azure Usando2 usando Azure Data FactoryCopy and transform data in Azure Data Lake Storage Gen2 using Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) è un set di funzionalità dedicate all'analisi dei Big Data integrata nell'archiviazione BLOB di [Azure.](../storage/blobs/storage-blobs-introduction.md) È possibile utilizzarlo per interfacciarsi con i dati utilizzando sia i paradigmi di archiviazione del file system che quello di archiviazione degli oggetti.

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure Data Lake Storage Gen2 e usare Flusso di dati per trasformare i dati in Azure Data Lake Storage Gen2.This article outlines how to use Copy Activity in Azure Data Factory to copy data from and to Azure Data Lake Storage Gen2, and use Data Flow to transform data in Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

>[!TIP]
>Per lo scenario di migrazione del data lake o del data warehouse, vedere Usare Azure Data Factory per eseguire la migrazione dei dati dal data lake o dal data warehouse ad Azure.For data lake or data warehouse migration scenario, learn more from [Use Azure Data Factory to migrate data from your data lake or data warehouse to Azure.](data-migration-guidance-overview.md)

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Gen2 di Archiviazione di Azure Data Lake è supportato per le attività seguenti:This Azure Data Lake Storage Gen2 connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Elimina attività](delete-activity.md)

Per l'attività Copia, con questo connettore è possibile:

- Copiare i dati da/in Azure Data Lake Storage Gen2 usando la chiave account, l'entità servizio o le identità gestite per le autenticazioni delle risorse di Azure.Copy data from/to Azure Data Lake Storage Gen2 by using account key, service principal, or managed identities for Azure resources authentications.
- Copiare i file così come sono oppure analizzare o generare file con [formati di file e codec di compressione supportati.](supported-file-formats-and-compression-codecs.md)
- [Conservare i metadati](#preserve-metadata-during-copy)del file durante la copia .
- Conservare gli ACL durante la copia da Azure Data Lake Storage Gen1/Gen2.Preserve [ACLs](#preserve-acls) when copying from Azure Data Lake Storage Gen1/Gen2.

>[!IMPORTANT]
>Se si abilita l'opzione **Consenti ai servizi Microsoft attendibili** di accedere a questo account di archiviazione nelle impostazioni del firewall di Archiviazione di Azure e si vuole usare il runtime di integrazione di Azure per connettersi a Data Lake Storage Gen2, è necessario usare [l'autenticazione dell'identità gestita](#managed-identity) per ADLS Gen2.


## <a name="get-started"></a>Introduzione

>[!TIP]
>Per una procedura di procedure su come usare il connettore Data Lake Storage Gen2, vedere [Caricare dati in Azure Data Lake Storage Gen2.](load-azure-data-lake-storage-gen2.md)

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni sulle proprietà utilizzate per definire le entità di Data Factory specifiche di Data Lake Storage Gen2.The following sections provide information about properties that are used to define Data Factory entities specific to Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il connettore Azure Data Lake Storage Gen2 supporta i tipi di autenticazione seguenti. Vedere le sezioni corrispondenti per i dettagli:

- [Autenticazione basata sulla chiave dell'account](#account-key-authentication)
- [Autenticazione dell'entità servizioService principal authentication](#service-principal-authentication)
- [Identità gestite per l'autenticazione delle risorse di Azure](#managed-identity)

>[!NOTE]
>Quando si usa PolyBase per caricare i dati in SQL Data Warehouse, se l'oggetto Data Lake Storage Gen2 di origine è configurato con l'endpoint di rete virtuale, è necessario usare l'autenticazione dell'identità gestita come richiesto da PolyBase. Vedere la sezione [relativa all'autenticazione dell'identità gestita](#managed-identity) con altri prerequisiti di configurazione.

### <a name="account-key-authentication"></a>Autenticazione basata sulla chiave dell'account

Per usare l'autenticazione basata sulla chiave dell'account di archiviazione, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello di `https://<accountname>.dfs.core.windows.net`. | Sì |
| accountKey | Chiave dell'account per Data Lake Storage Gen2. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| connectVia | Runtime [di integrazione](concepts-integration-runtime.md) da utilizzare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati si trova in una rete privata. Se questa proprietà non è specificata, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!NOTE]
>L'endpoint del file system ADLS secondario non è supportato quando si utilizza l'autenticazione con chiave dell'account. È possibile utilizzare altri tipi di autenticazione.

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

Per usare l'autenticazione dell'entità servizio, attenersi alla seguente procedura.

1. Registrare un'entità dell'applicazione in Azure Active Directory (Azure AD) seguendo i passaggi descritti in [Registrare l'applicazione con un tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)di Azure AD. Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio l'autorizzazione appropriata. Vedere esempi sul funzionamento delle autorizzazioni in Data Lake Storage Gen2 dagli elenchi di controllo di [accesso su file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Come origine**: In Storage Explorer, concedere almeno l'autorizzazione di **esecuzione** per TUTTE le cartelle upstream e il file system, insieme all'autorizzazione di **lettura** per i file da copiare. In alternativa, in Controllo di accesso (IAM), concedere almeno il ruolo **Lettore dati BLOB di archiviazione.**
    - **As sink**: In Storage Explorer concedere almeno l'autorizzazione **di esecuzione** per TUTTE le cartelle upstream e il file system, insieme all'autorizzazione **di scrittura** per la cartella del sink. In alternativa, in Controllo di accesso (IAM), concedere almeno il ruolo **Collaboratore dati BLOB di archiviazione.**

>[!NOTE]
>Se si usa l'interfaccia utente di Data Factory per creare e l'entità servizio non è impostata con il ruolo "Lettore/Collaboratore dati BLOB di archiviazione" in IAM, quando si esegue la connessione di test o l'esplorazione/esplorazione delle cartelle, scegliere "Test connessione al percorso del file" o "Sfoglia dal percorso specificato" e specificare un percorso con l'autorizzazione **Lettura e esecuzione** per continuare.

Queste proprietà sono supportate per il servizio collegato:These properties are supported for the linked service:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello di `https://<accountname>.dfs.core.windows.net`. | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo `SecureString` campo come a per archiviarlo in modo sicuro in Data Factory.Mark this field as a to store it securely in Data Factory. In alternativa, è possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarlo passando il mouse nell'angolo superiore destro del portale di Azure.Retrieve it by hovering the mouse in the upper-right corner of the Azure portal. | Sì |
| connectVia | Runtime [di integrazione](concepts-integration-runtime.md) da utilizzare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati si trova in una rete privata. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Identità gestite per l'autenticazione delle risorse di AzureManaged identities for Azure resources authentication

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione Gen2 di Archiviazione dati di Data Lake, in modo simile all'uso della propria entità servizio. Consente a questa factory designata di accedere e copiare i dati da o verso il Data Lake Storage Gen2.

Per usare le identità gestite per l'autenticazione delle risorse di Azure, eseguire la procedura seguente.

1. [Recuperare le informazioni sull'identità gestita](data-factory-service-identity.md#retrieve-managed-identity) di Data Factory copiando il valore dell'ID **oggetto identità gestita** generato insieme alla factory.

2. Concedere l'autorizzazione corretta dell'identità gestita. Vedere esempi sul funzionamento delle autorizzazioni in Data Lake Storage Gen2 dagli elenchi di controllo di [accesso su file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Come origine**: In Storage Explorer, concedere almeno l'autorizzazione di **esecuzione** per TUTTE le cartelle upstream e il file system, insieme all'autorizzazione di **lettura** per i file da copiare. In alternativa, in Controllo di accesso (IAM), concedere almeno il ruolo **Lettore dati BLOB di archiviazione.**
    - **As sink**: In Storage Explorer concedere almeno l'autorizzazione **di esecuzione** per TUTTE le cartelle upstream e il file system, insieme all'autorizzazione **di scrittura** per la cartella del sink. In alternativa, in Controllo di accesso (IAM), concedere almeno il ruolo **Collaboratore dati BLOB di archiviazione.**

>[!NOTE]
>Se si usa l'interfaccia utente di Data Factory per creare e l'identità gestita non è impostata con il ruolo "Lettore/Collaboratore dati BLOB di archiviazione" in IAM, quando si esegue la connessione di test o l'esplorazione/esplorazione delle cartelle, scegliere "Test connessione al percorso del file" o "Sfoglia dal percorso specificato" e specificare un percorso con autorizzazione **Lettura e esecuzione** per continuare.

>[!IMPORTANT]
>Se si usa PolyBase per caricare i dati da Data Lake Storage Gen2 in SQL Data Warehouse, quando si usa l'autenticazione dell'identità gestita per Data Lake Storage Gen2, assicurarsi di seguire anche i passaggi 1 e 2 di [questa guida](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) a 1) registrare il server di database SQL con Azure Active Directory (Azure AD) e 2) assegnare il ruolo Storage Blob Data Contributor al server di database SQL. il resto sono gestiti da Data Factory. Se Data Lake Storage Gen2 è configurato con un endpoint di rete virtuale di Azure, per usare PolyBase per caricare i dati da esso, è necessario usare l'autenticazione dell'identità gestita come richiesto da PolyBase.If your Data Lake Storage Gen2 is configured with an Azure Virtual Network endpoint, to use PolyBase to load data from it, you must use managed identity authentication as required by PolyBase.

Queste proprietà sono supportate per il servizio collegato:These properties are supported for the linked service:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello di `https://<accountname>.dfs.core.windows.net`. | Sì |
| connectVia | Runtime [di integrazione](concepts-integration-runtime.md) da utilizzare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati si trova in una rete privata. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei dataset, vedere [Dataset](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per `location` Data Lake Storage Gen2 nelle impostazioni del set di dati basato sul formato:The following properties are supported for Data Lake Storage Gen2 under settings in the format-based dataset:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà `location` type in nel set di dati deve essere impostata su **AzureBlobFSLocation**. | Sì      |
| Filesystem | Nome del file system Gen2 di Data Lake Storage.                              | No       |
| folderPath | Percorso di una cartella nel file system specificato. Se si desidera utilizzare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni dell'origine attività. | No       |
| fileName   | Il nome del file sotto l'oggetto fileSystem specificato e FolderPath. Se si desidera utilizzare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni dell'origine attività. | No       |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Copiare le configurazioni](copy-activity-overview.md#configuration) delle attività e [le pipeline e le attività.](concepts-pipelines-activities.md) Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 come tipo di origineAzure Data Lake Storage Gen2 as a source type

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le seguenti proprietà sono supportate per `storeSettings` Data Lake Storage Gen2 nelle impostazioni nell'origine della copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà `storeSettings` del tipo in deve essere impostata su **AzureBlobFSReadSettings**. | Sì                                           |
| ricorsiva                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando recursive è impostato su true e il sink è un archivio basato su file, una cartella vuota o una sottocartella non viene copiata o creata nel sink. I valori consentiti sono **true** (impostazione predefinita) e **false**. | No                                            |
| carattere jollyPercorsoCartella       | Percorso della cartella con caratteri jolly nel file system specificato configurato nel set di dati per filtrare le cartelle di origine. <br>I caratteri `*` jolly consentiti sono (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere). Utilizzare `^` per eseguire l'escape se il nome effettivo della cartella ha un carattere jolly o questo carattere di escape all'interno. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| carattere jollyNomefile         | Il nome del file con caratteri jolly nel file system specificato: percorsocartella/carattere jollyPercorsocartella per filtrare i file di origine. <br>I caratteri `*` jolly consentiti sono (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere). Utilizzare `^` per eseguire l'escape se il nome effettivo della cartella ha un carattere jolly o questo carattere di escape all'interno. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì `fileName` se non è specificato nel set di dati |
| modifiedDatetimeStart    | I file filtrano in base all'attributo Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00". <br> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore `modifiedDatetimeEnd` datetime ma è NULL, significa che vengono selezionati i file il cui attributo dell'ultima modifica è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore `modifiedDatetimeStart` datetime ma è NULL, significa che vengono selezionati i file il cui attributo last modified è minore del valore datetime. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| maxConcurrentConnections | Numero di connessioni per la connessione simultanea all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No                                            |

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le seguenti proprietà sono supportate per `storeSettings` Data Lake Storage Gen2 nelle impostazioni nel sink di copia basato sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà `storeSettings` del tipo in deve essere impostata su **AzureBlobFSWriteSettings**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita):</b>mantiene la gerarchia di file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: Unisce tutti i file dalla cartella di origine in un unico file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| blockSizeInMB | Specificare la dimensione del blocco in MB utilizzata per scrivere i dati in ADLS Gen2. Ulteriori informazioni [sui BLOB di blocchi](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Il valore consentito è **compreso tra 4 e 100 MB**. <br/>Per impostazione predefinita, ADF determina automaticamente la dimensione del blocco in base al tipo e ai dati dell'archivio di origine. Per la copia non binaria in ADLS Gen2, la dimensione predefinita del blocco è 100 MB in modo da adattarsi al massimo 4,95 TB di dati. Potrebbe non essere ottimale quando i dati non sono di grandi dimensioni, soprattutto quando si utilizza Runtime di integrazione self-hosted con rete insufficiente con conseguente timeout dell'operazione o problema di prestazioni. È possibile specificare in modo esplicito una dimensione del blocco, assicurandosi che blockSizeInMB 50000 sia sufficientemente grande per archiviare i dati, altrimenti l'esecuzione dell'attività di copia avrà esito negativo. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione simultanea all'archivio dati. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No       |

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

| folderPath | fileName | ricorsiva | Struttura delle cartelle di origine e risultato del filtro (i file in **grassetto** vengono recuperati)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vuoto, usa default) | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vuoto, usa default) | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alcuni esempi dei valori recursive e copyBehavior

In questa sezione viene descritto il comportamento risultante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La sottocartella 1 con File3, File4 e File5 non viene selezionata. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>La sottocartella 1 con File3, File4 e File5 non viene selezionata. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome di file generato automaticamente. Nome generato automaticamente per File1<br/><br/>La sottocartella 1 con File3, File4 e File5 non viene selezionata. |

## <a name="preserve-metadata-during-copy"></a>Mantieni metadati durante la copia

Quando si copiano file da Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 ad Azure Data Lake Storage Gen2/Azure Blob, è possibile scegliere di mantenere i metadati dei file insieme ai dati. Per ulteriori informazioni, vedere [Conservare i metadati](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a>Conservare gli ACL da Data Lake Storage Gen1/Gen2

Quando si copiano file da Azure Data Lake Storage Gen1/Gen2 a Gen2, è possibile scegliere di mantenere gli elenchi di controllo di accesso (ACL) POSIX insieme ai dati. Per ulteriori informazioni, vedere [Conservare gli ACL da Data Lake Storage Gen1/Gen2 a Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Per copiare i dati da Azure Data Lake Storage Gen1 in Gen2 in generale, vedere [Copiare i dati da Azure Data Lake Storage Gen1 a Gen2 con Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) per procedure procedure consigliate e di archiviazione.

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Quando si trasformano i dati nel mapping del flusso di dati, è possibile leggere e scrivere file da Azure Data Lake Storage Gen2 in formato JSON, Avro, Testo delimitato o Parquet. Per altre informazioni, vedere [Trasformazione dell'origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) nella funzionalità di mapping del flusso di dati.

### <a name="source-transformation"></a>Trasformazione della fonte

In the source transformation, you can read from a container, folder, or individual file in Azure Data Lake Storage Gen2. La scheda **Opzioni di origine** consente di gestire la modalità di lettura dei file. 

![Opzioni di origine](media/data-flow/sourceOptions1.png "Opzioni di origine")

**Percorso con caratteri jolly:** L'utilizzo di un modello con caratteri jolly indicherà ad ADF di scorrere ogni cartella e file corrispondenti in una singola trasformazione Origine. Questo è un modo efficace per elaborare più file all'interno di un singolo flusso. Aggiungere più modelli di corrispondenza con caratteri jolly con il segno di cancelazione che viene visualizzato al passaggio del mouse sul modello con caratteri jolly esistente.

Dal contenitore di origine scegliere una serie di file che corrispondano a un modello. Solo il contenitore può essere specificato nel set di dati. Il percorso con caratteri jolly deve pertanto includere anche il percorso della cartella dalla cartella radice.

Esempi di caratteri jolly:

* ```*```Rappresenta qualsiasi set di caratteri
* ```**```Rappresenta la nidificazione di directory ricorsive
* ```?```Sostituisce un carattere
* ```[]```Corrisponde a uno o più caratteri tra parentesi quadre

* ```/data/sales/**/*.csv```Ottiene tutti i file csv in /data/sales
* ```/data/sales/20??/**/```Ottiene tutti i file del XX secolo
* ```/data/sales/*/*/*.csv```Ottiene i file csv due livelli in /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Ottiene tutti i file csv nel 2004 nel mese di dicembre a partire da X o Y preceduti da un numero a due cifre

**Percorso radice partizione:** Se nell'origine file sono state ```key=value``` partizionate cartelle con un formato (ad esempio, anno 2019), è possibile assegnare il livello principale dell'albero delle cartelle della partizione a un nome di colonna nel flusso di dati del flusso di dati.

In primo luogo, impostare un carattere jolly per includere tutti i percorsi che sono le cartelle partizionate più i file foglia che si desidera leggere.

![Impostazioni del file di origine della partizionePartition source file settings](media/data-flow/partfile2.png "Impostazione del file di partizione")

Utilizzare l'impostazione Percorso radice partizione per definire il livello superiore della struttura di cartelle. Quando visualizzi il contenuto dei tuoi dati tramite un'anteprima dei dati, vedrai che ADF aggiungerà le partizioni risolte trovate in ciascuno dei livelli di cartella.

![Percorso radice partizione](media/data-flow/partfile1.png "Anteprima del percorso radice della partizionePartition root path preview")

**Elenco dei file:** Questo è un set di file. Creare un file di testo che includa un elenco di file di percorso relativo da elaborare. Posizionare il puntatore del tutto su questo file di testo.

**Colonna in cui memorizzare il nome del file:** Archiviare il nome del file di origine in una colonna dei dati. Immettere un nuovo nome di colonna qui per memorizzare la stringa del nome del file.

**Dopo il completamento:** Scegliere di non eseguire alcuna operazione con il file di origine dopo l'esecuzione del flusso di dati, eliminare il file di origine o spostare il file di origine. I percorsi per lo spostamento sono relativi.

Per spostare i file di origine in un'altra posizione dopo l'elaborazione, selezionare prima "Sposta" per l'operazione sui file. Quindi, impostare la directory "da". Se non si utilizza alcun carattere jolly per il percorso, l'impostazione "da" sarà la stessa cartella della cartella di origine.

Se si dispone di un percorso di origine con caratteri jolly, la sintassi sarà simile al seguente:If you have a source path with wildcard, your syntax will look like this below:

```/data/sales/20??/**/*.csv```

È possibile specificare "da" come

```/data/sales```

E "a" come

```/backup/priorSales```

In questo caso, tutti i file che sono stati originati in /data/sales vengono spostati in /backup/priorSales.

> [!NOTE]
> Le operazioni sui file vengono eseguite solo quando si avvia il flusso di dati da un'esecuzione della pipeline (debug di una pipeline o esecuzione) che utilizza l'attività Esegui flusso di dati in una pipeline. Le operazioni sui file *non vengono* eseguite in modalità di debug Flusso di dati.

**Filtra per ultima modifica:** È possibile filtrare i file che si elaborano specificando un intervallo di date di quando sono stati modificati l'ultima volta. Tutte le date sono in UTC. 

### <a name="sink-properties"></a>Proprietà sink

In the sink transformation, you can write to either a container or folder in Azure Data Lake Storage Gen2. la scheda **Impostazioni** consente di gestire la modalità di scrittura dei file.

![opzioni lavandino](media/data-flow/file-sink-settings.png "opzioni lavandino")

**Cancellare la cartella:** Determina se la cartella di destinazione viene cancellata prima della scrittura dei dati.

**Opzione nome file:** Determina la modalità di nome dei file di destinazione nella cartella di destinazione. Le opzioni per il nome del file sono:
   * **Default**: Consenti a Spark di denominare i file in base ai valori di default parte.
   * **Modello**: Immettere un modello che enumera i file di output per partizione. Ad esempio, **loans[n].csv** creerà loans1.csv, loans2.csv e così via.
   * **Per partizione**: Immettere un nome file per partizione.
   * **Come dati nella colonna**: Impostare il file di output sul valore di una colonna. Il percorso è relativo al contenitore del set di dati, non alla cartella di destinazione. Se si dispone di un percorso di cartella nel set di dati, verrà sostituito.
   * **Output in un singolo file**: Combinare i file di output partizionati in un unico file denominato. Il percorso è relativo alla cartella del set di dati. Tenere presente che l'operazione di unione può avere esito negativo in base alle dimensioni del nodo. Questa opzione non è consigliata per set di dati di grandi dimensioni.

**Citazione a tutti:** Determina se racchiudere tutti i valori tra virgolette

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
| type | La proprietà type del set di dati deve essere impostata su **AzureBlobFSFile**. |Sì |
| folderPath | Percorso della cartella in Data Lake Storage Gen2. Se il valore non è specificato, punta alla radice. <br/><br/>Il filtro con caratteri jolly è supportato. I caratteri `*` jolly consentiti sono (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere). Utilizzare `^` per eseguire l'escape se il nome effettivo della cartella contiene un carattere jolly o se si trova un carattere di escape. <br/><br/>Esempi: file system/cartella/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |No |
| fileName | Nome o filtro con caratteri jolly per i file nell'oggetto "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per il filtro, i `*` caratteri jolly consentiti `?` sono (corrisponde a zero o più caratteri) e (corrisponde a zero o a un singolo carattere).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Utilizzare `^` per eseguire l'escape se il nome del file effettivo contiene un carattere jolly o se si trova un carattere di escape.<br/><br/>Quando fileName non è specificato per un set di dati di output e **preserveHierarchy** non è specificato nel sink di attività, l'attività di copia genera automaticamente il nome del file con il modello seguente: "*Data.[ ID ID esecuzione attività]. [GUID se FlattenHierarchy]. [format se configurato]. [compressione se configurato]*", ad esempio, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se si copia da un'origine tabulare utilizzando un nome di tabella anziché una query, il modello di nome è "*[nome tabella].[ formato]. [compressione se configurata]*", ad esempio, "MyTable.csv". |No |
| modifiedDatetimeStart | I file filtrano in base all'attributo Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro dei file con enormi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore `modifiedDatetimeEnd` datetime ma è NULL, significa che vengono selezionati i file il cui attributo dell'ultima modifica è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore `modifiedDatetimeStart` datetime ma è NULL, significa che vengono selezionati i file il cui attributo last modified è minore del valore datetime.| No |
| modifiedDatetimeEnd | I file filtrano in base all'attributo Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro dei file con enormi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore `modifiedDatetimeEnd` datetime ma è NULL, significa che vengono selezionati i file il cui attributo dell'ultima modifica è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore `modifiedDatetimeStart` datetime ma è NULL, significa che vengono selezionati i file il cui attributo last modified è minore del valore datetime.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per ulteriori informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), Formato [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [Formato parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un determinato nome, specificare **folderPath** con una parte di cartella e **fileName** con un nome file.<br>Per copiare un sottoinsieme di file in una cartella, specificare **folderPath** con una parte di cartella e **fileName** con un filtro con caratteri jolly. 

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
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando recursive è impostato su true e il sink è un archivio basato su file, una cartella vuota o una sottocartella non viene copiata o creata nel sink.<br/>I valori consentiti sono **true** (impostazione predefinita) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione simultanea all'archivio dati. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

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

### <a name="legacy-copy-activity-sink-model"></a>Modello sink di attività di copia legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **AzureBlobFSSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita):</b>mantiene la gerarchia di file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: Unisce tutti i file dalla cartella di origine in un unico file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione simultanea all'archivio dati. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

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
