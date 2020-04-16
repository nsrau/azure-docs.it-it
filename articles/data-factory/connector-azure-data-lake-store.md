---
title: Copiare dati in o da Azure Data Lake Storage Gen1
description: Informazioni su come copiare dati da archivi dati di origine supportati ad Azure Data Lake Store, o da Data Lake Store ad archivi di sink supportati, usando Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 61bb8fe950de8cd9be91bc76bd24aa0151f3fb79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415422"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiare dati in o da Azure Data Lake Storage Gen1 usando Azure Data FactoryCopy data to or from Azure Data Lake Storage Gen1 using Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in uso:Select the version of Azure Data Factory that you're using:"]
> * [Versione 1](v1/data-factory-azure-datalake-connector.md)
> * [Versione corrente](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come copiare i dati da e verso Azure Data Lake Storage Gen1. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Azure Data Lake Storage Gen1 è supportato per le attività seguenti:This Azure Data Lake Storage Gen1 connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md) 
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Elimina attività](delete-activity.md)

In particolare, con questo connettore è possibile:Specifically, with this connector you can:

- Copiare i file usando uno dei metodi di autenticazione seguenti: identità servizio o identità gestite per le risorse di Azure.Copy files by using one of the following methods of authentication: service principal or managed identities for Azure resources.
- Copiare i file così come sono oppure analizzare o generare file con i formati di file e i [codec di compressione supportati.](supported-file-formats-and-compression-codecs.md)
- Conservare gli ACL durante la copia in Azure Data Lake Storage Gen2.Preserve [ACLs](#preserve-acls-to-data-lake-storage-gen2) when copying into Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Se si copiano dati utilizzando il runtime di integrazione self-hosted, configurare il firewall aziendale per consentire il traffico `<ADLS account name>.azuredatalakestore.net` in uscita `login.microsoftonline.com/<tenant>/oauth2/token` sulla porta 443. Quest'ultimo è il servizio token di sicurezza di Azure con cui deve comunicare il runtime di integrazione per ottenere il token di accesso.

## <a name="get-started"></a>Introduzione

> [!TIP]
> Per una procedura guidata su come usare il connettore di Azure Data Lake Store, vedere [Caricare dati in Azure Data Lake Store.](load-azure-data-lake-store.md)

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni sulle proprietà usate per definire le entità di Data Factory specifiche di Azure Data Lake Store.The following sections provide information about properties that are used to define Data Factory entities specific to Azure Data Lake Store.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato ad Azure Data Lake Store sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà `type` deve essere impostata su **AzureDataLakeStore**. | Sì |
| dataLakeStoreUri | Informazioni sull'account Azure Data Lake Store. Queste informazioni accettano uno dei seguenti formati: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sì |
| subscriptionId | ID sottoscrizione di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| resourceGroupName | Nome del gruppo di risorse di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| connectVia | Runtime [di integrazione](concepts-integration-runtime.md) da utilizzare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati si trova in una rete privata. Se questa proprietà non è specificata, viene usato il runtime di integrazione di Azure predefinito. |No |

### <a name="use-service-principal-authentication"></a>Usare l'autenticazione basata su entità servizio

Per usare l'autenticazione dell'entità servizio, attenersi alla seguente procedura.

1. Registrare un'entità dell'applicazione in Azure Active Directory e concederle l'accesso a Data Lake Store.Register an application entity in Azure Active Directory and grant it access to Data Lake Store. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio l'autorizzazione appropriata. Vedere esempi sul funzionamento delle autorizzazioni in Data Lake Storage Gen1 dal [controllo di accesso in Azure Data Lake Storage Gen1.](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)

    - **Come origine**: In**Accesso** **a** > Esplora dati concedere almeno l'autorizzazione di **esecuzione** per TUTTE le cartelle upstream, inclusa la radice, insieme all'autorizzazione **di lettura** per i file da copiare. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non è necessario il controllo degli accessi a livello di account (IAM).
    - **Come sink**: In **Accesso a** > Esplora dati**concedere**almeno l'autorizzazione **di esecuzione** per TUTTE le cartelle upstream, inclusa la radice, insieme all'autorizzazione **di scrittura** per la cartella del sink. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa un runtime di integrazione di Azure per copiare (origine e sink nel cloud), in IAM concedere almeno il ruolo **Lettore** per consentire a Data Factory di rilevare l'area per Data Lake Store. Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store. Ad esempio, se l'archivio dati è nell'Europa occidentale, creare un runtime di integrazione di Azure con la posizione impostata su "Europa occidentale". Associarli al servizio collegato Data Lake Store come illustrato nell'esempio seguente.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come `SecureString` per archiviarlo in modo sicuro in Data Factory oppure [riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant, ad esempio il nome di dominio o l'ID tenant, in cui risiede l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |

**Esempio:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Usare le entità gestite per l'autenticazione delle risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione dell'archivio dati, in modo simile all'uso dell'entità servizio personale. Consente alla factory designata di accedere e copiare i dati da e verso Data Lake Store.

Per usare le identità gestite per l'autenticazione delle risorse di Azure, eseguire la procedura seguente.

1. [Recuperare le informazioni sull'identità gestita della data factory](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore dell'"ID applicazione identità del servizio" generato insieme alla factory.

2. Concedere all'identità gestita l'accesso a Data Lake Store. Vedere esempi sul funzionamento delle autorizzazioni in Data Lake Storage Gen1 dal [controllo di accesso in Azure Data Lake Storage Gen1.](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)

    - **Come origine**: In**Accesso** **a** > Esplora dati concedere almeno l'autorizzazione di **esecuzione** per TUTTE le cartelle upstream, inclusa la radice, insieme all'autorizzazione **di lettura** per i file da copiare. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non è necessario il controllo degli accessi a livello di account (IAM).
    - **Come sink**: In **Accesso a** > Esplora dati**concedere**almeno l'autorizzazione **di esecuzione** per TUTTE le cartelle upstream, inclusa la radice, insieme all'autorizzazione **di scrittura** per la cartella del sink. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa un runtime di integrazione di Azure per copiare (origine e sink nel cloud), in IAM concedere almeno il ruolo **Lettore** per consentire a Data Factory di rilevare l'area per Data Lake Store. Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store. Associarli al servizio collegato Data Lake Store come illustrato nell'esempio seguente.

In Azure Data Factory non è necessario specificare alcuna proprietà oltre alle informazioni generali di Data Lake Store nel servizio collegato.

**Esempio:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

Le proprietà seguenti sono supportate per `location` Azure Data Lake Store Gen1 nelle impostazioni del set di dati basato sul formato:The following properties are supported for Azure Data Lake Store Gen1 under settings in the format-based dataset:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà `location` type in nel set di dati deve essere impostata su **AzureDataLakeStoreLocation**. | Sì      |
| folderPath | Percorso di una cartella. Se si desidera utilizzare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni dell'origine attività. | No       |
| fileName   | Il nome del file sotto il folderPath specificato. Se si desidera utilizzare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni dell'origine attività. | No       |

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store come origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per `storeSettings` Azure Data Lake Store Gen1 nelle impostazioni nell'origine della copia basata sul formato:The following properties are supported for Azure Data Lake Store Gen1 under settings in the format-based copy source:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà `storeSettings` type in deve essere impostata su **AzureDataLakeStoreReadSettings**. | Sì                                           |
| ricorsiva                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando recursive è impostato su true e il sink è un archivio basato su file, una cartella vuota o una sottocartella non viene copiata o creata nel sink. I valori consentiti sono **true** (impostazione predefinita) e **false**. | No                                            |
| carattere jollyPercorsoCartella       | Percorso della cartella con caratteri jolly per filtrare le cartelle di origine. <br>I caratteri `*` jolly consentiti sono (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere). Utilizzare `^` per eseguire l'escape se il nome effettivo della cartella ha un carattere jolly o questo carattere di escape all'interno. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| carattere jollyNomefile         | Il nome del file con caratteri jolly sotto la cartella specificataPercorso/carattere jollyFolderPath per filtrare i file di origine. <br>I caratteri `*` jolly consentiti sono (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere). Utilizzare `^` per eseguire l'escape se il nome effettivo della cartella ha un carattere jolly o questo carattere di escape all'interno. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì `fileName` se non è specificato nel set di dati |
| modifiedDatetimeStart    | I file filtrano in base all'attributo Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00". <br> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore `modifiedDatetimeEnd` datetime ma è NULL, significa che vengono selezionati i file il cui attributo dell'ultima modifica è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore `modifiedDatetimeStart` datetime ma è NULL, significa che vengono selezionati i file il cui attributo last modified è minore del valore datetime. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| maxConcurrentConnections | Numero di connessioni per la connessione simultanea all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No                                            |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSettings",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store come sink

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per `storeSettings` Azure Data Lake Store Gen1 nelle impostazioni del sink di copia basato sul formato:The following properties are supported for Azure Data Lake Store Gen1 under settings in the format-based copy sink:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà `storeSettings` del tipo in deve essere impostata su **AzureDataLakeStoreWriteSettings**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita):</b>mantiene la gerarchia di file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: Unisce tutti i file dalla cartella di origine in un unico file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| expiryDateTime | Specifica l'ora di scadenza dei file scritti. L'ora viene applicata all'ora UTC nel formato "2020-03-01T08:00:00". Per impostazione predefinita è NULL, il che significa che i file scritti non sono mai scaduti. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione simultanea all'archivio dati. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No       |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSettings",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>Esempi di comportamento dell'operazione di copia

Questa sezione descrive il comportamento risultante dell'operazione di copia per diverse combinazioni di valori `recursive` e `copyBehavior`.

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 , File2 , File2 , File3 , File5 viene unito in un unico file, con un nome di file generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene selezionata. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene selezionata. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 - Il contenuto di File2 viene unito in un unico file con il nome del file generato automaticamente. Nome generato automaticamente per File1<br/><br/>La sottocartella1 con File3, File4 e File5 non viene selezionata. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Conservazione degli ACL per Data Lake Storage Gen2

>[!TIP]
>Per copiare i dati da Azure Data Lake Storage Gen1 in Gen2 in generale, vedere [Copiare i dati da Azure Data Lake Storage Gen1 a Gen2 con Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) per procedure procedure consigliate e di archiviazione.

Se si desidera replicare gli elenchi di controllo di accesso (ACL) insieme ai file di dati quando si esegue l'aggiornamento da Data Lake Storage Gen1 a Data Lake Storage Gen2, vedere [Conservare gli ACL da Data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Quando si trasformano i dati nel mapping del flusso di dati, è possibile leggere e scrivere file da Azure Data Lake Storage Gen1 in formato JSON, Avro, Testo delimitato o Parquet. Per altre informazioni, vedere [Trasformazione dell'origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) nella funzionalità di mapping del flusso di dati.

### <a name="source-transformation"></a>Trasformazione della fonte

In the source transformation, you can read from a container, folder or individual file in Azure Data Lake Storage Gen1. La scheda **Opzioni di origine** consente di gestire la modalità di lettura dei file. 

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

In the sink transformation, you can write to either a container or folder in Azure Data Lake Storage Gen1. la scheda **Impostazioni** consente di gestire la modalità di scrittura dei file.

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
| type | La proprietà type del dataset deve essere impostata su **AzureDataLakeStoreFile**. |Sì |
| folderPath | Percorso della cartella in Data Lake Store. Se il valore non è specificato, punta alla radice. <br/><br/>Il filtro con caratteri jolly è supportato. I caratteri `*` jolly consentiti sono (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere). Utilizzare `^` per eseguire l'escape se il nome effettivo della cartella ha un carattere jolly o questo carattere di escape all'interno. <br/><br/>Ad esempio: rootfolder/sottocartell/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |No |
| fileName | Nome o filtro con caratteri jolly per i file nell'oggetto "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per il filtro, i `*` caratteri jolly consentiti `?` sono (corrisponde a zero o più caratteri) e (corrisponde a zero o a un singolo carattere).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Utilizzare `^` per eseguire l'escape se il nome del file effettivo ha un carattere jolly o questo carattere di escape all'interno.<br/><br/>Quando fileName non è specificato per un set di dati di output e **preserveHierarchy** non è specificato nel sink di attività, l'attività di copia genera automaticamente il nome del file con il modello seguente: "*Data.[ ID ID esecuzione attività]. [GUID se FlattenHierarchy]. [format se configurato]. [compressione se configurato]*", ad esempio, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se si copia da un'origine tabulare utilizzando un nome di tabella anziché una query, il modello di nome è "*[nome tabella].[ formato]. [compressione se configurata]*", ad esempio, "MyTable.csv". |No |
| modifiedDatetimeStart | I file filtrano in base all'attributo Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro dei file con enormi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore `modifiedDatetimeEnd` datetime ma è NULL, significa che vengono selezionati i file il cui attributo dell'ultima modifica è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore `modifiedDatetimeStart` datetime ma è NULL, significa che vengono selezionati i file il cui attributo last modified è minore del valore datetime.| No |
| modifiedDatetimeEnd | I file filtrano in base all'attributo Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro dei file con enormi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore `modifiedDatetimeEnd` datetime ma è NULL, significa che vengono selezionati i file il cui attributo dell'ultima modifica è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore `modifiedDatetimeStart` datetime ma è NULL, significa che vengono selezionati i file il cui attributo last modified è minore del valore datetime.| No |
| format | Se si desidera copiare i file così come è tra archivi basati su file (copia binaria), ignorare la sezione relativa al formato nelle definizioni del set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per ulteriori informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), Formato [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato Orco](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [Formato parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un nome particolare, specificare **folderPath** con una parte di cartella e **fileName** con un nome file.<br>Per copiare un sottoinsieme di file in una cartella, specificare **folderPath** con una parte di cartella e **fileName** con un filtro con caratteri jolly. 

**Esempio:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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
| type | La `type` proprietà dell'origine dell'attività di copia deve essere impostata su **AzureDataLakeStoreSource**. |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando `recursive` è impostato su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (impostazione predefinita) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione simultanea all'archivio dati. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
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
| type | La `type` proprietà del sink dell'attività di copia deve essere impostata su **AzureDataLakeStoreSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita):</b>mantiene la gerarchia di file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: Unisce tutti i file dalla cartella di origine in un unico file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, il nome del file viene generato automaticamente. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione simultanea all'archivio dati. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
