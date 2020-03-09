---
title: Copia i dati da e verso Azure Data Lake Storage Gen1
description: Informazioni su come copiare dati da archivi dati di origine supportati in Azure Data Lake Store o da Data Lake Store a archivi di sink supportati, usando Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 4dae0d10f103710a0e6039127c5c1cacb63c03c4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394275"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiare dati da o verso Azure Data Lake Storage Gen1 usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory che si sta usando:"]
> * [Versione 1](v1/data-factory-azure-datalake-connector.md)
> * [Versione corrente](connector-azure-data-lake-store.md)

Questo articolo illustra come copiare dati da e verso Azure Data Lake Storage Gen1. Per informazioni su Azure Data Factory, vedere l' [articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Azure Data Lake Storage Gen1 è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md) 
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Elimina attività](delete-activity.md)

In particolare, con questo connettore è possibile:

- Copiare i file usando uno dei metodi di autenticazione seguenti: entità servizio o identità gestite per le risorse di Azure.
- Copiare i file così come sono o analizzare o generare file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).
- [Mantenere gli ACL](#preserve-acls-to-data-lake-storage-gen2) durante la copia in Azure Data Lake storage Gen2.

> [!IMPORTANT]
> Se si copiano i dati tramite il runtime di integrazione self-hosted, configurare il firewall aziendale in modo da consentire il traffico in uscita per `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` sulla porta 443. Quest'ultimo è il servizio token di sicurezza di Azure con cui il runtime di integrazione deve comunicare per ottenere il token di accesso.

## <a name="get-started"></a>Inizia subito

> [!TIP]
> Per una procedura dettagliata sull'uso del connettore Azure Data Lake Store, vedere [caricare dati in Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti forniscono informazioni sulle proprietà che vengono usate per definire Data Factory entità specifiche per Azure Data Lake Store.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Azure Data Lake Store sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| Tipo | Il `type` proprietà deve essere impostata su **AzureDataLakeStore**. | Sì |
| dataLakeStoreUri | Informazioni sull'account Azure Data Lake Store. Queste informazioni assumono uno dei seguenti formati: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sì |
| SubscriptionId | ID sottoscrizione di Azure a cui appartiene l'account Data Lake Store. | Obbligatorio per il sink |
| ResourceGroupName | Nome del gruppo di risorse di Azure a cui appartiene l'account Data Lake Store. | Obbligatorio per il sink |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se l'archivio dati si trova in una rete privata, è possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted. Se questa proprietà non è specificata, viene usato il runtime di integrazione di Azure predefinito. |No |

### <a name="use-service-principal-authentication"></a>Usare l'autenticazione basata su entità servizio

Per utilizzare l'autenticazione basata su entità servizio, attenersi alla seguente procedura.

1. Registrare un'entità applicazione in Azure Active Directory e concedergli l'accesso ai Data Lake Store. Per i passaggi dettagliati, vedere [autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti, che è possibile usare per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio l'autorizzazione appropriata. Vedere esempi su come funziona l'autorizzazione in Data Lake Storage Gen1 dal [controllo di accesso nel Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Come origine**: in **Esplora dati** > **accedere**, concedere almeno l'autorizzazione **Execute** per tutte le cartelle upstream, inclusa la radice, insieme all'autorizzazione di **lettura** per i file da copiare. È possibile scegliere di aggiungere a **questa cartella e a tutti gli elementi figlio** per ricorsivi e aggiungere come **autorizzazione di accesso e una voce di autorizzazione predefinita**. Non sono previsti requisiti per il controllo di accesso a livello di account (IAM).
    - **Come sink**: in **Esplora dati** > **accedere**, concedere almeno l'autorizzazione **Execute** per tutte le cartelle upstream, inclusa la radice, oltre all'autorizzazione di **scrittura** per la cartella sink. È possibile scegliere di aggiungere a **questa cartella e a tutti gli elementi figlio** per ricorsivi e aggiungere come **autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa un runtime di integrazione di Azure per copiare (sia l'origine che il sink si trovano nel cloud), in IAM concedere almeno il ruolo **lettore** per consentire Data Factory rilevare l'area per data Lake Store. Se si vuole evitare questo ruolo IAM, creare in modo esplicito [un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) con il percorso data Lake Store. Ad esempio, se il Data Lake Store si trova in Europa occidentale, creare un runtime di integrazione di Azure con la località impostata su "Europa occidentale". Associarli nel servizio collegato Data Lake Store come illustrato nell'esempio seguente.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come `SecureString` per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| inquilino | Specificare le informazioni sul tenant, ad esempio il nome di dominio o l'ID tenant, in cui risiede l'applicazione. È possibile recuperarlo posizionando il puntatore del mouse nell'angolo in alto a destra della portale di Azure. | Sì |

**Esempio**

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

### <a name="managed-identity"></a>Usare identità gestite per l'autenticazione delle risorse di Azure

Un data factory può essere associato a un' [identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta questo data factory specifico. È possibile usare direttamente questa identità gestita per l'autenticazione Data Lake Store, in modo analogo all'uso della propria entità servizio. Consente a questa factory designata di accedere e copiare i dati da e verso Data Lake Store.

Per usare le identità gestite per l'autenticazione delle risorse di Azure, seguire questa procedura.

1. [Recuperare le informazioni sull'identità gestita di data factory](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore dell'ID dell'applicazione di identità del servizio generato insieme alla Factory.

2. Concedere all'identità gestita l'accesso Data Lake Store. Vedere esempi su come funziona l'autorizzazione in Data Lake Storage Gen1 dal [controllo di accesso nel Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Come origine**: in **Esplora dati** > **accedere**, concedere almeno l'autorizzazione **Execute** per tutte le cartelle upstream, inclusa la radice, insieme all'autorizzazione di **lettura** per i file da copiare. È possibile scegliere di aggiungere a **questa cartella e a tutti gli elementi figlio** per ricorsivi e aggiungere come **autorizzazione di accesso e una voce di autorizzazione predefinita**. Non sono previsti requisiti per il controllo di accesso a livello di account (IAM).
    - **Come sink**: in **Esplora dati** > **accedere**, concedere almeno l'autorizzazione **Execute** per tutte le cartelle upstream, inclusa la radice, oltre all'autorizzazione di **scrittura** per la cartella sink. È possibile scegliere di aggiungere a **questa cartella e a tutti gli elementi figlio** per ricorsivi e aggiungere come **autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa un runtime di integrazione di Azure per copiare (sia l'origine che il sink si trovano nel cloud), in IAM concedere almeno il ruolo **lettore** per consentire Data Factory rilevare l'area per data Lake Store. Se si vuole evitare questo ruolo IAM, creare in modo esplicito [un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) con il percorso data Lake Store. Associarli nel servizio collegato Data Lake Store come illustrato nell'esempio seguente.

In Azure Data Factory non è necessario specificare alcuna proprietà oltre alle informazioni generali sulla Data Lake Store nel servizio collegato.

**Esempio**

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

## <a name="dataset-properties"></a>Proprietà set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere l'articolo [set di impostazioni](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Azure Data Lake Store Gen1 in impostazioni `location` nel set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatorio |
| ---------- | ------------------------------------------------------------ | -------- |
| Tipo       | La proprietà Type in `location` nel set di dati deve essere impostata su **AzureDataLakeStoreLocation**. | Sì      |
| folderPath | Percorso di una cartella. Se si vuole usare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file sotto il folderPath specificato. Se si vuole usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |

**Esempio**

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store come origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Per Azure Data Lake Store Gen1 in impostazioni `storeSettings` nell'origine copia basata sul formato sono supportate le proprietà seguenti:

| Proprietà                 | Descrizione                                                  | Obbligatorio                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| Tipo                     | La proprietà Type in `storeSettings` deve essere impostata su **AzureDataLakeStoreReadSettings**. | Sì                                           |
| Ricorsiva                | Indica se i dati vengono letti in modo ricorsivo dalle sottocartelle o solo dalla cartella specificata. Quando l'opzione ricorsiva è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (impostazione predefinita) e **false**. | No                                            |
| wildcardFolderPath       | Percorso della cartella con caratteri jolly per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo contiene un carattere jolly o il carattere di escape. <br>Vedere altri esempi in [esempi di filtro di file e cartelle](#folder-and-file-filter-examples). | No                                            |
| wildcardFileName         | Nome file con caratteri jolly sotto il folderPath/wildcardFolderPath specificato per filtrare i file di origine. <br>I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo contiene un carattere jolly o il carattere di escape. Vedere altri esempi in [esempi di filtro di file e cartelle](#folder-and-file-filter-examples). | Sì se `fileName` non è specificato nel DataSet |
| modifiedDatetimeStart    | Filtro file basato sull'Ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di tempo compreso tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` dispone di un valore DateTime, ma `modifiedDatetimeEnd` è NULL, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati. Quando `modifiedDatetimeEnd` dispone di un valore DateTime ma `modifiedDatetimeStart` è NULL, significa che i file il cui ultimo attributo è minore del valore DateTime sono selezionati. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| maxConcurrentConnections | Numero di connessioni simultanee per la connessione all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No                                            |

**Esempio**

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

Per Azure Data Lake Store Gen1 in impostazioni `storeSettings` nel sink di copia basato sul formato sono supportate le proprietà seguenti:

| Proprietà                 | Descrizione                                                  | Obbligatorio |
| ------------------------ | ------------------------------------------------------------ | -------- |
| Tipo                     | La proprietà Type in `storeSettings` deve essere impostata su **AzureDataLakeStoreWriteSettings**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è file da un archivio dati basato su file.<br/><br/>I valori consentiti sono:<br/><b>-PreserveHierarchy (impostazione predefinita)</b>: conserva la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>-FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione sono generati automaticamente. <br/><b>-MergeFiles</b>: unisce tutti i file della cartella di origine in un unico file. Se viene specificato il nome del file, il nome del file Unito è il nome specificato. In caso contrario, si tratta di un nome file generato automaticamente. | No       |
| maxConcurrentConnections | Numero di connessioni per la connessione all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No       |

**Esempio**

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

### <a name="folder-and-file-filter-examples"></a>Esempi di filtri di file e cartelle

In questa sezione viene descritto il comportamento risultante del percorso della cartella e del nome file con i filtri con caratteri jolly.

| folderPath | fileName | Ricorsiva | Struttura della cartella di origine e risultato del filtro (vengono recuperati i file in **grassetto** )|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vuoto, USA predefinito) | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv&nbsp;<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Vuoto, USA predefinito) | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file4. JSON**&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**&nbsp;<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;file2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv&nbsp;<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;file2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**&nbsp;<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Esempi di comportamento dell'operazione di copia

In questa sezione viene descritto il comportamento risultante dell'operazione di copia per diverse combinazioni di valori `recursive` e `copyBehavior`.

| Ricorsiva | copyBehavior | Struttura di cartelle di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;file2 &nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Folder1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;file2 &nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;file2 &nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Folder1 di destinazione viene creata con la struttura seguente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il nome generato automaticamente per file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il nome generato automaticamente per file2<br/>&nbsp;&nbsp;&nbsp;&nbsp;il nome generato automaticamente per file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;il nome generato automaticamente per file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;il nome generato automaticamente per File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;file2 &nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Folder1 di destinazione viene creata con la struttura seguente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il contenuto di file1 + file2 + file3 + file4 + File5 vengono uniti in un unico file, con un nome file generato automaticamente. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;file2 &nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Folder1 di destinazione viene creata con la struttura seguente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;file2 &nbsp;<br/><br/>Sottocartella1 con file3, file4 e File5 non vengono prelevati. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;file2 &nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Folder1 di destinazione viene creata con la struttura seguente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il nome generato automaticamente per file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il nome generato automaticamente per file2<br/><br/>Sottocartella1 con file3, file4 e File5 non vengono prelevati. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;file2 &nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Folder1 di destinazione viene creata con la struttura seguente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;il contenuto di file1 + file2 vengono uniti in un unico file con il nome file generato automaticamente. nome generato automaticamente per file1<br/><br/>Sottocartella1 con file3, file4 e File5 non vengono prelevati. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Mantieni ACL per Data Lake Storage Gen2

>[!TIP]
>Per copiare dati da Azure Data Lake Storage Gen1 in Gen2 in generale, vedere [copiare dati da Azure Data Lake storage Gen1 a Gen2 con Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) per una procedura dettagliata e procedure consigliate.

Se si desidera replicare gli elenchi di controllo di accesso (ACL) insieme ai file di dati quando si esegue l'aggiornamento da Data Lake Storage Gen1 a Data Lake Storage Gen2, vedere [mantenere gli ACL da data Lake storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere file da Azure Data Lake Storage Gen1 in formato JSON, avro, testo delimitato o parquet. Per ulteriori informazioni, vedere trasformazione [origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) nella funzionalità flusso di dati mapping.

### <a name="source-transformation"></a>Trasformazione origine

Nella trasformazione origine è possibile leggere da un contenitore, una cartella o un singolo file in Azure Data Lake Storage Gen1. La scheda **Opzioni di origine** consente di gestire il modo in cui i file vengono letti. 

![Opzioni di origine](media/data-flow/sourceOptions1.png "Opzioni di origine")

**Percorso con caratteri jolly:** L'utilizzo di un modello con caratteri jolly indicherà ad ADF di scorrere ogni cartella e file corrispondente in un'unica trasformazione origine. Si tratta di un modo efficace per elaborare più file all'interno di un singolo flusso. Aggiungere più modelli di corrispondenza con caratteri jolly con il segno + visualizzato quando si passa il mouse sul modello con caratteri jolly esistente.

Dal contenitore di origine, scegliere una serie di file che corrispondono a un modello. Nel set di dati è possibile specificare solo il contenitore. Il percorso del carattere jolly deve quindi includere anche il percorso della cartella nella cartella radice.

Esempi di caratteri jolly:

* ```*``` rappresenta un set di caratteri
* ```**``` rappresenta l'annidamento delle directory ricorsive
* ```?``` sostituisce un carattere
* ```[]``` corrisponde a uno o più caratteri tra parentesi quadre

* ```/data/sales/**/*.csv``` ottiene tutti i file CSV in/data/Sales
* ```/data/sales/20??/**``` ottiene tutti i file nel ventesimo secolo
* ```/data/sales/2004/*/12/[XY]1?.csv``` ottiene tutti i file CSV 2004 nel dicembre a partire da X o Y preceduto da un numero a due cifre

**Percorso radice partizione:** Se nell'origine file sono presenti cartelle partizionate con un formato ```key=value``` (ad esempio, Year = 2019), è possibile assegnare il livello principale dell'albero delle cartelle della partizione a un nome di colonna nel flusso di dati del flusso di dati.

Per prima cosa, impostare un carattere jolly per includere tutti i percorsi che rappresentano le cartelle partizionate e i file foglia che si desidera leggere.

![Impostazioni del file di origine della partizione](media/data-flow/partfile2.png "Impostazione del file di partizione")

Utilizzare l'impostazione percorso radice partizione per definire il livello superiore della struttura di cartelle. Quando si Visualizza il contenuto dei dati tramite un'anteprima dei dati, si noterà che ADF aggiungerà le partizioni risolte presenti in ogni livello di cartella.

![Percorso radice partizione](media/data-flow/partfile1.png "Anteprima percorso radice partizione")

**Elenco di file:** Si tratta di un set di file. Creare un file di testo che includa un elenco di file di percorso relativi da elaborare. Puntare a questo file di testo.

**Colonna in cui archiviare il nome del file:** Archiviare il nome del file di origine in una colonna nei dati. Immettere qui il nome di una nuova colonna per archiviare la stringa del nome file.

**Dopo il completamento:** Scegliere di non eseguire alcuna operazione con il file di origine dopo l'esecuzione del flusso di dati, eliminare il file di origine oppure spostare il file di origine. I percorsi per lo spostamento sono relativi.

Per spostare i file di origine in un altro percorso dopo l'elaborazione, selezionare prima di tutto "Sposta" per operazione su file. Quindi, impostare la directory "from". Se non si usano caratteri jolly per il percorso, l'impostazione "da" sarà la stessa cartella della cartella di origine.

Se si dispone di un percorso di origine con carattere jolly, la sintassi avrà un aspetto simile al seguente:

```/data/sales/20??/**/*.csv```

È possibile specificare "from" come

```/data/sales```

E "a" come

```/backup/priorSales```

In questo caso, tutti i file originati in/data/Sales vengono spostati in/backup/priorSales.

> [!NOTE]
> Le operazioni sui file vengono eseguite solo quando si avvia il flusso di dati da un'esecuzione di pipeline (esecuzione del debug o esecuzione di una pipeline) che utilizza l'attività Esegui flusso di dati in una pipeline. Le operazioni sui file *non* vengono eseguite nella modalità di debug del flusso di dati.

**Filtra per Ultima modifica:** È possibile filtrare i file elaborati specificando un intervallo di date di data e ora dell'Ultima modifica. Tutte le ore di data sono in formato UTC. 

### <a name="sink-properties"></a>Proprietà sink

Nella trasformazione sink è possibile scrivere in un contenitore o in una cartella in Azure Data Lake Storage Gen1. la scheda **Impostazioni** consente di gestire il modo in cui i file vengono scritti.

![opzioni sink](media/data-flow/file-sink-settings.png "opzioni sink")

**Cancellare la cartella:** Determina se la cartella di destinazione viene cancellata prima della scrittura dei dati.

**Opzione nome file:** Determina la modalità di denominazione dei file di destinazione nella cartella di destinazione. Le opzioni del nome file sono:
   * **Impostazione predefinita**: consente a Spark di assegnare un nome ai file in base alle impostazioni predefinite della parte.
   * **Modello**: immettere un modello che enumera i file di output per partizione. Ad esempio, **Loans [n]. csv** creerà loans1. csv, loans2. csv e così via.
   * **Per partizione**: immettere un nome di file per partizione.
   * **Come dati in column**: impostare il file di output sul valore di una colonna. Il percorso è relativo al contenitore del set di dati e non alla cartella di destinazione.
   * **Output in un singolo file**: combina i file di output partizionati in un singolo file denominato. Il percorso è relativo alla cartella del set di dati. Tenere presente che l'operazione di merge di te potrebbe avere esito negativo in base alla dimensione del nodo. Questa opzione non è consigliata per i set di impostazioni di grandi dimensioni.

**Tutte le virgolette:** Determina se racchiudere tutti i valori tra virgolette

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare l' [attività GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Elimina proprietà attività

Per informazioni dettagliate sulle proprietà, controllare l' [attività di eliminazione](delete-activity.md)

## <a name="legacy-models"></a>Modelli legacy

>[!NOTE]
>I modelli seguenti sono ancora supportati così come sono per la compatibilità con le versioni precedenti. Si consiglia di usare il nuovo modello menzionato nelle sezioni precedenti in futuro e l'interfaccia utente di creazione di ADF ha cambiato la generazione del nuovo modello.

### <a name="legacy-dataset-model"></a>Modello DataSet legacy

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| Tipo | La proprietà Type del set di dati deve essere impostata su **AzureDataLakeStoreFile**. |Sì |
| folderPath | Percorso della cartella in Data Lake Store. Se non specificato, punta alla radice. <br/><br/>Il filtro con caratteri jolly è supportato. I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo contiene un carattere jolly o il carattere di escape. <br/><br/>Ad esempio: RootFolder/sottocartella/. Vedere altri esempi in [esempi di filtro di file e cartelle](#folder-and-file-filter-examples). |No |
| fileName | Nome o filtro con caratteri jolly per i file sotto il "folderPath" specificato. Se non si specifica un valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per il filtro, i caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo).<br/>-Esempio 1: `"fileName": "*.csv"`<br/>-Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per eseguire l'escape se il nome file effettivo contiene un carattere jolly o il carattere di escape.<br/><br/>Quando fileName non è specificato per un set di dati di output e **preserveHierarchy** non è specificato nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il modello seguente: "*Data. [ GUID ID esecuzione attività]. [GUID se FlattenHierarchy]. [format se configurato]. [compressione se configurata]* ", ad esempio" Data. 0a405f8a-93ff-4C6F-B3BE-f69616f1df7a. txt. gz ". Se si copia da un'origine tabulare usando un nome di tabella anziché una query, il modello del nome è " *[nome tabella]. [ formato]. [compressione se configurata]* ", ad esempio" MyTable. csv ". |No |
| modifiedDatetimeStart | Filtro file basato sull'Ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di tempo compreso tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si desidera eseguire un filtro di file con grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` dispone di un valore DateTime, ma `modifiedDatetimeEnd` è NULL, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati. Quando `modifiedDatetimeEnd` dispone di un valore DateTime ma `modifiedDatetimeStart` è NULL, significa che i file il cui ultimo attributo è minore del valore DateTime sono selezionati.| No |
| modifiedDatetimeEnd | Filtro file basato sull'Ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di tempo compreso tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si desidera eseguire un filtro di file con grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` dispone di un valore DateTime, ma `modifiedDatetimeEnd` è NULL, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati. Quando `modifiedDatetimeEnd` dispone di un valore DateTime ma `modifiedDatetimeStart` è NULL, significa che i file il cui ultimo attributo è minore del valore DateTime sono selezionati.| No |
| formato | Se si desidera copiare i file così come sono tra archivi basati su file (copia binaria), ignorare la sezione del formato nelle definizioni del set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato di file seguenti **:** TextFormat, **JsonFormat**, **AvroFormat**, **OrcFormat**e **ParquetFormat**. Impostare la proprietà **Type** in **Format** su uno di questi valori. Per altre informazioni, vedere le sezioni formato [testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [formato parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione per i dati. Per ulteriori informazioni, vedere [formati di file supportati e codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono **gzip**, **deflate**, **bzip2**e **ZipDeflate**.<br/>I livelli supportati sono **ottimale** e più **veloce**. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath** .<br>Per copiare un singolo file con un nome specifico, specificare **folderPath** con una parte di cartella e **filename** con un nome file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con una parte di cartella e **filename** con un filtro con caratteri jolly. 

**Esempio**

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

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| Tipo | La proprietà `type` dell'origine dell'attività di copia deve essere impostata su **AzureDataLakeStoreSource**. |Sì |
| Ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle sottocartelle o solo dalla cartella specificata. Quando `recursive` è impostato su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (impostazione predefinita) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

**Esempio**

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

### <a name="legacy-copy-activity-sink-model"></a>Modello di sink dell'attività di copia legacy

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| Tipo | La proprietà `type` del sink dell'attività di copia deve essere impostata su **AzureDataLakeStoreSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è file da un archivio dati basato su file.<br/><br/>I valori consentiti sono:<br/><b>-PreserveHierarchy (impostazione predefinita)</b>: conserva la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>-FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione sono generati automaticamente. <br/><b>-MergeFiles</b>: unisce tutti i file della cartella di origine in un unico file. Se viene specificato il nome del file, il nome del file Unito è il nome specificato. In caso contrario, il nome del file viene generato automaticamente. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

**Esempio**

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

Per un elenco di archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
