---
title: Copiare dati da o verso Azure Data Lake Storage Gen1 usando Data Factory | Microsoft Docs
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
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: e0626d847b22c11ce5acca5633c9b1291c03742d
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839866"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiare dati da o verso Azure Data Lake Storage Gen1 usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory che si sta usando:"]
> * [Versione 1](v1/data-factory-azure-datalake-connector.md)
> * [Versione corrente](connector-azure-data-lake-store.md)

Questo articolo illustra come copiare dati da e verso Azure Data Lake Storage Gen1. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Azure Data Lake Storage Gen1 è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine o sink supportata](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

In particolare, con questo connettore è possibile:

- Copiare i file usando uno dei metodi di autenticazione seguenti: entità servizio o identità gestite per le risorse di Azure.
- Copiare i file così come sono o analizzare o generare file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Se si copiano i dati tramite il runtime di integrazione self-hosted, configurare il firewall aziendale in modo da `<ADLS account name>.azuredatalakestore.net` consentire `login.microsoftonline.com/<tenant>/oauth2/token` il traffico in uscita verso e sulla porta 443. Quest'ultimo è il servizio token di sicurezza di Azure con cui deve comunicare il runtime di integrazione per ottenere il token di accesso.

## <a name="get-started"></a>Attività iniziali

> [!TIP]
> Per una procedura dettagliata sull'uso del connettore Azure Data Lake Store, vedere [caricare dati in Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti forniscono informazioni sulle proprietà che vengono usate per definire Data Factory entità specifiche per Azure Data Lake Store.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato ad Azure Data Lake Store sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà `type` deve essere impostata su **AzureDataLakeStore**. | Sì |
| dataLakeStoreUri | Informazioni sull'account Azure Data Lake Store. Queste informazioni accettano uno dei seguenti formati: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sì |
| subscriptionId | ID sottoscrizione di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| resourceGroupName | Nome del gruppo di risorse di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se l'archivio dati si trova in una rete privata, è possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted. Se questa proprietà non è specificata, viene usato il runtime di integrazione di Azure predefinito. |No |

### <a name="use-service-principal-authentication"></a>Usare l'autenticazione basata su entità servizio

Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory e concedere all'entità l'accesso a Data Lake Store. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

- ID applicazione
- Chiave applicazione
- ID tenant

>[!IMPORTANT]
> Concedere all'entità servizio l'autorizzazione appropriata in Data Lake Store:
>- **Come origine**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Lettura + Esecuzione** per elencare e copiare i file in cartelle e sottocartelle. In alternativa, è possibile concedere l'autorizzazione **lettura** per copiare un singolo file. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non sono previsti requisiti per il controllo di accesso a livello di account (IAM).
>- **Come sink**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Scrittura + Esecuzione** per creare elementi figlio nella cartella. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa un runtime di integrazione di Azure per copiare (sia l'origine che il sink si trovano nel cloud), in IAM concedere almeno il ruolo **lettore** per consentire Data Factory rilevare l'area per data Lake Store. Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store. Ad esempio, se il Data Lake Store si trova in Europa occidentale, creare un runtime di integrazione di Azure con la località impostata su "Europa occidentale". Associarli nel servizio collegato Data Lake Store come illustrato nell'esempio seguente.

>[!NOTE]
>Per elencare le cartelle a partire dalla radice, è necessario impostare l'autorizzazione dell'entità servizio concessa sul **livello di radice con autorizzazione "Esecuzione"** . Ciò vale quando si usano gli strumenti seguenti:
>- **Strumento copia dati** per creare una pipeline di copia.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione.
>In caso di dubbi sulla concessione dell'autorizzazione a livello di radice, durante la creazione, ignorare il test della connessione e immettere un percorso paraent con l'autorizzazione concessa, quindi scegliere di selezionare il percorso specificato. L'attività di copia funziona a condizione che all'entità servizio venga concessa l'autorizzazione appropriata nei file da copiare.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
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

### <a name="managed-identity"></a> Usare le entità gestite per l'autenticazione delle risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione Data Lake Store, in modo analogo all'uso della propria entità servizio. Consente alla factory designata di accedere e copiare i dati da e verso Data Lake Store.

Per usare l'autenticazione basata sulle identità gestite per le risorse di Azure:

1. [Recuperare le informazioni sull'identità gestita di data factory](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore dell'ID dell'applicazione di identità del servizio generato insieme alla Factory.
2. Concedere all'identità gestita l'accesso Data Lake Store, come per l'entità servizio, seguendo queste note.

>[!IMPORTANT]
> Assicurarsi di concedere le autorizzazioni appropriate per l'identità gestita data factory in Data Lake Store:
>- **Come origine**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Lettura + Esecuzione** per elencare e copiare i file in cartelle e sottocartelle. In alternativa, è possibile concedere l'autorizzazione **lettura** per copiare un singolo file. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non sono previsti requisiti per il controllo di accesso a livello di account (IAM).
>- **Come sink**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Scrittura + Esecuzione** per creare elementi figlio nella cartella. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa un runtime di integrazione di Azure per copiare (sia l'origine che il sink si trovano nel cloud), in IAM concedere almeno il ruolo **lettore** per consentire Data Factory rilevare l'area per data Lake Store. Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store. Associarli nel servizio collegato Data Lake Store come illustrato nell'esempio seguente.

>[!NOTE]
>Per elencare le cartelle a partire dalla radice, è necessario impostare l'autorizzazione dell'identità gestita da concedere a **a livello di radice con l'autorizzazione "Execute"** . Ciò vale quando si usano gli strumenti seguenti:
>- **Strumento copia dati** per creare una pipeline di copia.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione.
>In caso di dubbi sulla concessione dell'autorizzazione a livello di radice, durante la creazione, ignorare il test della connessione e immettere un percorso padre con l'autorizzazione concessa, quindi scegliere di selezionare il percorso specificato. L'attività di copia funziona a condizione che all'entità servizio venga concessa l'autorizzazione appropriata nei file da copiare.

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

- Per **parquet, testo delimitato e formato binario**, fare riferimento alla sezione [set di dati in formato parquet, testo delimitato e formato binario](#format-based-dataset) .
- Per altri formati come il **formato ORC/avro/JSON**, vedere la sezione [altro set di dati di formato](#other-format-dataset) .

### <a name="format-based-dataset"></a>Set di dati in formato testo e binario delimitato in parquet

Per copiare dati da e verso **parquet, formato testo o binario delimitato**, vedere l'articolo formato [parquet](format-parquet.md), [formato testo delimitato](format-delimited-text.md) e [formato binario](format-binary.md) su set di dati basato su formato e impostazioni supportate.
Per Azure Data Lake Store Gen1 `location` in impostazioni nel set di dati basato su formato, sono supportate le proprietà seguenti:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà `location` Type in nel set di dati deve essere impostata su **AzureDataLakeStoreLocation**. | Sì      |
| folderPath | Percorso di una cartella. Se si vuole usare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file sotto il folderPath specificato. Se si vuole usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |

> [!NOTE]
>
> Il set di dati di tipo **AzureDataLakeStoreFile** con parquet o il formato di testo indicato nella sezione seguente è ancora supportato come per l'attività di copia, ricerca e GetMetadata per la compatibilità con le versioni precedenti. Ma non funziona con la funzionalità del flusso di dati di mapping. Si consiglia di usare questo nuovo modello in futuro. L'interfaccia utente di creazione Data Factory genera questi nuovi tipi.

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

### <a name="other-format-dataset"></a>Set di dati di altri formati

Per copiare dati da e verso Azure Data Lake Store Gen1 nel **formato ORC/avro/JSON**, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su **AzureDataLakeStoreFile**. |Sì |
| folderPath | Percorso della cartella in Data Lake Store. Se il valore non è specificato, punta alla radice. <br/><br/>Il filtro con caratteri jolly è supportato. I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e (corrisponde a `?` zero o a un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo ha un carattere jolly o questo carattere di escape all'interno di. <br/><br/>Ad esempio: RootFolder/sottocartella/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |No |
| fileName | Nome o filtro con caratteri jolly per i file sotto il "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per il filtro, i caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per eseguire l'escape se il nome del file effettivo ha un carattere jolly o questo carattere di escape all'interno di.<br/><br/>Se non si specifica fileName per un set di dati di output e non si specifica **preserveHierarchy** nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il criterio seguente: "*Dati. [GUID ID esecuzione attività]. [GUID se FlattenHierarchy]. [format se configurato]. [compressione se configurata]* ", ad esempio" Data. 0a405f8a-93ff-4C6F-B3BE-f69616f1df7a. txt. gz ". Se si copia da un'origine tabulare usando un nome di tabella anziché una query, il modello del nome è " *[nome tabella]. [ formato]. [compressione se configurata]* ", ad esempio" MyTable. csv ". |No |
| modifiedDatetimeStart | Filtro file basato sull'Ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di tempo `modifiedDatetimeStart` compreso `modifiedDatetimeEnd`tra e. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si desidera eseguire un filtro di file con grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati. Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.| No |
| modifiedDatetimeEnd | Filtro file basato sull'Ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di tempo `modifiedDatetimeStart` compreso `modifiedDatetimeEnd`tra e. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si desidera eseguire un filtro di file con grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati. Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.| No |
| format | Se si desidera copiare i file così come sono tra archivi basati su file (copia binaria), ignorare la sezione del formato nelle definizioni del set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs.md#avro-format), [Formato OCR](supported-file-formats-and-compression-codecs.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compression | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |


>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un nome specifico, specificare **folderPath** con una parte di cartella e **filename** con un nome file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con una parte di cartella e **filename** con un filtro con caratteri jolly. 

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

- Per eseguire la copia da **parquet, testo delimitato e formato binario**, vedere la sezione relativa all' [origine del formato binario e del testo delimitato in parquet](#format-based-source) .
- Per eseguire la copia da altri formati, ad esempio il **formato ORC/avro/JSON**, vedere la sezione [altra origine del formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, testo delimitato e origine del formato binario

Per copiare dati da **parquet, formato testo delimitato o binario**, vedere l'articolo formato [parquet](format-parquet.md), [formato testo delimitato](format-delimited-text.md) e [formato binario](format-binary.md) in origine dell'attività di copia basata su formato e impostazioni supportate.  Per Azure Data Lake Store Gen1 `storeSettings` in impostazioni nell'origine copia basata sul formato sono supportate le proprietà seguenti:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà Type in `storeSettings` deve essere impostata su **AzureDataLakeStoreReadSetting**. | Sì                                           |
| recursive                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando l'opzione ricorsiva è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No                                            |
| wildcardFolderPath       | Percorso della cartella con caratteri jolly per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e (corrisponde a `?` zero o a un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo ha un carattere jolly o questo carattere di escape all'interno di. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| wildcardFileName         | Nome file con caratteri jolly sotto il folderPath/wildcardFolderPath specificato per filtrare i file di origine. <br>I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e (corrisponde a `?` zero o a un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo ha un carattere jolly o questo carattere di escape all'interno di. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì se `fileName` non è specificato nel DataSet |
| modifiedDatetimeStart    | Filtro file basato sull'Ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di tempo `modifiedDatetimeStart` compreso `modifiedDatetimeEnd`tra e. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati. Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| maxConcurrentConnections | Numero di connessioni simultanee per la connessione all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No                                            |

> [!NOTE]
> Per il formato di testo parquet o delimitato, l'origine dell'attività di copia di tipo **AzureDataLakeStoreSource** citata nella sezione seguente è ancora supportata come per la compatibilità con le versioni precedenti. Si consiglia di usare questo nuovo modello in futuro. L'interfaccia utente di creazione Data Factory genera questi nuovi tipi.

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

#### <a name="other-format-source"></a>Altra origine del formato

Per copiare dati da Azure Data Lake Store Gen1 in **formato ORC, avro o JSON**, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La `type` proprietà dell'origine dell'attività di copia deve essere impostata su **AzureDataLakeStoreSource**. |Sì |
| recursive | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando `recursive` è impostato su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

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

- Per la copia in **parquet, testo delimitato o formato binario**, vedere la sezione relativa a [parquet, testo delimitato e sink di formato binario](#format-based-sink) .
- Per eseguire la copia in altri formati come il **formato ORC/avro/JSON**, vedere la sezione [altro sink di formato](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, testo delimitato e sink di formato binario

Per copiare dati in **parquet, testo delimitato o formato binario**, vedere l'articolo formato [parquet](format-parquet.md), formato [testo delimitato](format-delimited-text.md) e [formato binario](format-binary.md) in sink dell'attività di copia basata su formato e impostazioni supportate.  Per Azure Data Lake Store Gen1 `storeSettings` in impostazioni nel sink di copia basato sul formato sono supportate le proprietà seguenti:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà Type in `storeSettings` deve essere impostata su **AzureDataLakeStoreWriteSetting**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| maxConcurrentConnections | Numero di connessioni per la connessione all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No       |

> [!NOTE]
> Per il formato di testo parquet o delimitato, il sink dell'attività di copia di tipo **AzureDataLakeStoreSink** indicato nella sezione seguente è ancora supportato come per la compatibilità con le versioni precedenti. Si consiglia di usare questo nuovo modello in futuro. L'interfaccia utente di creazione Data Factory genera questi nuovi tipi.

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

#### <a name="other-format-sink"></a>Altro sink di formato

Per copiare dati in Azure Data Lake Store Gen1 in **formato ORC, avro o JSON**, nella sezione **sink** sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La `type` proprietà del sink dell'attività di copia deve essere impostata su **AzureDataLakeStoreSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, il nome del file viene generato automaticamente. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

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
| `Folder*` | (Vuoto, USA predefinito) | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vuoto, USA predefinito) | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Esempi di comportamento dell'operazione di copia

Questa sezione descrive il comportamento risultante dell'operazione di copia per diverse combinazioni di valori `recursive` e `copyBehavior`.

| recursive | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5. |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di file1 + file2 + file3 + file4 + File5 viene unito in un file, con un nome file generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Sottocartella1 con file3, file4 e File5 non vengono prelevati. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>Sottocartella1 con file3, file4 e File5 non vengono prelevati. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di file1 + file2 viene unito in un file con il nome file generato automaticamente. Nome generato automaticamente per File1<br/><br/>Sottocartella1 con file3, file4 e File5 non vengono prelevati. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Mantieni ACL per Data Lake Storage Gen2

Se si desidera replicare gli elenchi di controllo di accesso (ACL) insieme ai file di dati quando si esegue l'aggiornamento da Data Lake Storage Gen1 a Data Lake Storage Gen2, vedere [mantenere gli ACL da data Lake storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Ulteriori informazioni sulla trasformazione [origine](data-flow-source.md) e sulla [trasformazione sink](data-flow-sink.md) nella funzionalità flusso di dati di mapping.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
