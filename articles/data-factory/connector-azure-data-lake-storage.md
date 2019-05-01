---
title: Copiare dati da e in Azure Data Lake Storage Gen2 tramite Data Factory | Microsoft Docs
description: Informazioni su come copiare dati in e da Azure Data Lake Storage Gen2 tramite Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 1b60236d0c83cca75a8a0f826835f9bec8b4b85f
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64876799"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiare dati da e in Azure Data Lake Storage Gen2 tramite Azure Data Factory

Gen2 di archiviazione di Azure Data Lake (ADLS Gen2) è un set di funzionalità dedicato a analitica dei big data, integrate [archiviazione Blob di Azure](../storage/blobs/storage-blobs-introduction.md). Consente di interagire con i dati approfittando dei paradigmi sia del file system che dell'archiviazione di oggetti.

Questo articolo illustra come copiare dati da e verso Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore Azure Data Lake Storage Gen2 è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [supportata matrice di origine/sink](copy-activity-overview.md)
- [Mapping di flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

In particolare, questo connettore supporta:

- La copia dei dati tramite chiave dell'account, entità servizio o identità gestite per l'autenticazione delle risorse di Azure.
- La copia di file così come sono oppure l'analisi o la generazione di file con i [formati di file e i codec di compressione supportati](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Se si abilita lo spazio dei nomi gerarchico, non vi è alcuna interoperabilità delle operazioni tra le API BLOB e ADLS Gen2. Se viene visualizzato l'errore "ErrorCode=FilesystemNotFound" con il messaggio dettagliato "The specified filesystem does not exist." (Il file system specificato non esiste.), significa che il file system sink è stato creato altrove dall'API BLOB anziché dall'API ADLS Gen2. Per risolvere il problema, specificare un nuovo file system con un nome non esistente come nome di un contenitore BLOB e Azure Data Factory creerà automaticamente quel file system durante la copia dei dati.

>[!NOTE]
>Se si abilita l'opzione _"Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione"_ nelle impostazioni del firewall di Archiviazione di Azure, l'uso di Azure Integration Runtime per la connessione a Data Lake Storage Gen2 non riesce a causa di un errore di operazione non consentita, perché Azure Data Factory non viene trattato come servizio Microsoft attendibile. Usare invece il runtime di integrazione self-hosted per la connessione.

## <a name="get-started"></a>Attività iniziali

>[!TIP]
>Per una procedura dettagliata dell'uso del connettore Data Lake Storage Gen2, vedere [Load data into Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md) (Caricare dati in Azure Data Lake Storage Gen2).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il connettore Azure Data Lake Storage Gen2 supporta i tipi di autenticazione seguenti. Vedere la sezione corrispondente per informazioni dettagliate:

- [Autenticazione basata sulla chiave dell'account](#account-key-authentication)
- [Autenticazione di un'entità servizio](#service-principal-authentication)
- [Autenticazione di identità gestite per le risorse di Azure](#managed-identity)

### <a name="account-key-authentication"></a>Autenticazione basata sulla chiave dell'account

Per usare l'autenticazione basata sulla chiave dell'account di archiviazione, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello `https://<accountname>.dfs.core.windows.net`. | Sì |
| accountKey | Chiave dell'account per il servizio Data Lake Storage Gen2. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

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

Per usare l'autenticazione basata su entità servizio, eseguire la procedura seguente:

1. Registrare un'entità applicazione in Azure Active Directory (Azure AD) seguendo le indicazioni fornite in [Registrare l'applicazione con un tenant di Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere l'autorizzazione appropriata dell'entità servizio.

    - **Come origine**, in Storage Explorer, concedere almeno **lettura + esecuzione** autorizzati a elencare e copiare i file nelle cartelle e sottocartelle o concedere **lettura** l'autorizzazione a duplicare un singolo file. In alternativa, nel controllo di accesso (IAM), concedere almeno **lettore di dati Blob di archiviazione** ruolo.
    - **Come sink**, in Storage Explorer, concedere almeno **scrittura + esecuzione** dell'autorizzazione per creare elementi figlio nella cartella. In alternativa, nel controllo di accesso (IAM), concedere almeno **collaboratore ai dati Blob di archiviazione** ruolo.

>[!NOTE]
>Elenco cartelle a partire dal livello di account o al test della connessione, è necessario impostare l'autorizzazione dell'entità servizio viene concesso al **account di archiviazione con autorizzazione "Execute" in IAM**. Ciò vale quando si usano gli strumenti seguenti:
>- **Strumento Copia dati** per creare la pipeline di copia.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione. 
>Nel caso di problema per concedere l'autorizzazione a livello di account, è possibile ignorare test della connessione e il percorso di input manualmente durante la creazione. Attività di copia continuerà a funzionare fino a quando l'entità servizio viene concesso con l'autorizzazione appropriata i file da copiare.

Queste proprietà sono supportate nel servizio collegato:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello `https://<accountname>.dfs.core.windows.net`. | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarle passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

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

### <a name="managed-identity"></a>Autenticazione di identità gestite per le risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione di Azure Data Lake Store Gen2 simile all'uso dell'entità del servizio. Consente questa factory designata di accedere e copiare i dati da e verso i Gen2 Azure Data Lake Store.

Per usare l'autenticazione di identità gestite per le risorse di Azure, seguire questa procedura:

1. [Recuperare informazioni di identità di data factory gestiti](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore di "SERVICE IDENTITY APPLICATION ID" generato con la factory.

2. Concedere un'autorizzazione appropriata l'identità gestita. 

    - **Come origine**, in Storage Explorer, concedere almeno **lettura + esecuzione** autorizzati a elencare e copiare i file nelle cartelle e sottocartelle o concedere **lettura** l'autorizzazione a duplicare un singolo file. In alternativa, nel controllo di accesso (IAM), concedere almeno **lettore di dati Blob di archiviazione** ruolo.
    - **Come sink**, in Storage Explorer, concedere almeno **scrittura + esecuzione** dell'autorizzazione per creare elementi figlio nella cartella. In alternativa, nel controllo di accesso (IAM), concedere almeno **collaboratore ai dati Blob di archiviazione** ruolo.

>[!NOTE]
>Elenco cartelle a partire dal livello di account o al test della connessione, è necessario impostare l'autorizzazione di identità gestite concessa al **account di archiviazione con autorizzazione "Execute" in IAM**. Ciò vale quando si usano gli strumenti seguenti:
>- **Strumento Copia dati** per creare la pipeline di copia.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione. 
>Nel caso di problema per concedere l'autorizzazione a livello di account, è possibile ignorare test della connessione e il percorso di input manualmente durante la creazione. Attività di copia continuerà a funzionare, purché l'identità gestita viene concesso con l'autorizzazione appropriata i file da copiare.

Queste proprietà sono supportate nel servizio collegato:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello `https://<accountname>.dfs.core.windows.net`. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). 

- Per la **Parquet e del formato di testo delimitato**, fare riferimento a [set di dati di formato Parquet e testo delimitato](#parquet-and-delimited-text-format-dataset) sezione.
- Per altri formati, ad esempio **formato ORC/Avro/JSON/binario**, fare riferimento a [altri set di dati di formato](#other-format-dataset) sezione.

### <a name="parquet-and-delimited-text-format-dataset"></a>Set di dati di formato parquet e testo delimitato

Per copiare dati da e verso Azure Data Lake Store Gen2 nella **Parquet o formato di testo delimitato**, fare riferimento a [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articolo nel set di dati in base al formato e impostazioni supportate. Le proprietà seguenti sono supportate per Azure Data Lake Store Gen2 sotto `location` impostazioni nel set di dati in base al formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà del tipo sotto `location` nel set di dati deve essere impostata su **AzureBlobFSLocation**. | Sì      |
| fileSystem | Nome del sistema file Gen2 Azure Data Lake Store.                              | No        |
| folderPath | Percorso di cartella nel file system specificato. Se si desidera utilizzare con caratteri jolly alla cartella di filtro, ignorare questa impostazione e specificare nelle impostazioni di origine di attività. | No        |
| fileName   | Il nome del file sotto il file System specificato + folderPath. Se si desidera utilizzare con caratteri jolly per filtrare i file, ignorare questa impostazione e specificare nelle impostazioni di origine di attività. | No        |

> [!NOTE]
> **AzureBlobFSFile** tipo set di dati con formato Parquet, Text indicato nella sezione successiva è ancora supportata come-per attività di copia/ricerca/GetMetadata per compatibilità con le versioni precedenti, ma non funziona con il Mapping di flusso di dati. Consigliabile per usare questo nuovo modello in futuro e Azure Data factory di creazione dell'interfaccia utente è stata attivata per la generazione di questi nuovi tipi.

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen2 linked service name>",
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

### <a name="other-format-dataset"></a>Altri set di dati di formato

Per copiare dati da e verso Azure Data Lake Store Gen2 nella **formato ORC/Avro/JSON/binario**, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureBlobFSFile**. |Sì |
| folderPath | Percorso della cartella in Data Lake Storage Gen2. Se il valore non è specificato, punta alla radice. <br/><br/>I filtri con caratteri jolly sono supportati, i caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per applicare una sequenza di escape se il nome effettivo della cartella include caratteri jolly o tale carattere di escape. <br/><br/>Esempi: file System o alla cartella /, vedere altri esempi nella [esempi di filtro di file e cartelle](#folder-and-file-filter-examples). |No  |
| fileName | **Filtro con nome o carattere jolly** per i file nell'elemento "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per un filtro, i caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per il carattere escape se il nome effettivo del file include caratteri jolly o escape.<br/><br/>Se non si specifica fileName per un set di dati di output e non si specifica **preserveHierarchy** nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il criterio seguente: "*Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]*", ad esempio "Dati.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; se si copia da un'origine tabulare usando il nome tabella anziché la query, il criterio del nome è "*[nome tabella].[formato].[compressione se configurata]*", per esempio "MyTable.csv". |No  |
| modifiedDatetimeStart | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No  |
| modifiedDatetimeEnd | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No  |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs.md#avro-format), [Formato OCR](supported-file-formats-and-compression-codecs.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No  |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un determinato nome, specificare **folderPath** con il percorso della cartella e **fileName** con il nome del file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con il percorso della cartella e **fileName** con il filtro con caratteri jolly. 

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

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere gli articoli sulle [configurazioni per l'attività di copia](copy-activity-overview.md#configuration) e su [pipeline e attività](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 come tipo di origine

- Per la copia da **Parquet e del formato di testo delimitato**, fare riferimento a [Parquet e testo delimitato formato origine](#parquet-and-delimited-text-format-source) sezione.
- Per la copia da altri formati, ad esempio **formato ORC/Avro/JSON/binario**, fare riferimento a [altra origine formato](#other-format-source) sezione.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e testo delimitato formato origine

Per copiare dati da ADLS Gen2 nel **Parquet o formato di testo delimitato**, fare riferimento a [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articolo sull'origine dell'attività copy in base al formato e impostazioni supportate. Le proprietà seguenti sono supportate per Azure Data Lake Store Gen2 sotto `storeSettings` le impostazioni di origine della copia in base al formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà del tipo sotto `storeSettings` deve essere impostata su **AzureBlobFSReadSetting**. | Sì                                           |
| ricorsiva                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No                                             |
| wildcardFolderPath       | Il percorso della cartella con caratteri jolly nel file system specificato configurato nel set di dati nelle cartelle di origine di filtro. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                             |
| wildcardFileName         | Il nome di file con caratteri jolly con il file system specificato + folderPath/wildcardFolderPath per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì se `fileName` non è specificato nel set di dati |
| modifiedDatetimeStart    | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime. | No                                             |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                             |
| maxConcurrentConnections | Il numero delle connessioni per connettersi all'archivio di archiviazione simultaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No                                             |

> [!NOTE]
> Per formato di testo delimitato/Parquet **AzureBlobFSSource** origine dell'attività copy tipo indicato nella sezione successiva è ancora supportata come-sia per la compatibilità con le versioni precedenti. Consigliabile per usare questo nuovo modello in futuro e Azure Data factory di creazione dell'interfaccia utente è stata attivata per la generazione di questi nuovi tipi.

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
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

Per copiare dati da ADLS Gen2 nel **formato ORC/Avro/JSON/binario**, nell'attività di copia sono supportate le proprietà seguenti **origine** sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **AzureBlobFSSource**. |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink.<br/>I valori consentiti sono **true** (predefinito) e **false**. | No  |
| maxConcurrentConnections | Il numero delle connessioni per connettersi all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No  |

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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 come tipo di sink

- Per la copia negli **Parquet e del formato di testo delimitato**, fare riferimento a [Parquet e testo delimitato formato sink](#parquet-and-delimited-text-format-sink) sezione.
- Per la copia in altri formati, ad esempio **formato ORC/Avro/JSON/binario**, fare riferimento a [altri sink formato](#other-format-sink) sezione.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet e il sink di formato di testo delimitato

Per copiare dati in Azure Data Lake Store Gen2 nella **Parquet o formato di testo delimitato**, fare riferimento a [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articolo nel sink dell'attività Copia in base al formato e impostazioni supportate. Le proprietà seguenti sono supportate per Azure Data Lake Store Gen2 sotto `storeSettings` impostazioni nel sink di copia in base al formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà del tipo sotto `storeSettings` deve essere impostata su **AzureBlobFSWriteSetting**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No        |
| maxConcurrentConnections | Il numero delle connessioni per connettersi all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No        |

> [!NOTE]
> Per formato di testo delimitato/Parquet **AzureBlobFSSink** sink dell'attività Copia tipo indicato nella sezione successiva è ancora supportata come-sia per la compatibilità con le versioni precedenti. Consigliabile per usare questo nuovo modello in futuro e Azure Data factory di creazione dell'interfaccia utente è stata attivata per la generazione di questi nuovi tipi.

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
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Altri sink di formato

Per copiare dati in Azure Data Lake Store Gen2 nella **formato ORC/Avro/JSON/binario**, sono supportate le proprietà seguenti nella **sink** sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **AzureBlobFSSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No  |
| maxConcurrentConnections | Il numero delle connessioni per connettersi all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No  |

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

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

Questa sezione descrive il comportamento risultante del percorso cartella e del nome del file con i filtri con caratteri jolly.

| folderPath | fileName | ricorsiva | Struttura delle cartelle di origine e risultato del filtro (i file in **grassetto** sono stati recuperati)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vuoto, usare valore predefinito) | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vuoto, usare valore predefinito) | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alcuni esempi dei valori recursive e copyBehavior

In questa sezione viene descritto il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La cartella di destinazione Cartella1 viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome di file generato automaticamente. Nome generato automaticamente per File1<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Informazioni dettagliate dal [trasformazione sorgente](data-flow-source.md) e [sink trasformazione](data-flow-sink.md) nel Mapping di flusso di dati.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
