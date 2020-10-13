---
title: Copiare e trasformare i dati in archiviazione BLOB di Azure
description: Informazioni su come copiare dati da e verso l'archiviazione BLOB e trasformare i dati in archiviazione BLOB usando Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/10/2020
ms.openlocfilehash: dff5e73f9bb02357a6a6f74f5d0db08eee13e76e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332271"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Copiare e trasformare i dati in archiviazione BLOB di Azure con Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-azure-blob-connector.md)
> * [Versione corrente](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in archiviazione BLOB di Azure. Viene inoltre descritto come utilizzare l'attività flusso di dati per trasformare i dati nell'archivio BLOB di Azure. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

>[!TIP]
>Per informazioni su uno scenario di migrazione per un data Lake o una data warehouse, vedere [usare Azure Data Factory per eseguire la migrazione dei dati dal data Lake o data warehouse ad Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di archiviazione BLOB di Azure è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Delete](delete-activity.md)

Per l'attività di copia, questo connettore di archiviazione BLOB supporta:

- La copia di BLOB in e da account di archiviazione di Azure per utilizzo generico e servizi di Archiviazione BLOB ad accesso frequente o sporadico. 
- Copia di BLOB usando una chiave dell'account, una firma di accesso condiviso del servizio, un'entità servizio o identità gestite per le autenticazioni di risorse di Azure.
- La copia di BLOB da BLOB in blocchi, di aggiunta o di pagine e la copia di dati solo in BLOB in blocchi.
- La copia di BLOB così come sono o l'analisi o la generazione di BLOB con [formati di file e codec di compressione supportati](supported-file-formats-and-compression-codecs.md).
- [Mantenimento dei metadati del file durante la copia](#preserving-metadata-during-copy).

>[!IMPORTANT]
>Se si Abilita l'opzione **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** nelle impostazioni del firewall di archiviazione di Azure e si vuole usare il runtime di integrazione di Azure per connettersi all'archiviazione BLOB, è necessario usare l'autenticazione dell' [identità gestita](#managed-identity).

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per Archiviazione BLOB.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Questo connettore di archiviazione BLOB supporta i tipi di autenticazione seguenti. Per informazioni dettagliate, vedere le sezioni corrispondenti.

- [Autenticazione basata sulla chiave dell'account](#account-key-authentication)
- [Autenticazione con firma di accesso condiviso](#shared-access-signature-authentication)
- [Autenticazione di un'entità servizio](#service-principal-authentication)
- [Identità gestite per l'autenticazione delle risorse di Azure](#managed-identity)

>[!NOTE]
>Quando si usa la polibase per caricare i dati in Azure sinapsi Analytics (in precedenza SQL Data Warehouse), se l'archiviazione BLOB di origine o di gestione temporanea è configurata con un endpoint di rete virtuale di Azure, è necessario usare l'autenticazione di identità gestita come richiesto da polibase. È anche necessario usare il runtime di integrazione self-hosted con la versione 3,18 o successiva. Per ulteriori prerequisiti di configurazione, vedere la sezione [autenticazione identità gestita](#managed-identity) .

>[!NOTE]
>Azure HDInsight e le attività Azure Machine Learning supportano solo l'autenticazione che usa le chiavi dell'account di archiviazione BLOB di Azure.

### <a name="account-key-authentication"></a>Autenticazione basata sulla chiave dell'account

Data Factory supporta le seguenti proprietà per l'autenticazione della chiave dell'account di archiviazione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **AzureBlobStorage** (suggerito) o **AzureStorage** (vedere le note seguenti). |Sì |
| connectionString | Specificare le informazioni necessarie per la connessione alla risorsa di archiviazione per la proprietà **ConnectionString** . <br/> È anche possibile inserire la chiave dell'account in Azure Key Vault ed estrarre la `accountKey` configurazione dalla stringa di connessione. Per ulteriori informazioni, vedere gli esempi seguenti e le [credenziali di archiviazione in Azure Key Vault](store-credentials-in-key-vault.md) articolo. |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, il servizio usa il runtime di integrazione di Azure predefinito. |No |

>[!NOTE]
>Un endpoint di servizio BLOB secondario non è supportato quando si usa l'autenticazione della chiave dell'account. È possibile usare altri tipi di autenticazione.

>[!NOTE]
>Se si usa il servizio collegato di tipo "AzureStorage", è ancora supportato così com'è. Tuttavia, è consigliabile usare il nuovo tipo di servizio collegato "AzureBlobStorage" in futuro.

**Esempio:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la chiave dell'account in Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autenticazione con firma di accesso condiviso

Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile usarla per concedere a un client autorizzazioni limitate per gli oggetti nell'account di archiviazione per un periodo di tempo specificato. 

Non è necessario condividere le chiavi di accesso degli account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma di accesso condiviso al costruttore o al metodo appropriato. 

Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory supporta attualmente sia le *firme di accesso condiviso del servizio* che le *firme di accesso condiviso dell'account*. Per altre informazioni sulle firme di accesso condiviso, vedere [concedere l'accesso limitato alle risorse di archiviazione di Azure usando le firme di accesso condiviso](../storage/common/storage-sas-overview.md).
>- Nelle configurazioni del set di dati successive il percorso della cartella è il percorso assoluto a partire dal livello del contenitore. È necessario configurare un set di dati allineato con il percorso nell'URI SAS.

Data Factory supporta le seguenti proprietà per l'uso dell'autenticazione della firma di accesso condiviso:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **AzureBlobStorage** (suggerito) o **AzureStorage** (vedere la nota seguente). |Sì |
| sasUri | Specificare l'URI della firma di accesso condiviso per le risorse di archiviazione, ad esempio BLOB o contenitore. <br/>Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in data factory. È anche possibile inserire il token SAS in Azure Key Vault per usare la rotazione automatica e rimuovere la parte del token. Per ulteriori informazioni, vedere gli esempi seguenti e [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, il servizio usa il runtime di integrazione di Azure predefinito. |No |

>[!NOTE]
>Se si usa il servizio collegato di tipo "AzureStorage", è ancora supportato così com'è. Tuttavia, è consigliabile usare il nuovo tipo di servizio collegato "AzureBlobStorage" in futuro.

**Esempio:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la chiave dell'account in Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Quando si crea un URI di firma di accesso condiviso, tenere presente quanto segue:

- Impostare le autorizzazioni appropriate di lettura o scrittura per gli oggetti in base al modo in cui il servizio collegato (lettura, scrittura, lettura/scrittura) viene usato nella data factory.
- Impostare **Ora di scadenza** in modo appropriato. Assicurarsi che l'accesso agli oggetti di archiviazione non scada nel periodo attivo della pipeline.
- L'URI deve essere creato nel contenitore o nel BLOB a destra in base alla necessità. Un URI di firma di accesso condiviso per un BLOB consente a Data Factory di accedere a tale BLOB specifico. Un URI di firma di accesso condiviso per un contenitore di risorse di archiviazione BLOB consente a Data Factory di passare da un BLOB all'altro nel contenitore. Per consentire l'accesso a più o meno oggetti in un secondo momento o aggiornare l'URI di firma di accesso condiviso, ricordarsi di aggiornare il servizio collegato con il nuovo URI.

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per informazioni generali sull'autenticazione dell'entità servizio di archiviazione di Azure, vedere [autenticare l'accesso ad archiviazione di Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md).

Per usare l'autenticazione basata su entità servizio, eseguire la procedura seguente:

1. Registrare un'entità applicazione in Azure Active Directory (Azure AD) seguendo le indicazioni fornite in [Registrare l'applicazione con un tenant di Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prendere nota di questi valori, che è possibile usare per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio un'autorizzazione appropriata in Archiviazione BLOB di Azure. Per altre informazioni sui ruoli, vedere [gestire i diritti di accesso ai dati di archiviazione di Azure con RBAC](../storage/common/storage-auth-aad-rbac.md).

    - **Come origine**, in **controllo di accesso (IAM)** concedere almeno il ruolo **lettore dati BLOB di archiviazione** .
    - **Come sink**, in **controllo di accesso (IAM)** concedere almeno il ruolo di **collaboratore dati BLOB di archiviazione** .

Per un servizio collegato ad Archiviazione BLOB di Azure sono supportate queste proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **AzureBlobStorage**. |Sì |
| serviceEndpoint | Specificare l'endpoint del servizio di Archiviazione BLOB di Azure con il criterio `https://<accountName>.blob.core.windows.net/`. |Sì |
| accountKind | Specificare il tipo di account di archiviazione. I valori consentiti sono: **storage** (utilizzo generico V1), **archiviazione V2** (utilizzo generico v2), **BlobStorage**o **BlockBlobStorage**. <br/> Quando si usa il servizio collegato BLOB di Azure nel flusso di dati, l'identità gestita o l'autenticazione basata su entità servizio non è supportata quando il tipo di account è vuoto o "archiviazione". Specificare il tipo di account appropriato, scegliere un'autenticazione diversa oppure aggiornare l'account di archiviazione a utilizzo generico V2. |No |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in data factory o [fare riferimento a un segreto archiviato nel Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarla passando il mouse sull'angolo superiore destro della portale di Azure. | Sì |
| azureCloudType | Per l'autenticazione dell'entità servizio, specificare il tipo di ambiente cloud di Azure in cui è registrata l'applicazione Azure Active Directory. <br/> I valori consentiti sono **AzurePublic**, **AzureChina**, **AzureUsGovernment**e **AzureGermany**. Per impostazione predefinita, viene usato l'ambiente cloud del data factory. | No |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, il servizio usa il runtime di integrazione di Azure predefinito. |No |

>[!NOTE]
>L'autenticazione basata su entità servizio è supportata solo dal servizio collegato di tipo "AzureBlobStorage" e non dal precedente servizio collegato di tipo "AzureStorage".

**Esempio:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Identità gestite per l'autenticazione delle risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione dell'archiviazione BLOB, che è simile all'uso della propria entità servizio. Consente a questa factory designata di accedere e copiare i dati da o in un archivio BLOB.

Per informazioni generali sull'autenticazione di archiviazione di Azure, vedere [autenticare l'accesso ad archiviazione di Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md). Per usare le identità gestite per l'autenticazione delle risorse di Azure, seguire questa procedura:

1. [Recuperare data factory informazioni sull'identità gestita](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore dell'ID oggetto identità gestito generato insieme alla Factory.

2. Concedere l'autorizzazione di identità gestita nell'archivio BLOB di Azure. Per altre informazioni sui ruoli, vedere [gestire i diritti di accesso ai dati di archiviazione di Azure con RBAC](../storage/common/storage-auth-aad-rbac.md).

    - **Come origine**, in **controllo di accesso (IAM)** concedere almeno il ruolo **lettore dati BLOB di archiviazione** .
    - **Come sink**, in **controllo di accesso (IAM)** concedere almeno il ruolo di **collaboratore dati BLOB di archiviazione** .

>[!IMPORTANT]
>Se si usa la polibase per caricare dati dall'archivio BLOB (come origine o come gestione temporanea) in Azure sinapsi Analytics (in precedenza SQL Data Warehouse), quando si usa l'autenticazione dell'identità gestita per l'archiviazione BLOB, assicurarsi di seguire anche i passaggi 1 e 2 in [questa guida](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Questa procedura consente di registrare il server con Azure AD e assegnare il ruolo di collaboratore dati BLOB di archiviazione al server. Data Factory gestisce il resto. Se l'archiviazione BLOB è stata configurata con un endpoint di rete virtuale di Azure, per usare la polibase per caricare i dati, è necessario usare l'autenticazione di identità gestita come richiesto da polibase.

Per un servizio collegato ad Archiviazione BLOB di Azure sono supportate queste proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **AzureBlobStorage**. |Sì |
| serviceEndpoint | Specificare l'endpoint del servizio di Archiviazione BLOB di Azure con il criterio `https://<accountName>.blob.core.windows.net/`. |Sì |
| accountKind | Specificare il tipo di account di archiviazione. I valori consentiti sono: **storage** (utilizzo generico V1), **archiviazione V2** (utilizzo generico v2), **BlobStorage**o **BlockBlobStorage**. <br/> Quando si usa il servizio collegato BLOB di Azure nel flusso di dati, l'identità gestita o l'autenticazione basata su entità servizio non è supportata quando il tipo di account è vuoto o "archiviazione". Specificare il tipo di account appropriato, scegliere un'autenticazione diversa oppure aggiornare l'account di archiviazione a utilizzo generico V2. |No |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, il servizio usa il runtime di integrazione di Azure predefinito. |No |

> [!NOTE]
> Le identità gestite per l'autenticazione delle risorse di Azure sono supportate solo dal servizio collegato di tipo "AzureBlobStorage" e non dal precedente servizio collegato di tipo "AzureStorage".

**Esempio:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
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

Le proprietà seguenti sono supportate per l'archiviazione BLOB di Azure in `location` impostazioni in un set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà **Type** della posizione nel set di dati deve essere impostata su **AzureBlobStorageLocation**. | Sì      |
| contenitore  | Contenitore BLOB.                                          | Sì      |
| folderPath | Percorso della cartella sotto il contenitore specificato. Se si vuole usare un carattere jolly per filtrare la cartella, ignorare questa impostazione e specificare che nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file nel percorso specificato del contenitore e della cartella. Se si vuole usare il carattere jolly per filtrare i file, ignorare questa impostazione e specificare che nelle impostazioni dell'origine dell'attività. | No       |

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di archiviazione BLOB.

### <a name="blob-storage-as-a-source-type"></a>Archiviazione BLOB come tipo di origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per l'archiviazione BLOB di Azure in `storeSettings` impostazioni in un'origine copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà **Type** in `storeSettings` deve essere impostata su **AzureBlobStorageReadSettings**. | Sì                                           |
| ***Individuare i file da copiare:*** |  |  |
| OPZIONE 1: percorso statico<br> | Copia dal percorso del contenitore o della cartella/file specificato nel set di dati. Se si desidera copiare tutti i BLOB da un contenitore o da una cartella, specificare `wildcardFileName` anche come `*` . |  |
| OPZIONE 2: prefisso BLOB<br>- prefisso | Prefisso per il nome del BLOB nel contenitore specificato configurato in un set di dati per filtrare i BLOB di origine. Sono selezionati i BLOB i cui nomi iniziano con `container_in_dataset/this_prefix` . Usa il filtro sul lato servizio per l'archiviazione BLOB, che offre prestazioni migliori rispetto a un filtro con caratteri jolly. | No                                                          |
| OPZIONE 3: carattere jolly<br>- wildcardFolderPath | Percorso della cartella con caratteri jolly nel contenitore specificato configurato in un set di dati per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Utilizzare `^` per eseguire l'escape se il nome della cartella contiene caratteri jolly o questo carattere di escape. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| OPZIONE 3: carattere jolly<br>- wildcardFileName | Nome file con caratteri jolly nel contenitore e nel percorso della cartella specificati (o percorso della cartella con caratteri jolly) per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella contiene un carattere jolly o questo carattere di escape all'interno di. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì |
| OPZIONE 4: un elenco di file<br>- fileListPath | Indica di copiare un determinato set di file. Puntare a un file di testo che include un elenco di file da copiare, un file per riga, che rappresenta il percorso relativo del percorso configurato nel set di dati.<br/>Quando si usa questa opzione, non specificare un nome di file nel set di dati. Per altri esempi, vedere [Esempi di elenco di file](#file-list-examples). |No |
| ***Impostazioni aggiuntive:*** |  | |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando l'opzione **ricorsiva** è impostata su **true** e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. <br>I valori consentiti sono **true** (predefinito) e **false**.<br>Questa proprietà non è applicabile quando si configura `fileListPath`. |No |
| deleteFilesAfterCompletion | Indica se i file binari verranno eliminati dall'archivio di origine dopo che è stato eseguito il passaggio all'archivio di destinazione. L'eliminazione del file è per file, pertanto quando l'attività di copia ha esito negativo, si noterà che alcuni file sono già stati copiati nella destinazione ed eliminati dall'origine, mentre altri ancora rimangono nell'archivio di origine. <br/>Questa proprietà è valida solo nello scenario di copia di file binari. Valore predefinito: false. |No |
| modifiedDatetimeStart    | I file vengono filtrati in base all'attributo: data Ultima modifica. <br>I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata a un fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere **null**, il che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è **null**, verranno selezionati i file il cui attributo Last modified è maggiore o uguale al valore DateTime.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è **null**, verranno selezionati i file il cui attributo Last modified è minore del valore DateTime.<br/>Questa proprietà non è applicabile quando si configura `fileListPath`. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| enablePartitionDiscovery | Per i file partizionati, specificare se analizzare le partizioni dal percorso del file e aggiungerle come colonne di origine aggiuntive.<br/>I valori consentiti sono **false** (impostazione predefinita) e **true**. | No                                            |
| partitionRootPath | Quando è abilitata l'individuazione delle partizioni, specificare il percorso radice assoluto per leggere le cartelle partizionate come colonne di dati.<br/><br/>Se non viene specificato, per impostazione predefinita<br/>-Quando si usa il percorso del file in un set di dati o un elenco di file nell'origine, il percorso radice della partizione è il percorso configurato nel set di dati.<br/>-Quando si usa il filtro di cartelle con caratteri jolly, il percorso radice della partizione è il percorso secondario prima del primo carattere jolly.<br/>-Quando si usa il prefisso, il percorso radice della partizione è il percorso secondario prima dell'ultimo "/". <br/><br/>Si supponga, ad esempio, di configurare il percorso nel set di dati come "root/folder/Year = 2020/month = 08/Day = 27":<br/>-Se si specifica il percorso radice della partizione come "root/folder/Year = 2020", l'attività di copia genererà altre due colonne `month` e `day` con il valore "08" e "27", oltre alle colonne all'interno dei file.<br/>-Se il percorso radice della partizione non è specificato, non verrà generata alcuna colonna aggiuntiva. | No                                            |
| maxConcurrentConnections | Numero di connessioni simultanee all'archiviazione. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No                                            |

> [!NOTE]
> Per il formato di testo parquet/delimitato, il tipo di **BlobSource** per l'origine dell'attività di copia indicata nella sezione successiva è ancora supportato come per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello fino a quando l'interfaccia utente di creazione Data Factory non ha cambiato la generazione di questi nuovi tipi.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="blob-storage-as-a-sink-type"></a>Archiviazione BLOB come tipo di sink

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

Le proprietà seguenti sono supportate per l'archiviazione BLOB di Azure in `storeSettings` impostazioni in un sink di copia basato sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà **Type** in `storeSettings` deve essere impostata su **AzureBlobStorageWriteSettings**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome del file o del BLOB , il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| blockSizeInMB | Specificare le dimensioni del blocco, in megabyte, utilizzate per scrivere i dati nei BLOB in blocchi. Altre informazioni sui [BLOB in blocchi](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Il valore consentito è *compreso tra 4 MB e 100 MB*. <br/>Per impostazione predefinita, Data Factory determina automaticamente le dimensioni del blocco in base al tipo e ai dati dell'archivio di origine. Per la copia non binaria nell'archivio BLOB, le dimensioni predefinite del blocco sono pari a 100 MB, in modo che possano adattarsi (al massimo) 4,95 TB di dati. Potrebbe non essere ottimale quando i dati non sono di grandi dimensioni, soprattutto quando si usa il runtime di integrazione self-hosted con connessioni di rete insufficienti che causano problemi di prestazioni o di timeout dell'operazione. È possibile specificare in modo esplicito una dimensione del blocco, garantendo al tempo stesso una dimensione `blockSizeInMB*50000` sufficiente per archiviare i dati. In caso contrario, l'esecuzione dell'attività di copia avrà esito negativo. | No |
| maxConcurrentConnections | Numero di connessioni simultanee all'archiviazione. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No       |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
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
| `container/Folder*` | (vuoto, usare valore predefinito) | false | contenitore<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (vuoto, usare valore predefinito) | true | contenitore<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | contenitore<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | contenitore<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Esempi di elenco di file

In questa sezione viene descritto il comportamento risultante dell'utilizzo di un percorso elenco file nell'origine dell'attività di copia.

Si supponga di avere la seguente struttura di cartelle di origine e di voler copiare i file in grassetto:

| Esempio di struttura di origine                                      | Contenuto in FileListToCopy.txt                             | Configurazione di Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| contenitore<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadati<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sottocartella1/File3.csv<br>Sottocartella1/File5.csv | **Nel set di dati:**<br>- Contenitore: `container`<br>- Percorso cartella: `FolderA`<br><br>**In origine dell'attività di copia:**<br>- Percorso elenco file: `container/Metadata/FileListToCopy.txt` <br><br>Il percorso dell'elenco dei file punta a un file di testo nello stesso archivio dati che include un elenco di file da copiare, un file per riga, con il percorso relativo del percorso configurato nel set di dati. |

### <a name="some-recursive-and-copybehavior-examples"></a>Alcuni esempi dei valori recursive e copyBehavior

In questa sezione viene descritto il comportamento risultante dell'operazione di copia per diverse combinazioni di valori **ricorsivi** e **copyBehavior** .

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La cartella di destinazione, Folder1, viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La cartella di destinazione, Folder1, viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La cartella di destinazione, Folder1, viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La cartella di destinazione, Folder1, viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La cartella di destinazione, Folder1, viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La cartella di destinazione, Folder1, viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome di file generato automaticamente. Nome generato automaticamente per File1<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |

## <a name="preserving-metadata-during-copy"></a>Mantenimento dei metadati durante la copia

Quando si copiano i file da Amazon S3, archiviazione BLOB di Azure o Azure Data Lake Storage Gen2 in Azure Data Lake Storage Gen2 o nell'archiviazione BLOB di Azure, è possibile scegliere di mantenere i metadati dei file insieme ai dati. Altre informazioni in [Mantenere i metadati](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Quando si trasformano i dati nei flussi di dati di mapping, è possibile leggere e scrivere file dall'archiviazione BLOB di Azure nei formati seguenti:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Testo delimitato](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Le impostazioni specifiche del formato si trovano nella documentazione relativa a tale formato. Per ulteriori informazioni, vedere [trasformazione origine nel flusso di dati di mapping](data-flow-source.md) e [trasformazione sink nel flusso di dati del mapping](data-flow-sink.md).

### <a name="source-transformation"></a>Trasformazione origine

In trasformazione origine è possibile leggere da un contenitore, una cartella o un singolo file nell'archivio BLOB di Azure. Utilizzare la scheda **Opzioni di origine** per gestire il modo in cui i file vengono letti. 

![Opzioni origine](media/data-flow/sourceOptions1.png "Opzioni origine")

**Percorsi con caratteri jolly:** L'utilizzo di un modello con caratteri jolly indicherà Data Factory di scorrere ogni cartella e file corrispondente in un'unica trasformazione origine. Si tratta di un modo efficace per elaborare più file all'interno di un singolo flusso. Aggiungere più modelli di corrispondenza con caratteri jolly con il segno più che viene visualizzato quando si passa il mouse sul modello con caratteri jolly esistente.

Nel contenitore di origine scegliere una serie di file che corrispondono a un criterio. Nel set di dati è possibile specificare solo un contenitore. Il percorso con caratteri jolly deve quindi includere anche il percorso della cartella a partire dalla cartella radice.

Esempi di caratteri jolly:

* ```*``` Rappresenta qualsiasi set di caratteri.
* ```**``` Rappresenta l'annidamento di directory ricorsiva.
* ```?``` Sostituisce un carattere.
* ```[]``` Trova la corrispondenza di uno o più caratteri tra parentesi quadre.

* ```/data/sales/**/*.csv``` Ottiene tutti i file con estensione CSV in/data/Sales.
* ```/data/sales/20??/**/``` Ottiene tutti i file nel ventesimo secolo.
* ```/data/sales/*/*/*.csv``` Ottiene i file con estensione CSV a due livelli sotto/data/Sales.
* ```/data/sales/2004/*/12/[XY]1?.csv``` Ottiene tutti i file con estensione CSV nel 2004 dicembre a partire da X o Y preceduto da un numero a due cifre.

**Percorso radice partizione:** Se nell'origine file sono presenti cartelle partizionate con un ```key=value``` formato (ad esempio, `year=2019` ), è possibile assegnare il livello principale dell'albero delle cartelle della partizione a un nome di colonna nel flusso di dati del flusso di dati.

Per prima cosa, impostare un carattere jolly per includere tutti i percorsi che rappresentano le cartelle partizionate e i file foglia che si desidera leggere.

![Impostazioni file di origine partizione](media/data-flow/partfile2.png "Impostazione del file di partizione")

Utilizzare l'impostazione **percorso radice partizione** per definire il livello superiore della struttura di cartelle. Quando si Visualizza il contenuto dei dati tramite un'anteprima dei dati, si noterà che Data Factory aggiungerà le partizioni risolte presenti in ogni livello di cartella.

![Partition Root Path](media/data-flow/partfile1.png "Anteprima percorso radice partizione") (Percorso radice partizione)

**Elenco di file**: set di file. Creare un file di testo che includa un elenco di file di percorsi relativi da elaborare. Puntare a questo file di testo.

**Column to store file name** (Colonna in cui archiviare il nome del file): archiviare il nome del file di origine in una colonna relativa ai dati. Immettere un nuovo nome di colonna per archiviare la stringa del nome file.

**After completion** (Dopo il completamento): scegliere di non eseguire alcuna operazione con il file di origine dopo l'esecuzione del flusso di dati, eliminare il file di origine oppure spostare il file di origine. I percorsi per lo spostamento sono relativi.

Per spostare i file di origine in un altro percorso dopo l'elaborazione, selezionare "Sposta" come operazione sul file. Impostare quindi la directory "da". Se non si usano caratteri jolly per il percorso, l'impostazione "da" sarà la stessa cartella della cartella di origine.

Se si dispone di un percorso di origine con carattere jolly, la sintassi avrà un aspetto simile al seguente:

```/data/sales/20??/**/*.csv```

È possibile specificare "from" come:

```/data/sales```

È anche possibile specificare "to" come:

```/backup/priorSales```

In questo caso, tutti i file originati in/data/sales vengono spostati in /backup/priorSales.

> [!NOTE]
> Le operazioni sui file vengono eseguite solo quando si avvia il flusso di dati da un'esecuzione di pipeline (esecuzione del debug o esecuzione della pipeline) che usa l'attività di esecuzione del flusso di dati in una pipeline. Le operazioni sui file *non* vengono eseguite in modalità di debug del flusso di dati.

**Filter by last modified** (Filtra in base a ultima modifica): è possibile filtrare i file elaborati specificando un intervallo di date relative all'ultima modifica. Tutti i valori DateTime sono in formato UTC. 

### <a name="sink-properties"></a>Proprietà sink

Nella trasformazione sink è possibile scrivere in un contenitore o in una cartella nell'archiviazione BLOB di Azure. Utilizzare la scheda **Impostazioni** per gestire il modo in cui i file vengono scritti.

![Opzioni sink](media/data-flow/file-sink-settings.png "Opzioni sink")

**Clear the folder** (Cancellare la cartella): determina se la cartella di destinazione viene cancellata prima della scrittura dei dati.

**File name option** (Opzione nome file): determina la modalità di denominazione dei file finali nella cartella di destinazione. Le opzioni di nomi di file sono indicate di seguito:
   * **Predefinita**: consente a Spark di denominare i file basati sulle impostazioni predefinite di PART.
   * **Pattern** (Modello): consente di immettere un modello che enumera i file di output per partizione. **loans[n].csv**, ad esempio, creerà loans1.csv, loans2.csv e così via.
   * **Per partition** (Per partizione): consente di immettere un nome di file per partizione.
   * **As data in column** (Come dati in colonna): consente di impostare il file di output sul valore di una colonna. Il percorso è relativo al contenitore del set di dati e non alla cartella di destinazione. Se nel set di dati è presente un percorso di cartella, quest'ultimo verrà ignorato.
   * **Output to a single file** (Output in un singolo file): consente di combinare i file di output partizionati in un singolo file denominato. Il percorso è relativo alla cartella del set di dati. Tenere presente che l'operazione di Unione potrebbe avere esito negativo in base alle dimensioni del nodo. Questa opzione non è consigliata per i set di impostazioni di grandi dimensioni.

**Tutte le virgolette:** Determina se racchiudere tutti i valori tra virgolette.

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
| type | La proprietà **Type** del set di dati deve essere impostata su **AzureBlob**. |Sì |
| folderPath | Percorso del contenitore e della cartella nell'archivio BLOB. <br/><br/>Per il percorso è supportato un filtro con caratteri jolly, escluso il nome del contenitore. I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella contiene un carattere jolly o questo carattere di escape all'interno di. <br/><br/>Un esempio è: contenitoreblob/cartellablob/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |Sì per l'attività di copia o ricerca, no per l'attività GetMetadata |
| fileName | Nome o filtro con caratteri jolly per i BLOB nel valore **folderPath** specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i BLOB nella cartella. <br/><br/>Per il filtro, i caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per eseguire l'escape se il nome del file contiene un carattere jolly o questo carattere di escape.<br/><br/>Quando **filename** non è specificato per un set di dati di output e **preserveHierarchy** non è specificato nel sink dell'attività, l'attività di copia genera automaticamente il nome del BLOB con il modello seguente: "*Data. [ GUID ID esecuzione attività]. [GUID se FlattenHierarchy]. [format se configurato]. [compressione se configurata]*". Ad esempio: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz". <br/><br/>Se si copia da un'origine tabulare usando un nome di tabella anziché una query, il modello del nome è "*[nome tabella]. [ formato]. [compressione se configurata]*". Ad esempio: "MyTable.csv". |No |
| modifiedDatetimeStart | I file vengono filtrati in base all'attributo: data Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata in base al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che l'abilitazione di questa impostazione influirà sulle prestazioni complessive dello spostamento dei dati quando si desidera filtrare grandi quantità di file. <br/><br/> Le proprietà possono essere **null**, il che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è **null**, verranno selezionati i file il cui attributo Last modified è maggiore o uguale al valore DateTime.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è **null**, verranno selezionati i file il cui attributo Last modified è minore del valore DateTime.| No |
| modifiedDatetimeEnd | I file vengono filtrati in base all'attributo: data Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata in base al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che l'abilitazione di questa impostazione influirà sulle prestazioni complessive dello spostamento dei dati quando si desidera filtrare grandi quantità di file. <br/><br/> Le proprietà possono essere **null**, il che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è **null**, verranno selezionati i file il cui attributo Last modified è maggiore o uguale al valore DateTime.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è **null**, verranno selezionati i file il cui attributo Last modified è minore del valore DateTime.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato OCR](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i BLOB in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo BLOB con un nome specificato, specificare **folderPath** per la parte della cartella **e il nome file** per il nome file.<br>Per copiare un subset di BLOB in una cartella, specificare **folderPath** per la parte della cartella e il **nome file** con un filtro con caratteri jolly. 

**Esempio:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>Modello di origine legacy per l'attività di copia

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** dell'origine dell'attività di copia deve essere impostata su **BlobSource**. |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando l'opzione **ricorsiva** è impostata su **true** e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink.<br/>I valori consentiti sono **true** (predefinito) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni simultanee all'archiviazione. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-sink-model-for-the-copy-activity"></a>Modello di sink legacy per l'attività di copia

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del sink dell'attività di copia deve essere impostata su **BlobSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome del file o del BLOB , il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No |
| maxConcurrentConnections | Numero di connessioni simultanee all'archiviazione. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

Per un elenco di archivi dati supportati dall'attività di copia in Data Factory come origini e sink, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
