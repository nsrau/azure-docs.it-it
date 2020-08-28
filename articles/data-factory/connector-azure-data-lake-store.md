---
title: Copiare i dati da e in Azure Data Lake Storage Gen1
description: Informazioni su come copiare dati da archivi dati di origine supportati ad Azure Data Lake Storage o da Data Lake Storage ad archivi di sink supportati, usando Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: 6728b00a4b77e3a755c6220404a07ae54bb5263a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051237"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiare dati da e in Azure Data Lake Storage Gen1 tramite Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in uso:"]
>
> * [Versione 1](v1/data-factory-azure-datalake-connector.md)
> * [Versione corrente](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come copiare dati da e in Azure Data Lake Storage Gen1. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore Azure Data Lake Storage Gen1 è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md) 
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Delete](delete-activity.md)

In particolare, con il connettore è possibile eseguire queste operazioni:

- Copia di file tramite uno dei seguenti metodi di autenticazione, ovvero entità servizio o identità gestite per le risorse di Azure.
- Copia di file così come sono o analisi o generazione di file con i [formati di file e i codec di compressione supportati](supported-file-formats-and-compression-codecs.md).
- [Mantenimento degli elenchi di controllo di accesso](#preserve-acls-to-data-lake-storage-gen2) durante la copia in Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Se si copiano dati tramite il runtime di integrazione self-hosted, configurare il firewall aziendale in modo da consentire il traffico in uscita verso `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` sulla porta 443. Quest'ultimo è il servizio token di sicurezza di Azure con cui deve comunicare il runtime di integrazione per ottenere il token di accesso.

## <a name="get-started"></a>Introduzione

> [!TIP]
> Per una procedura dettagliata su come usare il connettore Azure Data Lake Storage, vedere [Caricare i dati in Azure Data Lake Storage](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti contengono informazioni sulle proprietà usate per definire entità di Data Factory specifiche per Azure Data Lake Storage.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato ad Azure Data Lake Storage sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà `type` deve essere impostata su **AzureDataLakeStore**. | Sì |
| dataLakeStoreUri | Informazioni sull'account Azure Data Lake Storage. Queste informazioni accettano uno dei seguenti formati: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sì |
| subscriptionId | ID sottoscrizione di Azure a cui l'account Data Lake Storage appartiene. | Richiesto per il sink |
| resourceGroupName | Nome del gruppo di risorse di Azure a cui l'account Data Lake Storage appartiene. | Richiesto per il sink |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, come valore predefinito viene usato Azure Integration Runtime. |No |

### <a name="use-service-principal-authentication"></a>Usare l'autenticazione basata su entità servizio

Per usare l'autenticazione basata su entità servizio, eseguire queste operazioni.

1. Registrare un'entità applicazione in Azure Active Directory e concederle l'accesso a Data Lake Storage. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio l'autorizzazione appropriata. Per gli esempi di funzionamento dell'autorizzazione in Data Lake Storage Gen1, vedere [Controllo di accesso in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Come origine**: In **Esplora dati** > **Accesso**, concedere almeno l'autorizzazione **Esecuzione** per TUTTE le cartelle upstream, inclusa la cartella radice, con l'autorizzazione **Lettura** per i file da copiare. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non è presente alcun requisito per il controllo di accesso a livello di account (IAM).
    - **Come sink**: In **Esplora dati** > **Accesso**, concedere almeno l'autorizzazione **Esegui** per TUTTE le cartelle upstream, inclusa la cartella radice, con l'autorizzazione **Scrittura** per la cartella sink. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come `SecureString` per archiviarlo in modo sicuro in Data Factory oppure [riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant, ad esempio nome di dominio o ID tenant, in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |
| azureCloudType | Per l'autenticazione dell'entità servizio, specificare il tipo di ambiente cloud di Azure in cui è registrata l'applicazione Azure Active Directory. <br/> I valori consentiti sono **AzurePublic**, **AzureChina**, **AzureUsGovernment**e **AzureGermany**. Per impostazione predefinita, viene usato l'ambiente cloud del data factory. | No |

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

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione con Data Lake Storage, analogamente a come si usa l'entità servizio. Consente alla factory designata di accedere e copiare i dati da e in Data Lake Storage.

Per usare l'autenticazione di identità gestite per le risorse di Azure, eseguire queste operazioni.

1. [Recuperare l'identità gestita Data Factory](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore di "Service Identity Application ID" generato con la factory.

2. Concedere all'identità gestita l'accesso a Data Lake Storage. Per gli esempi di funzionamento dell'autorizzazione in Data Lake Storage Gen1, vedere [Controllo di accesso in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Come origine**: In **Esplora dati** > **Accesso**, concedere almeno l'autorizzazione **Esecuzione** per TUTTE le cartelle upstream, inclusa la cartella radice, con l'autorizzazione **Lettura** per i file da copiare. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non è presente alcun requisito per il controllo di accesso a livello di account (IAM).
    - **Come sink**: In **Esplora dati** > **Accesso**, concedere almeno l'autorizzazione **Esegui** per TUTTE le cartelle upstream, inclusa la cartella radice, con l'autorizzazione **Scrittura** per la cartella sink. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**.

In Azure Data Factory non è necessario specificare alcuna proprietà oltre alle informazioni generali di Data Lake Storage nel servizio collegato.

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Azure Data Lake Storage Gen1 nelle impostazioni `location` nel set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà type in `location` nel set di dati deve essere impostata su **AzureDataLakeStoreLocation**. | Sì      |
| folderPath | Percorso di una cartella. Se si intende usare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file nel percorso cartella specificato. Se si intende usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di Azure Data Lake Storage.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Storage come origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Azure Data Lake Storage Gen1 nelle impostazioni `storeSettings` nell'origine della copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                     |
| ------------------------ | ------------------------------------------------------------ | -------------------------------------------- |
| type                     | La proprietà type in `storeSettings` deve essere impostata su **AzureDataLakeStoreReadSettings**. | Sì                                          |
| ***Individuare i file da copiare:*** |  |  |
| OPZIONE 1: percorso statico<br> | Copia dal percorso di cartella/file specificato nel set di dati. Se si vogliono copiare tutti i file da una cartella, specificare anche `wildcardFileName` come `*`. |  |
| OPZIONE 2: intervallo di nomi<br>- listAfter | Recuperare le cartelle o i file il cui nome è seguito da questo valore in ordine alfabetico (esclusivo). Usa il filtro sul lato servizio per ADLS Gen1, che offre prestazioni migliori rispetto a un filtro con caratteri jolly. <br/>Data Factory applica questo filtro al percorso definito nel set di dati ed è supportato un solo livello di entità. Per altri esempi, vedere [esempi di filtro di intervallo di nomi](#name-range-filter-examples). | No |
| OPZIONE 2: intervallo di nomi<br/>- listBefore | Recuperare le cartelle o i file il cui nome è prima di questo valore in ordine alfabetico (inclusivo). Usa il filtro sul lato servizio per ADLS Gen1, che offre prestazioni migliori rispetto a un filtro con caratteri jolly.<br>Data Factory applica questo filtro al percorso definito nel set di dati ed è supportato un solo livello di entità. Per altri esempi, vedere [esempi di filtro di intervallo di nomi](#name-range-filter-examples). | No |
| OPZIONE 3: carattere jolly<br>- wildcardFolderPath | Percorso della cartella con caratteri jolly per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| OPZIONE 3: carattere jolly<br>- wildcardFileName | Nome file con caratteri jolly nel percorso folderPath/wildcardFolderPath specificato per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì |
| OPZIONE 4: un elenco di file<br>- fileListPath | Indica di copiare un determinato set di file. Puntare a un file di testo che include un elenco di file da copiare, un file per riga, che rappresenta il percorso relativo del percorso configurato nel set di dati.<br/>Quando si usa questa opzione, non specificare il nome del file nel set di dati. Per altri esempi, vedere [Esempi di elenco di file](#file-list-examples). |No |
| ***Impostazioni aggiuntive:*** |  | |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. <br>I valori consentiti sono **true** (predefinito) e **false**.<br>Questa proprietà non è applicabile quando si configura `fileListPath`. |No |
| deleteFilesAfterCompletion | Indica se i file binari verranno eliminati dall'archivio di origine dopo che è stato eseguito il passaggio all'archivio di destinazione. L'eliminazione del file è per file, pertanto quando l'attività di copia ha esito negativo, si noterà che alcuni file sono già stati copiati nella destinazione ed eliminati dall'origine, mentre altri ancora rimangono nell'archivio di origine. <br/>Questa proprietà è valida solo nello scenario di copia binaria, in cui gli archivi di origini dati sono BLOB, ADLS Gen1, ADLS Gen2, S3, Google Cloud Storage, file, file di Azure, SFTP o FTP. Valore predefinito: false. |No |
| modifiedDatetimeStart    | Filtro di file basato sull'attributo: Ultima modifica. <br>I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, il che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.<br/>Questa proprietà non è applicabile quando si configura `fileListPath`. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                           |
| enablePartitionDiscovery | Per i file partizionati, specificare se analizzare le partizioni dal percorso del file e aggiungerle come colonne di origine aggiuntive.<br/>I valori consentiti sono **false** (impostazione predefinita) e **true**. | Falso                                            |
| partitionRootPath | Quando è abilitata l'individuazione delle partizioni, specificare il percorso radice assoluto per leggere le cartelle partizionate come colonne di dati.<br/><br/>Se non viene specificato, per impostazione predefinita<br/>-Quando si usa il percorso del file in un set di dati o un elenco di file nell'origine, il percorso radice della partizione è il percorso configurato nel set di dati.<br/>-Quando si usa il filtro di cartelle con caratteri jolly, il percorso radice della partizione è il percorso secondario prima del primo carattere jolly.<br/><br/>Si supponga, ad esempio, di configurare il percorso nel set di dati come "root/folder/Year = 2020/month = 08/Day = 27":<br/>-Se si specifica il percorso radice della partizione come "root/folder/Year = 2020", l'attività di copia genererà altre due colonne `month` e `day` con il valore "08" e "27", oltre alle colonne all'interno dei file.<br/>-Se il percorso radice della partizione non è specificato, non verrà generata alcuna colonna aggiuntiva. | Falso                                            |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi alla risorsa di archiviazione. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No                                           |

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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Storage come sink

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Le proprietà seguenti sono supportate per Azure Data Lake Storage Gen1 nelle impostazioni `storeSettings` nel sink di copia basato sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà type in `storeSettings` deve essere impostata su **AzureDataLakeStoreWriteSettings**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| expiryDateTime | Specifica il momento di scadenza dei file scritti. La scadenza viene applicata in orario UTC nel formato "2020-03-01T08:00:00Z". Per impostazione predefinita è NULL, indicando pertanto che i file scritti non sono mai scaduti. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi all'archivio dati. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No       |

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
### <a name="name-range-filter-examples"></a>Esempi di filtro di intervallo di nomi

Questa sezione descrive il comportamento risultante dei filtri di intervallo di nomi.

| Esempio di struttura di origine | Configurazione di Azure Data Factory | Risultato |
|:--- |:--- |:--- |
|root<br/>&nbsp;&nbsp;&nbsp;&nbsp;un<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;bx.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;c<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;cx.csv| **Nel set di dati:**<br>- Percorso cartella: `root`<br><br>**Nell'origine dell'attività Copy:**<br>-Elenco dopo: `a`<br>-Elenca prima: `b`| Verranno quindi copiati i file seguenti:<br><br>root<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv |

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
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadati<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sottocartella1/File3.csv<br>Sottocartella1/File5.csv | **Nel set di dati:**<br>- Percorso cartella: `root/FolderA`<br><br>**Nell'origine dell'attività Copy:**<br>- Percorso elenco file: `root/Metadata/FileListToCopy.txt` <br><br>Il percorso dell'elenco di file fa riferimento a un file di testo nello stesso archivio dati che include un elenco di file da copiare, un file per riga con il percorso relativo del percorso configurato nel set di dati. |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Esempi di comportamento dell'operazione di copia

Questa sezione descrive il comportamento risultante dell'operazione di copia per diverse combinazioni di valori `recursive` e `copyBehavior`.

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con nome generato automaticamente. Nome generato automaticamente per File1<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Mantenere elenchi di controllo di accesso per Data Lake Storage Gen2

>[!TIP]
>Per copiare i dati da Azure Data Lake Storage Gen1 in Gen2 in generale, vedere la procedura dettagliata e le procedure consigliate in [Copiare dati da Azure Data Lake Storage Gen1 a Gen2 con Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md).

Se si desidera replicare gli elenchi di controllo di accesso (ACL) insieme ai file di dati quando si esegue l'aggiornamento da Data Lake Storage Gen1 a Data Lake Storage Gen2, vedere [Mantenere gli elenchi di controllo di accesso da Data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Quando si trasformano i dati nei flussi di dati di mapping, è possibile leggere e scrivere file da Azure Data Lake Storage Gen1 nei formati seguenti:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Testo delimitato](format-delimited-text.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Le impostazioni specifiche del formato si trovano nella documentazione relativa a tale formato. Per ulteriori informazioni, vedere [trasformazione origine nel flusso di dati di mapping](data-flow-source.md) e [trasformazione sink nel flusso di dati del mapping](data-flow-sink.md).

### <a name="source-transformation"></a>Trasformazione origine

Nella trasformazione origine è possibile leggere da un contenitore, una cartella o un singolo file in Azure Data Lake Storage Gen1. La scheda **Source options** (Opzioni origine) consente di gestire la modalità di lettura dei file. 

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

Nella trasformazione sink è possibile scrivere in un contenitore oppure in una cartella in Azure Data Lake Storage Gen1. La scheda **Settings** (Impostazioni) consente di gestire la modalità di scrittura dei file.

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
| type | La proprietà type del set di dati deve essere impostata su **AzureDataLakeStoreFile**. |Sì |
| folderPath | Percorso della cartella in Data Lake Storage. Se il valore non è specificato, punta alla radice. <br/><br/>Il filtro con caratteri jolly è supportato. I caratteri jolly consentiti sono `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per applicare una sequenza di escape se il nome effettivo della cartella include caratteri jolly o tale carattere di escape. <br/><br/>Esempio: cartellaradice/sottocartella/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |No |
| fileName | Filtro con nome o carattere jolly per i file nell'elemento "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per un filtro, i caratteri jolly consentiti sono `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per applicare una sequenza di escape se il nome effettivo del file include caratteri jolly o tale carattere di escape.<br/><br/>Se non si specifica fileName per un set di dati di output e non si specifica **preserveHierarchy** nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il criterio seguente: "*Data.[GUID ID esecuzione attività].[GUID se FlattenHierarchy].[formato se configurato].[compressione se configurata]* ", ad esempio "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se si esegue la copia da un'origine in formato tabulare usando un nome di tabella anziché una query, il modello di nome è " *[nome tabella].[formato].[compressione se configurata]* ", ad esempio "MiaTabella.csv". |No |
| modifiedDatetimeStart | Filtro di file basato sull'ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo compreso tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata in base al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si intende applicare un filtro a grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file. Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| modifiedDatetimeEnd | Filtro di file basato sull'ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'ultima modifica rientra nell'intervallo di tempo compreso tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata in base al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si intende applicare un filtro a grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file. Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime. Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato OCR](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un determinato nome, specificare **folderPath** con un percorso di cartella e **fileName** con un nome file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con un percorso di cartella e **fileName** con un filtro con caratteri jolly. 

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
| type | La proprietà `type` dell'origine dell'attività di copia deve essere impostato su **AzureDataLakeStoreSource**. |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando `recursive` è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata né creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi all'archivio dati. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No |

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

### <a name="legacy-copy-activity-sink-model"></a>Modello di sink dell'attività di copia legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà `type` del sink dell'attività di copia deve essere impostata su **AzureDataLakeStoreSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, il nome del file viene generato automaticamente. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi all'archivio dati. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No |

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
