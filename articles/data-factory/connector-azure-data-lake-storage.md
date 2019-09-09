---
title: Copiare dati da o verso Azure Data Lake Storage Gen2 usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da e verso Azure Data Lake Storage Gen2 usando Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 76c50814c66b941310b73e1d2a52867b0da5aa10
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813594"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiare dati da e in Azure Data Lake Storage Gen2 tramite Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) è un set di funzionalità dedicate a Big Data Analytics incorporate nell' [Archivio BLOB di Azure](../storage/blobs/storage-blobs-introduction.md). È possibile usarlo per interfacciarsi con i dati usando i paradigmi di archiviazione di file system e di oggetti.

Questo articolo illustra come copiare dati da e verso Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Azure Data Lake Storage Gen2 è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine o sink supportata](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

In particolare, con questo connettore è possibile:

- Copiare i dati usando la chiave dell'account, l'entità servizio o le identità gestite per le autenticazioni delle risorse di Azure.
- Copia i file così come sono o analizza o genera file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Se si Abilita lo spazio dei nomi gerarchico, attualmente non esiste alcuna interoperabilità tra le API BLOB e Data Lake Storage Gen2. Se viene visualizzato l'errore "ErrorCode = FilesystemNotFound" con il messaggio "il file system specificato non esiste", è causato dal sink specificato file system creato tramite l'API BLOB anziché Data Lake Storage Gen2 API altrove. Per risolvere il problema, specificare un nuovo file system con un nome che non esiste come nome di un contenitore BLOB. Quindi Data Factory crea automaticamente tale file system durante la copia dei dati.

>[!NOTE]
>Se si Abilita l'opzione **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** nelle impostazioni del firewall di archiviazione di Azure, il runtime di integrazione di Azure non si connette a data Lake storage Gen2 e Mostra un errore non consentito. Il messaggio di errore viene visualizzato perché Data Factory non viene considerato come un servizio Microsoft attendibile. Usare invece il runtime di integrazione self-hosted per connettersi.

## <a name="get-started"></a>Attività iniziali

>[!TIP]
>Per una procedura dettagliata sull'uso del connettore Data Lake Storage Gen2, vedere [caricare dati in Azure Data Lake storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti forniscono informazioni sulle proprietà che vengono usate per definire Data Factory entità specifiche per Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il connettore Azure Data Lake Storage Gen2 supporta i tipi di autenticazione seguenti. Per informazioni dettagliate, vedere le sezioni corrispondenti:

- [Autenticazione basata sulla chiave dell'account](#account-key-authentication)
- [Autenticazione di un'entità servizio](#service-principal-authentication)
- [Autenticazione di identità gestite per le risorse di Azure](#managed-identity)

>[!NOTE]
>Quando si usa la polibase per caricare i dati in SQL Data Warehouse, se il Data Lake Storage Gen2 di origine è configurato con l'endpoint di rete virtuale, è necessario usare l'autenticazione di identità gestita come richiesto da polibase. Vedere la sezione [autenticazione di identità gestita](#managed-identity) con altri prerequisiti di configurazione.

### <a name="account-key-authentication"></a>Autenticazione basata sulla chiave dell'account

Per usare l'autenticazione basata sulla chiave dell'account di archiviazione, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello di `https://<accountname>.dfs.core.windows.net`. | Sì |
| accountKey | Chiave dell'account per Data Lake Storage Gen2. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se l'archivio dati si trova in una rete privata, è possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted. Se questa proprietà non è specificata, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!NOTE]
>L'endpoint ADLS secondario file system non è supportato quando si usa l'autenticazione della chiave dell'account. È possibile utilizzare altri tipi di autenticazione.

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

Per utilizzare l'autenticazione basata su entità servizio, attenersi alla seguente procedura.

1. Registrare un'entità applicazione in Azure Active Directory (Azure AD) attenendosi alla procedura descritta in [registrare l'applicazione con un tenant di Azure ad](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio l'autorizzazione appropriata. Altre informazioni sul funzionamento dell'autorizzazione in Data Lake Storage Gen2 da [elenchi di controllo di accesso su file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Come origine**: In Storage Explorer, concedere almeno l'autorizzazione **Execute** a partire dall'file System di origine, insieme all'autorizzazione di **lettura** per i file da copiare. In alternativa, in controllo di accesso (IAM) concedere almeno il ruolo **lettore dati BLOB di archiviazione** .
    - **Come sink**: In Storage Explorer, concedere almeno l'autorizzazione **Execute** a partire dal sink file System, insieme all'autorizzazione di **scrittura** per la cartella sink. In alternativa, in controllo di accesso (IAM) concedere almeno il ruolo di **collaboratore dati BLOB di archiviazione** .

>[!NOTE]
>Per elencare le cartelle a partire dal livello di account o per testare la connessione, è necessario impostare l'autorizzazione dell'entità servizio che viene concessa all' **account di archiviazione con l'autorizzazione "lettore dati BLOB di archiviazione" in IAM**. Ciò vale quando si usano gli strumenti seguenti:
>- **Strumento copia dati** per creare una pipeline di copia.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione. 
>In caso di dubbi sulla concessione dell'autorizzazione a livello di account, durante la creazione, ignorare il test della connessione e immettere un percorso padre con l'autorizzazione concessa, quindi scegliere di selezionare il percorso specificato. L'attività di copia funziona a condizione che all'entità servizio venga concessa l'autorizzazione appropriata nei file da copiare.

Queste proprietà sono supportate per il servizio collegato:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello di `https://<accountname>.dfs.core.windows.net`. | Yes |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come `SecureString` per archiviarlo in modo sicuro in data factory. In alternativa, è possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarlo posizionando il puntatore del mouse nell'angolo in alto a destra della portale di Azure. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se l'archivio dati si trova in una rete privata, è possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

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

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione Data Lake Storage Gen2, in modo analogo all'uso della propria entità servizio. Consente a questa factory designata di accedere e copiare i dati da o verso la Data Lake Storage Gen2.

Per usare le identità gestite per l'autenticazione delle risorse di Azure, seguire questa procedura.

1. [Recuperare le informazioni sull'identità gestita di data factory](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore dell' **ID dell'applicazione di identità del servizio** generato insieme alla Factory.

2. Concedere all'identità gestita l'autorizzazione appropriata. Altre informazioni sul funzionamento dell'autorizzazione in Data Lake Storage Gen2 da [elenchi di controllo di accesso su file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Come origine**: In Storage Explorer, concedere almeno l'autorizzazione **Execute** a partire dall'file System di origine, insieme all'autorizzazione di **lettura** per i file da copiare. In alternativa, in controllo di accesso (IAM) concedere almeno il ruolo **lettore dati BLOB di archiviazione** .
    - **Come sink**: In Storage Explorer, concedere almeno l'autorizzazione **Execute** a partire dal sink file System, insieme all'autorizzazione di **scrittura** per la cartella sink. In alternativa, in controllo di accesso (IAM) concedere almeno il ruolo di **collaboratore dati BLOB di archiviazione** .

>[!NOTE]
>Per elencare le cartelle a partire dal livello di account o per testare la connessione, è necessario impostare l'autorizzazione dell'identità gestita da concedere all' **account di archiviazione con l'autorizzazione "lettore dati BLOB di archiviazione" in IAM**. Ciò vale quando si usano gli strumenti seguenti:
>- **Strumento copia dati** per creare una pipeline di copia.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione. 
>In caso di dubbi sulla concessione dell'autorizzazione a livello di account, durante la creazione, ignorare il test della connessione e immettere un percorso padre con l'autorizzazione concessa, quindi scegliere di selezionare il percorso specificato. L'attività di copia funziona a condizione che all'entità servizio venga concessa l'autorizzazione appropriata nei file da copiare.

>[!IMPORTANT]
>Se si usa la polibase per caricare i dati da Data Lake Storage Gen2 in SQL Data Warehouse, quando si usa l'autenticazione di identità gestita per Data Lake Storage Gen2, assicurarsi di seguire anche i passaggi 1 e 2 in [questa guida](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) a 1) registrare il server di database SQL con Azure Active Directory (Azure AD) e 2) assegnare il ruolo di collaboratore dati BLOB di archiviazione al server del database SQL. il resto viene gestito da Data Factory. Se la Data Lake Storage Gen2 è configurata con un endpoint di rete virtuale di Azure, per usare la polibase per il caricamento dei dati, è necessario usare l'autenticazione di identità gestita come richiesto da polibase.

Queste proprietà sono supportate per il servizio collegato:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con il modello di `https://<accountname>.dfs.core.windows.net`. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se l'archivio dati si trova in una rete privata, è possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [Datasets](concepts-datasets-linked-services.md).

- Per **parquet, delimitato testo, JSON, avro e formato binario**, vedere la sezione [parquet, delimitato testo, JSON, avro e formato binario set di dati](#format-based-dataset) .
- Per altri formati come il **formato ORC**, vedere la sezione [altro set di dati del formato](#other-format-dataset) .

### <a name="format-based-dataset"></a>Set di dati parquet, delimitato di testo, JSON, avro e Binary Format

Per copiare dati da e verso **parquet, testo delimitato, avro o formato binario**, vedere l'articolo formato [parquet](format-parquet.md), [formato testo delimitato](format-delimited-text.md), formato [avro](format-avro.md) e [formato binario](format-binary.md) su DataSet basato su formato e impostazioni supportate. Per data Lake storage Gen2 `location` in impostazioni nel set di dati basato su formato, sono supportate le proprietà seguenti:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà `location` Type in nel set di dati deve essere impostata su **AzureBlobFSLocation**. | Yes      |
| fileSystem | Nome del file system Data Lake Storage Gen2.                              | No       |
| folderPath | Percorso di una cartella nella file system specificata. Se si vuole usare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file nel file System + folderPath specificato. Se si vuole usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |

> [!NOTE]
> Il set di dati di tipo **AzureBlobFSFile** con parquet o il formato di testo indicato nella sezione seguente è ancora supportato come per l'attività di copia, ricerca o GetMetadata per la compatibilità con le versioni precedenti. Ma non funziona con la funzionalità del flusso di dati di mapping. Si consiglia di usare questo nuovo modello in futuro. L'interfaccia utente di creazione Data Factory genera questi nuovi tipi.

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

### <a name="other-format-dataset"></a>Set di dati di altri formati

Per copiare dati da e verso Data Lake Storage Gen2 nel **formato ORC**, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureBlobFSFile**. |Sì |
| folderPath | Percorso della cartella in Data Lake Storage Gen2. Se il valore non è specificato, punta alla radice. <br/><br/>Il filtro con caratteri jolly è supportato. I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e (corrisponde a `?` zero o a un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo contiene un carattere jolly o se il carattere di escape è interno. <br/><br/>Esempi: filesystem/Folder/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |No |
| fileName | Nome o filtro con caratteri jolly per i file sotto il "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per il filtro, i caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Utilizzare `^` per eseguire l'escape se il nome file effettivo dispone di un carattere jolly o se il carattere di escape è interno.<br/><br/>Se non si specifica fileName per un set di dati di output e non si specifica **preserveHierarchy** nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il criterio seguente: "*Dati. [GUID ID esecuzione attività]. [GUID se FlattenHierarchy]. [format se configurato]. [compressione se configurata]* ", ad esempio" Data. 0a405f8a-93ff-4C6F-B3BE-f69616f1df7a. txt. gz ". Se si copia da un'origine tabulare usando un nome di tabella anziché una query, il modello del nome è " *[nome tabella]. [ formato]. [compressione se configurata]* ", ad esempio" MyTable. csv ". |No |
| modifiedDatetimeStart | Filtro file basato sull'Ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di tempo `modifiedDatetimeStart` compreso `modifiedDatetimeEnd`tra e. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si desidera eseguire un filtro di file con grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati. Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.| No |
| modifiedDatetimeEnd | Filtro file basato sull'Ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di tempo `modifiedDatetimeStart` compreso `modifiedDatetimeEnd`tra e. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si desidera eseguire un filtro di file con grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati. Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni formato [testo](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato ORC](supported-file-formats-and-compression-codecs.md#orc-format)e [formato parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |No (solo per uno scenario di copia binaria) |
| compression | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un determinato nome, specificare **folderPath** con una parte di cartella e **filename** con un nome file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con una parte di cartella e **filename** con un filtro con caratteri jolly. 

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere la pagina relativa alle [configurazioni delle attività di copia](copy-activity-overview.md#configuration) e [alle pipeline e alle attività](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 come tipo di origine

- Per eseguire la copia da **parquet, testo delimitato, JSON, avro e formato binario**, vedere la sezione [parquet, delimitato testo, JSON, avro e formato binario](#format-based-source) .
- Per eseguire la copia da altri formati come il **formato ORC**, vedere la sezione [altra origine del formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, delimitato testo, JSON, avro e origine del formato binario

Per copiare dati da **parquet, testo delimitato, JSON, avro e formato binario**, vedere l'articolo formato [parquet](format-parquet.md), [formato testo delimitato](format-delimited-text.md), formato [avro](format-avro.md) e [formato binario](format-binary.md) in origine dell'attività di copia basata su formato e supportato Impostazioni. Le proprietà seguenti sono supportate per data Lake storage Gen2 `storeSettings` in impostazioni in origine copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà Type in `storeSettings` deve essere impostata su **AzureBlobFSReadSetting**. | Sì                                           |
| recursive                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando l'opzione ricorsiva è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No                                            |
| wildcardFolderPath       | Il percorso della cartella con caratteri jolly sotto la file system specificata configurata nel set di dati per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e (corrisponde a `?` zero o a un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo ha un carattere jolly o questo carattere di escape all'interno di. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| wildcardFileName         | Nome file con caratteri jolly sotto il file system + folderPath/wildcardFolderPath specificato per filtrare i file di origine. <br>I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e (corrisponde a `?` zero o a un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo ha un carattere jolly o questo carattere di escape all'interno di. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì se `fileName` non è specificato nel DataSet |
| modifiedDatetimeStart    | Filtro file basato sull'Ultima modifica dell'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di tempo `modifiedDatetimeStart` compreso `modifiedDatetimeEnd`tra e. L'ora viene applicata al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file. Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati. Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| maxConcurrentConnections | Numero di connessioni simultanee per la connessione all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No                                            |

> [!NOTE]
> Per il formato di testo parquet o delimitato, l'origine dell'attività di copia di tipo **AzureBlobFSSource** citata nella sezione seguente è ancora supportata come per la compatibilità con le versioni precedenti. Si consiglia di usare questo nuovo modello in futuro. L'interfaccia utente di creazione Data Factory genera questi nuovi tipi.

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

#### <a name="other-format-source"></a>Altra origine del formato

Per copiare dati da Data Lake Storage Gen2 in **formato ORC**, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **AzureBlobFSSource**. |Sì |
| recursive | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando l'opzione ricorsiva è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink.<br/>I valori consentiti sono **true** (predefinito) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

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

- Per la copia in **parquet, testo delimitato, JSON, avro e formato binario**, vedere la sezione [parquet, delimitato testo, JSON, avro e il sink di formato binario](#format-based-sink) .
- Per eseguire la copia in altri formati come il **formato ORC/JSON**, vedere la sezione [altro sink di formato](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, testo delimitato, JSON, avro e sink di formato binario

Per copiare dati in **parquet, testo delimitato, JSON, avro e formato binario**, vedere l'articolo formato [parquet](format-parquet.md), [formato testo delimitato](format-delimited-text.md), formato [avro](format-avro.md) e [formato binario](format-binary.md) in sink dell'attività di copia basata su formato e supportato Impostazioni. Le proprietà seguenti sono supportate per data Lake storage Gen2 `storeSettings` in impostazioni in sink di copia basato sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà Type in `storeSettings` deve essere impostata su **AzureBlobFSWriteSetting**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| maxConcurrentConnections | Numero di connessioni per la connessione all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No       |

> [!NOTE]
> Per il formato di testo parquet o delimitato, il sink dell'attività di copia di tipo **AzureBlobFSSink** indicato nella sezione seguente è ancora supportato come per la compatibilità con le versioni precedenti. Si consiglia di usare questo nuovo modello in futuro. L'interfaccia utente di creazione Data Factory genera questi nuovi tipi.

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

#### <a name="other-format-sink"></a>Altro sink di formato

Per copiare i dati in Data Lake Storage Gen2 nel **formato ORC**, nella sezione **sink** sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **AzureBlobFSSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No |
| maxConcurrentConnections | Numero di connessioni per la connessione all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

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

| folderPath | fileName | recursive | Struttura delle cartelle di origine e risultato del filtro (i file in **grassetto** sono stati recuperati)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vuoto, USA predefinito) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vuoto, USA predefinito) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alcuni esempi dei valori recursive e copyBehavior

In questa sezione viene descritto il comportamento risultante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| recursive | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Sottocartella1 con file3, file4 e File5 non viene prelevato. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>Sottocartella1 con file3, file4 e File5 non viene prelevato. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome di file generato automaticamente. Nome generato automaticamente per File1<br/><br/>Sottocartella1 con file3, file4 e File5 non viene prelevato. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Mantieni ACL da Data Lake Storage Gen1

>[!TIP]
>Per copiare dati da Azure Data Lake Storage Gen1 in Gen2 in generale, vedere [copiare dati da Azure Data Lake storage Gen1 a Gen2 con Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) per una procedura dettagliata e procedure consigliate.

Quando si copiano i file da Azure Data Lake Storage Gen1 a Gen2, è possibile scegliere di mantenere gli elenchi di controllo di accesso (ACL) POSIX insieme ai dati. Per altre informazioni sul controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-access-control.md) e [controllo di accesso in Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

I tipi di ACL seguenti possono essere conservati usando l'attività di copia Azure Data Factory. È possibile selezionare uno o più tipi:

- **ACL**: Copiare e mantenere gli elenchi di controllo di accesso POSIX su file e directory. Copia gli ACL completi esistenti dall'origine al sink. 
- **Proprietario**: Copiare e mantenere l'utente proprietario dei file e delle directory. È necessario l'accesso con privilegi avanzati ai Data Lake Storage Gen2 sink.
- **Gruppo**: Copiare e mantenere il gruppo proprietario di file e directory. È necessario l'accesso con privilegi avanzati a sink Data Lake Storage Gen2 o all'utente proprietario (se l'utente proprietario è anche membro del gruppo di destinazione).

Se si specifica di copiare da una cartella, Data Factory replica gli ACL per la cartella specificata e i file e le directory al suo interno, `recursive` se è impostato su true. Se si specifica di copiare da un singolo file, vengono copiati gli ACL in tale file.

>[!IMPORTANT]
>Quando si sceglie di mantenere gli ACL, assicurarsi di concedere autorizzazioni sufficientemente elevate affinché Data Factory funzionino con l'account di Data Lake Storage Gen2 del sink. Ad esempio, usare l'autenticazione con chiave dell'account o assegnare il ruolo di proprietario dei dati BLOB di archiviazione all'entità servizio o all'identità gestita.

Quando si configura l'origine come Data Lake Storage Gen1 con l'opzione di copia binaria o il formato binario e il sink come Data Lake Storage Gen2 con l'opzione di copia binaria o il formato binario, è possibile trovare l'opzione **Mantieni** nella pagina **delle impostazioni dello strumento copia dati** o in scheda**Impostazioni** **attività** > di copia per la creazione di attività.

![Data Lake Storage Gen1 a Gen2 Mantieni ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Di seguito è riportato un esempio di configurazione JSON `preserve`(vedere): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Ulteriori informazioni sulla trasformazione [origine](data-flow-source.md) e sulla [trasformazione sink](data-flow-sink.md) nella funzionalità flusso di dati di mapping.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
