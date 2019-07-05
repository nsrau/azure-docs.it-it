---
title: Copiare dati da o verso Azure Data Lake archiviazione Gen1 usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi dati di origine supportati ad Azure Data Lake Store, o da Data Lake Store ad archivi di sink supportati, usando Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: df88c3e2e07165182c917eaf30a5f37451fbd073
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509575"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiare dati da o verso Azure Data Lake archiviazione Gen1 usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in cui in uso:"]
> * [Versione 1](v1/data-factory-azure-datalake-connector.md)
> * [Versione corrente](connector-azure-data-lake-store.md)

Questo articolo illustra come copiare dati da e verso Azure Data Lake archiviazione Gen1. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore Azure Data Lake archiviazione Gen1 è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [supportata matrice di origine o sink](copy-activity-overview.md)
- [Mapping di flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

In particolare, questo connettore è possibile:

- Copiare i file utilizzando uno dei seguenti metodi di autenticazione: identità dell'entità o gestita per le risorse di Azure del servizio.
- Copiare i file così come sono o analizzare o generare file con il [formati di file e i codec di compressione supportati](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Se si copiano dati tramite il runtime di integrazione self-hosted, configurare il firewall aziendale per consentire al traffico in uscita `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` sulla porta 443. Quest'ultimo è il servizio token di sicurezza di Azure con cui deve comunicare il runtime di integrazione per ottenere il token di accesso.

## <a name="get-started"></a>Attività iniziali

> [!TIP]
> Per una procedura dettagliata su come usare il connettore Azure Data Lake Store, vedere [caricare i dati in Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti forniscono informazioni sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Azure Data Lake Store.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato ad Azure Data Lake Store sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà `type` deve essere impostata su **AzureDataLakeStore**. | Sì |
| dataLakeStoreUri | Informazioni sull'account Azure Data Lake Store. Queste informazioni accettano uno dei seguenti formati: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sì |
| subscriptionId | ID sottoscrizione di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| resourceGroupName | Nome del gruppo di risorse di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati si trova in una rete privata. Se questa proprietà non è specificata, viene utilizzato il runtime di integrazione di Azure predefinito. |No |

### <a name="use-service-principal-authentication"></a>Usare l'autenticazione basata su entità servizio

Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory e concedere all'entità l'accesso a Data Lake Store. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

- ID applicazione
- Chiave applicazione
- ID tenant

>[!IMPORTANT]
> Concedere l'autorizzazione appropriata dell'entità servizio in Data Lake Store:
>- **Come origine**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Lettura + Esecuzione** per elencare e copiare i file in cartelle e sottocartelle. In alternativa, è possibile concedere l'autorizzazione **lettura** per copiare un singolo file. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non è richiesto nel controllo di accesso a livello di account (IAM).
>- **Come sink**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Scrittura + Esecuzione** per creare elementi figlio nella cartella. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa un runtime di integrazione di Azure per copiare (sia origine che sink sono nel cloud), nella pagina IAM, concedere almeno il **lettore** ruolo per permettere a Data Factory di rilevare l'area per Data Lake Store. Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store. Ad esempio, se la Store di Data Lake è in Europa occidentale, creare un runtime di integrazione di Azure con località impostata su "Europa occidentale". La loro associazione nel servizio collegato di Data Lake Store come illustrato nell'esempio seguente.

>[!NOTE]
>Per elencare le cartelle a partire dalla radice, è necessario impostare l'autorizzazione dell'entità servizio concessa sul **livello di radice con autorizzazione "Esecuzione"** . Ciò vale quando si usano gli strumenti seguenti:
>- **Lo strumento Copia dati** alla pipeline di copia dell'autore.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione.
>Se si hanno dubbi sulla concessione dell'autorizzazione a livello di radice, durante la creazione, ignorare test connessione e un percorso paraent con autorizzazione concessa quindi si sceglie di passare dall'input che il percorso specificato. Copiare attività works, purché l'entità servizio viene concesso con l'autorizzazione appropriata i file da copiare.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come `SecureString` per archiviarlo in modo sicuro in Data Factory oppure [riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant, ad esempio il nome di dominio o ID, in cui risiede l'applicazione del tenant. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |

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

### <a name="managed-identity"></a> Usare le entità gestite per l'autenticazione delle risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione con Data Lake Store, analogamente all'uso dell'entità del servizio. Consente alla factory designata di accedere e copiare i dati da e verso Data Lake Store.

Per usare l'autenticazione basata sulle identità gestite per le risorse di Azure:

1. [Recuperare le informazioni sull'identità di data factory gestiti](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore del "Service Identity Application ID" generato con la factory.
2. Concedere l'accesso identità gestita per Data Lake Store, la stessa procedura valida per l'entità servizio, seguendo queste note.

>[!IMPORTANT]
> Assicurarsi di che concedere l'autorizzazione appropriata di identità gestita factory i dati in Data Lake Store:
>- **Come origine**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Lettura + Esecuzione** per elencare e copiare i file in cartelle e sottocartelle. In alternativa, è possibile concedere l'autorizzazione **lettura** per copiare un singolo file. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non è richiesto nel controllo di accesso a livello di account (IAM).
>- **Come sink**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Scrittura + Esecuzione** per creare elementi figlio nella cartella. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa un runtime di integrazione di Azure per copiare (sia origine che sink sono nel cloud), nella pagina IAM, concedere almeno il **lettore** ruolo per permettere a Data Factory di rilevare l'area per Data Lake Store. Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store. La loro associazione nel servizio collegato di Data Lake Store come illustrato nell'esempio seguente.

>[!NOTE]
>Elenco cartelle a partire dalla radice, è necessario impostare l'autorizzazione di identità gestite concessa al **a livello di radice con autorizzazione "Execute"** . Ciò vale quando si usano gli strumenti seguenti:
>- **Lo strumento Copia dati** alla pipeline di copia dell'autore.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione.
>Se si hanno dubbi sulla concessione dell'autorizzazione a livello di radice, durante la creazione, ignorare test connessione e un percorso padre con autorizzazione concessa quindi si sceglie di passare dall'input che il percorso specificato. Copiare attività works, purché l'entità servizio viene concesso con l'autorizzazione appropriata i file da copiare.

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). 

- Per il formato di testo delimitato e parquet, vedere la [set di dati di formato Parquet e testo delimitato](#parquet-and-delimited-text-format-dataset) sezione.
- Per altri formati, ad esempio ORC, Avro, JSON o formato binario, vedere la [altri set di dati di formato](#other-format-dataset) sezione.

### <a name="parquet-and-delimited-text-format-dataset"></a>Set di dati di formato parquet e testo delimitato

Per copiare dati da e verso Azure Data Lake Store Gen1 in formato testo delimitato o parquet, vedere la [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articoli nel set di dati in base al formato e le impostazioni supportate. Le proprietà seguenti sono supportate per Azure Data Lake Store Gen1 sotto `location` le impostazioni del set di dati in base al formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà del tipo sotto `location` nel set di dati deve essere impostata su **AzureDataLakeStoreLocation**. | Yes      |
| folderPath | Il percorso in una cartella. Se si desidera usare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni di origine di attività. | No       |
| fileName   | Il nome del file in folderPath specificato. Se si desidera usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni di origine di attività. | No       |

> [!NOTE]
>
> Il **AzureDataLakeStoreFile** tipo set di dati con formato parquet o testo indicato nella sezione seguente è ancora supportata perché è per copia, la ricerca e attività GetMetadata per garantire la compatibilità con le versioni precedenti. Ma non funziona con la funzionalità di flusso di dati di mapping. È consigliabile usare questo nuovo modello in futuro. La creazione dell'interfaccia utente di Data Factory genera questi nuovi tipi.

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

### <a name="other-format-dataset"></a>Altri set di dati di formato

Per copiare dati da e verso Azure Data Lake Store Gen1 in formato binario, JSON, Avro o ORC, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureDataLakeStoreFile**. |Yes |
| folderPath | Percorso della cartella in Data Lake Store. Se il valore non è specificato, punta alla radice. <br/><br/>Filtro con caratteri jolly è supportata. Consentiti sono i caratteri jolly `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o un singolo carattere). Usare `^` di escape se il nome effettivo della cartella contiene un carattere jolly o questo carattere di escape all'interno. <br/><br/>Ad esempio: rootfolder/subfolder /. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |No |
| fileName | Filtro nome o un carattere jolly per i file sotto la proprietà "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per il filtro, i caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o un singolo carattere).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` di escape se il nome del file effettivo include il carattere di escape all'interno o un carattere jolly.<br/><br/>Se non si specifica fileName per un set di dati di output e non si specifica **preserveHierarchy** nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il criterio seguente: "*Dei dati. [GUID dell'ID esecuzione attività]. [GUID se FlattenHierarchy]. [format se configurata]. [la compressione se configurata]* ", ad esempio,"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se si copia da un'origine tabulare usando un nome di tabella anziché una query, è il modello di nome " *[nome della tabella]. [ Format]. [la compressione se configurata]* ", ad esempio,"MyTable.csv". |No |
| modifiedDatetimeStart | Filtro di file basato sull'attributo Data ultima modifica. I file vengono selezionati se loro ora ultima modifica è compreso nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. Viene applicata l'ora al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive di spostamento dei dati dipende dai si abilita questa impostazione quando si desidera file filtro con elevate quantità di file. <br/><br/> La proprietà può essere NULL, il che significa che viene applicato alcun filtro di attributi di file per il set di dati. Quando `modifiedDatetimeStart` ha un valore data/ora ma `modifiedDatetimeEnd` è NULL, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore di data/ora vengono selezionate. Quando `modifiedDatetimeEnd` ha un valore data/ora ma `modifiedDatetimeStart` è NULL, significa che i file il cui ultimo attributo modificato è minore del valore data/ora vengono selezionati.| No |
| modifiedDatetimeEnd | Filtro di file basato sull'attributo Data ultima modifica. I file vengono selezionati se loro ora ultima modifica è compreso nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. Viene applicata l'ora al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive di spostamento dei dati dipende dai si abilita questa impostazione quando si desidera file filtro con elevate quantità di file. <br/><br/> La proprietà può essere NULL, il che significa che viene applicato alcun filtro di attributi di file per il set di dati. Quando `modifiedDatetimeStart` ha un valore data/ora ma `modifiedDatetimeEnd` è NULL, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore di data/ora vengono selezionate. Quando `modifiedDatetimeEnd` ha un valore data/ora ma `modifiedDatetimeStart` è NULL, significa che i file il cui ultimo attributo modificato è minore del valore data/ora vengono selezionati.| No |
| format | Se si desidera copiare file così come sono tra archivi basati su file (copia binaria), ignorare la sezione del formato nelle definizioni dei set di dati di input e output di.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs.md#avro-format), [Formato OCR](supported-file-formats-and-compression-codecs.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compression | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |


>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un nome specifico, specificare **folderPath** con una parte della cartella e **fileName** con un nome file.<br>Per copiare un sottoinsieme di file in una cartella, specificare **folderPath** con una parte della cartella e **fileName** con un filtro con caratteri jolly. 

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

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store come origine

- Per copiare da parquet o formato di testo delimitato, vedere la [Parquet e testo delimitato formato origine](#parquet-and-delimited-text-format-source) sezione.
- Per copiare da altri formati, ad esempio ORC, Avro, JSON o formato binario, vedere la [altra origine formato](#other-format-source) sezione.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e testo delimitato formato origine

Per copiare dati da Azure Data Lake Store Gen1 in formato testo delimitato o parquet, vedere la [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articoli sull'origine dell'attività copy in base al formato e le impostazioni supportate. Le proprietà seguenti sono supportate per Azure Data Lake Store Gen1 sotto `storeSettings` le impostazioni dell'origine di copia in base al formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà del tipo sotto `storeSettings` deve essere impostata su **AzureDataLakeStoreReadSetting**. | Sì                                           |
| recursive                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando recursive è impostata su true e il sink è un archivio basato su file, una cartella vuota o una sottocartella non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No                                            |
| wildcardFolderPath       | Il percorso della cartella con caratteri jolly per filtrare le cartelle di origine. <br>Consentiti sono i caratteri jolly `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o un singolo carattere). Usare `^` di escape se il nome effettivo della cartella contiene un carattere jolly o questo carattere di escape all'interno. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| wildcardFileName         | Il nome di file con caratteri jolly in folderPath/wildcardFolderPath specificata per filtrare i file di origine. <br>Consentiti sono i caratteri jolly `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o un singolo carattere). Usare `^` di escape se il nome effettivo della cartella contiene un carattere jolly o questo carattere di escape all'interno. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì se `fileName` non è specificato nel set di dati |
| modifiedDatetimeStart    | Filtro di file basato sull'attributo Data ultima modifica. I file vengono selezionati se loro ora ultima modifica è compreso nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. Viene applicata l'ora al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br> La proprietà può essere NULL, il che significa che viene applicato alcun filtro di attributi di file per il set di dati. Quando `modifiedDatetimeStart` ha un valore data/ora ma `modifiedDatetimeEnd` è NULL, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore di data/ora vengono selezionate. Quando `modifiedDatetimeEnd` ha un valore data/ora ma `modifiedDatetimeStart` è NULL, significa che i file il cui ultimo attributo modificato è minore del valore data/ora vengono selezionati. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| maxConcurrentConnections | Il numero di connessioni per connettersi all'archivio di archiviazione simultaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No                                            |

> [!NOTE]
> Per parquet o formato di testo delimitato, il **AzureDataLakeStoreSource** origine dell'attività copy tipo indicato nella sezione seguente è ancora supportata perché è per la compatibilità con le versioni precedenti. È consigliabile usare questo nuovo modello in futuro. La creazione dell'interfaccia utente di Data Factory genera questi nuovi tipi.

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSetting",
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

Per copiare dati da Azure Data Lake Store Gen1 in formato binario, JSON, Avro o ORC, le proprietà seguenti sono supportate nell'attività di copia **origine** sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | Il `type` proprietà dell'origine dell'attività di copia deve essere impostata su **AzureDataLakeStoreSource**. |Yes |
| recursive | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando si `recursive` è impostato su true e il sink è un archivio basato su file, una cartella vuota o sottocartella non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No |
| maxConcurrentConnections | Il numero di connessioni per connettersi all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No |

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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store come sink

- Per copiare in formato testo delimitato e parquet, vedere la [Parquet e testo delimitato formato sink](#parquet-and-delimited-text-format-sink) sezione.
- Per copiare in altri formati, ad esempio ORC, Avro, JSON o formato binario, vedere la [altri sink formato](#other-format-sink) sezione.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet e il sink di formato di testo delimitato

Per copiare dati in Azure Data Lake Store Gen1 in formato testo delimitato o parquet, vedere la [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articoli nel sink dell'attività Copia in base al formato e le impostazioni supportate. Le proprietà seguenti sono supportate per Azure Data Lake Store Gen1 sotto `storeSettings` impostazioni nel sink in base al formato di copia:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà del tipo sotto `storeSettings` deve essere impostata su **AzureDataLakeStoreWriteSetting**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| maxConcurrentConnections | Il numero di connessioni per connettersi all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No       |

> [!NOTE]
> Per parquet o formato di testo delimitato, il **AzureDataLakeStoreSink** sink dell'attività Copia tipo indicato nella sezione seguente è ancora supportato in quanto sono per la compatibilità con le versioni precedenti. È consigliabile usare questo nuovo modello in futuro. La creazione dell'interfaccia utente di Data Factory genera questi nuovi tipi.

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
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Altri sink di formato

Per copiare dati in Azure Data Lake Store Gen1 in formato binario, JSON, Avro o ORC, le proprietà seguenti sono supportate nel **sink** sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | Il `type` proprietà del sink dell'attività di copia deve essere impostata su **AzureDataLakeStoreSink**. |Yes |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, il nome del file viene generato automaticamente. | No |
| maxConcurrentConnections | Il numero di connessioni per connettersi all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No |

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

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

Questa sezione descrive il comportamento risultante del percorso cartella e del nome del file con i filtri con caratteri jolly.

| folderPath | fileName | recursive | Struttura delle cartelle di origine e risultato del filtro (i file in **grassetto** sono stati recuperati)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vuota, utilizzare l'impostazione predefinita) | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vuota, utilizzare l'impostazione predefinita) | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Esempi di comportamento dell'operazione di copia

Questa sezione descrive il comportamento risultante dell'operazione di copia per diverse combinazioni di valori `recursive` e `copyBehavior`.

| recursive | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5. |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 contenuto viene unito in un file, con un nome file generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La Sottocartella1 con File3, File4 e File5 non vengono rilevate. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>La Sottocartella1 con File3, File4 e File5 non vengono rilevate. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 contenuto viene uniti in un file con nome file generato automaticamente. Nome generato automaticamente per File1<br/><br/>La Sottocartella1 con File3, File4 e File5 non vengono rilevate. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Mantenere gli ACL per Data Lake Store Gen2

Se si vuole replicare elenchi di controllo di accesso (ACL) insieme ai file di dati durante l'aggiornamento da Data Lake archiviazione Gen1 a Data Lake Storage Gen2, vedere [conservare gli ACL da Data Lake archiviazione Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Altre informazioni sulle [trasformazione sorgente](data-flow-source.md) e [sink trasformazione](data-flow-sink.md) nella funzionalità di flusso di dati di mapping.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
