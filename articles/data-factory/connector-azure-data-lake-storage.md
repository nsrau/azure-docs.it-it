---
title: Copiare dati da o verso Azure Data Lake Storage Gen2 con Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da e verso Azure Data Lake Storage Gen2 usando Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 6425fdfe89ca2f4c47aaf0e5ffd1dac7767b5020
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057942"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiare dati da e in Azure Data Lake Storage Gen2 tramite Azure Data Factory

Gen2 di archiviazione di Azure Data Lake è un set di funzionalità dedicato a analitica dei big data integrate [archiviazione Blob di Azure](../storage/blobs/storage-blobs-introduction.md). È possibile usarlo per interfacciarsi con i dati utilizzando sia paradigmi di archiviazione file system e l'oggetto.

Questo articolo illustra come copiare dati da e verso Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore Azure Data Lake Storage Gen2 è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [supportata matrice di origine o sink](copy-activity-overview.md)
- [Mapping di flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

In particolare, questo connettore è possibile:

- Copiare i dati con una chiave dell'account, entità servizio o identità gestite per le autenticazioni di risorse di Azure.
- Copiare i file così come sono o analizzare o generare file con [formati di file e i codec di compressione supportati](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Se si abilita lo spazio dei nomi gerarchico, attualmente non impostare alcuna interoperabilità delle operazioni tra Blob e archiviazione Gen2 API di Data Lake. Se si raggiunge l'errore "ErrorCode = FilesystemNotFound" con il messaggio "il file System specificato non esiste", è causato dal file system sink specificato che è stato creato in un' posizione tramite l'API di Blob invece di archiviazione Gen2 API di Data Lake. Per risolvere il problema, specificare un nuovo file system con un nome che non esiste come nome di un contenitore Blob. Quindi Data Factory crea automaticamente questo file system durante la copia dei dati.

>[!NOTE]
>Se si abilita il **Consenti attendibili i servizi Microsoft per accedere a questo account di archiviazione** opzione nelle impostazioni firewall di archiviazione di Azure, il runtime di integrazione di Azure non può connettersi a Data Lake Storage Gen2 e Mostra un errore non consentito. Poiché Data Factory non è trattato come un servizio Microsoft attendibile, viene visualizzato il messaggio di errore. Usare il runtime di integrazione self-hosted per connettersi invece.

## <a name="get-started"></a>Attività iniziali

>[!TIP]
>Per una procedura dettagliata su come usare il connettore Data Lake Storage Gen2, vedere [caricare i dati in Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti forniscono informazioni sulle proprietà che vengono usate per definire entità di Data Factory specifiche di Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il connettore Azure Data Lake Storage Gen2 supporta i seguenti tipi di autenticazione. Vedere le sezioni corrispondenti per informazioni dettagliate:

- [Autenticazione basata sulla chiave dell'account](#account-key-authentication)
- [Autenticazione di un'entità servizio](#service-principal-authentication)
- [Autenticazione di identità gestite per le risorse di Azure](#managed-identity)

### <a name="account-key-authentication"></a>Autenticazione basata sulla chiave dell'account

Per usare l'autenticazione basata sulla chiave dell'account di archiviazione, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con lo schema delle `https://<accountname>.dfs.core.windows.net`. | Yes |
| accountKey | Chiave dell'account di Data Lake Storage Gen2. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati è in una rete privata. Se questa proprietà non è specificata, viene utilizzato il runtime di integrazione di Azure predefinito. |No |

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

Per usare l'autenticazione dell'entità servizio, seguire questa procedura.

1. Registrare un'entità applicazione in Azure Active Directory (Azure AD) seguendo i passaggi descritti in [registrare l'applicazione con un tenant di Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere l'autorizzazione appropriata dell'entità servizio.

    - **Come origine**: In Azure Storage Explorer, concedere almeno **lettura ed esecuzione** dell'autorizzazione per elencare e copiare i file nelle cartelle e sottocartelle. In alternativa, è possibile concedere l'autorizzazione **lettura** per copiare un singolo file. In alternativa, nel controllo di accesso (IAM), concedere almeno il **lettore di dati Blob di archiviazione** ruolo.
    - **Come sink**: In Storage Explorer, concedere almeno **scrittura + esecuzione** dell'autorizzazione per creare elementi figlio nella cartella. In alternativa, nel controllo di accesso (IAM), concedere almeno il **collaboratore ai dati Blob di archiviazione** ruolo.

>[!NOTE]
>Elenco cartelle a partire dal livello di account o al test della connessione, è necessario impostare l'autorizzazione dell'entità servizio viene concesso al **account di archiviazione con l'autorizzazione "Lettore di dati Blob di archiviazione" nella pagina IAM**. Ciò vale quando si usano gli strumenti seguenti:
>- **Lo strumento Copia dati** alla pipeline di copia dell'autore.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione. 
>Se si hanno dubbi sulla concessione dell'autorizzazione a livello di account, è possibile ignorare test della connessione e il percorso di input manualmente durante la creazione. Attività di copia continui a funzionare fino a quando l'entità servizio viene concesso con l'autorizzazione appropriata i file da copiare.

Queste proprietà sono supportate per il servizio collegato:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con lo schema delle `https://<accountname>.dfs.core.windows.net`. | Yes |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come un `SecureString` per archiviarlo in modo sicuro in Data Factory. In alternativa, è possibile [fanno riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarlo passando il mouse nell'angolo superiore destro del portale di Azure. | Yes |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati è in una rete privata. Se non specificato, viene utilizzato il runtime di integrazione di Azure predefinito. |No |

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

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione di Data Lake Storage Gen2, simile all'uso dell'entità del servizio. Consente questa factory designata di accedere e copiare i dati da o verso il Gen2 di archiviazione Data Lake.

Per usare le identità gestito per l'autenticazione di risorse di Azure, seguire questa procedura.

1. [Recuperare le informazioni sull'identità gestita di Data Factory](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore della **ID applicazione identità del servizio** generato con la factory.

2. Concedere un'autorizzazione appropriata l'identità gestita.

    - **Come origine**: In Storage Explorer, concedere almeno **lettura ed esecuzione** dell'autorizzazione per elencare e copiare i file nelle cartelle e sottocartelle. In alternativa, è possibile concedere l'autorizzazione **lettura** per copiare un singolo file. In alternativa, nel controllo di accesso (IAM), concedere almeno il **lettore di dati Blob di archiviazione** ruolo.
    - **Come sink**: In Storage Explorer, concedere almeno **scrittura + esecuzione** dell'autorizzazione per creare elementi figlio nella cartella. In alternativa, nel controllo di accesso (IAM), concedere almeno il **collaboratore ai dati Blob di archiviazione** ruolo.

>[!NOTE]
>Elenco cartelle a partire dal livello di account o al test della connessione, è necessario impostare l'autorizzazione di identità gestite concessa al **account di archiviazione con l'autorizzazione "Lettore di dati Blob di archiviazione" nella pagina IAM**. Ciò vale quando si usano gli strumenti seguenti:
>- **Lo strumento Copia dati** alla pipeline di copia dell'autore.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione. 
>Se si hanno dubbi sulla concessione dell'autorizzazione a livello di account, è possibile ignorare test della connessione e il percorso di input manualmente durante la creazione. Attività di copia continui a funzionare, purché l'identità gestita viene concesso con l'autorizzazione appropriata i file da copiare.

>[!IMPORTANT]
>Se si usa PolyBase per caricare dati da Data Lake Storage Gen2 in SQL Data Warehouse, quando si usa l'autenticazione di Data Lake Storage Gen2 identità gestita, verificare che è anche seguire i passaggi 1 e 2 nella [questo materiale sussidiario](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Seguire le istruzioni per registrare il server di Database SQL con Azure Active Directory (Azure AD). È inoltre possibile assegnare il ruolo di collaboratore ai dati di archiviazione Blob con controllo degli accessi basata sui ruoli al server di Database SQL. Il resto viene gestito da Data Factory. Se il Gen2 di archiviazione Data Lake è configurato con un endpoint di rete virtuale di Azure per usare PolyBase per caricare i dati da quest'ultimo, è necessario usare l'autenticazione identità gestita.

Queste proprietà sono supportate per il servizio collegato:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureBlobFS**. |Sì |
| url | Endpoint per Data Lake Storage Gen2 con lo schema delle `https://<accountname>.dfs.core.windows.net`. | Yes |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati è in una rete privata. Se non specificato, viene utilizzato il runtime di integrazione di Azure predefinito. |No |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere [set di dati](concepts-datasets-linked-services.md).

- Per il formato di testo delimitato e parquet, vedere la [set di dati di formato Parquet e testo delimitato](#parquet-and-delimited-text-format-dataset) sezione.
- Per altri formati, ad esempio ORC, Avro, JSON o formato binario, vedere la [altri set di dati di formato](#other-format-dataset) sezione.

### <a name="parquet-and-delimited-text-format-dataset"></a>Set di dati di formato parquet e testo delimitato

Per copiare dati da e verso Data Lake Storage Gen2 in formato testo delimitato o parquet, vedere la [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articoli nel set di dati in base al formato e le impostazioni supportate. Sono supportate le proprietà seguenti per Data Lake Storage Gen2 sotto `location` le impostazioni del set di dati in base al formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà del tipo sotto `location` nel set di dati deve essere impostata su **AzureBlobFSLocation**. | Yes      |
| fileSystem | Nome del sistema del file Data Lake Storage Gen2.                              | No       |
| folderPath | Il percorso a una cartella nel file system specificato. Se si desidera usare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni di origine di attività. | No       |
| fileName   | Il nome del file sotto il file System specificato + folderPath. Se si desidera usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni di origine di attività. | No       |

> [!NOTE]
> Il **AzureBlobFSFile** tipo set di dati con formato parquet o testo indicato nella sezione seguente è ancora supportata perché è per copia, la ricerca o attività GetMetadata per garantire la compatibilità con le versioni precedenti. Ma non funziona con la funzionalità di flusso di dati di mapping. È consigliabile usare questo nuovo modello in futuro. La creazione dell'interfaccia utente di Data Factory genera questi nuovi tipi.

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

### <a name="other-format-dataset"></a>Altri set di dati di formato

Per copiare dati da e verso Data Lake Storage Gen2 in formato binario, JSON, Avro o ORC, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureBlobFSFile**. |Sì |
| folderPath | Percorso della cartella in Data Lake Storage Gen2. Se il valore non è specificato, punta alla radice. <br/><br/>Filtro con caratteri jolly è supportata. Consentiti sono i caratteri jolly `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o un singolo carattere). Usare `^` di escape se il nome effettivo della cartella contiene un carattere jolly o il carattere di escape si trova all'interno. <br/><br/>Esempi: file System o alla cartella /. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |No |
| fileName | Filtro nome o un carattere jolly per i file sotto la proprietà "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per il filtro, i caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o un singolo carattere).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` di escape se il nome del file effettivo include un carattere jolly o il carattere di escape si trova all'interno.<br/><br/>Se non si specifica fileName per un set di dati di output e non si specifica **preserveHierarchy** nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il criterio seguente: "*Dei dati. [GUID dell'ID esecuzione attività]. [GUID se FlattenHierarchy]. [format se configurata]. [la compressione se configurata]* ", ad esempio,"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se si copia da un'origine tabulare usando un nome di tabella anziché una query, è il modello di nome " *[nome della tabella]. [ Format]. [la compressione se configurata]* ", ad esempio,"MyTable.csv". |No |
| modifiedDatetimeStart | Filtro di file basato sull'attributo Data ultima modifica. I file vengono selezionati se loro ora ultima modifica è compreso nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. Viene applicata l'ora al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive di spostamento dei dati dipende dai si abilita questa impostazione quando si desidera file filtro con elevate quantità di file. <br/><br/> La proprietà può essere NULL, il che significa che viene applicato alcun filtro di attributi di file per il set di dati. Quando `modifiedDatetimeStart` ha un valore data/ora ma `modifiedDatetimeEnd` è NULL, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore di data/ora vengono selezionate. Quando `modifiedDatetimeEnd` ha un valore data/ora ma `modifiedDatetimeStart` è NULL, significa che i file il cui ultimo attributo modificato è minore del valore data/ora vengono selezionati.| No |
| modifiedDatetimeEnd | Filtro di file basato sull'attributo Data ultima modifica. I file vengono selezionati se loro ora ultima modifica è compreso nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. Viene applicata l'ora al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br/><br/> Le prestazioni complessive di spostamento dei dati dipende dai si abilita questa impostazione quando si desidera file filtro con elevate quantità di file. <br/><br/> La proprietà può essere NULL, il che significa che viene applicato alcun filtro di attributi di file per il set di dati. Quando `modifiedDatetimeStart` ha un valore data/ora ma `modifiedDatetimeEnd` è NULL, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore di data/ora vengono selezionate. Quando `modifiedDatetimeEnd` ha un valore data/ora ma `modifiedDatetimeStart` è NULL, significa che i file il cui ultimo attributo modificato è minore del valore data/ora vengono selezionati.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere la [formato di testo](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato ORC](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) sezioni. |No (solo per uno scenario di copia binaria) |
| compression | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un nome specifico, specificare **folderPath** con una parte della cartella e **fileName** con un nome file.<br>Per copiare un sottoinsieme di file in una cartella, specificare **folderPath** con una parte della cartella e **fileName** con un filtro con caratteri jolly. 

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di attività, vedere [configurazioni di attività di copia](copy-activity-overview.md#configuration) e [pipeline e attività](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 come tipo di origine

- Per copiare dal parquet o formato di testo delimitato, vedere la [Parquet e testo delimitato formato origine](#parquet-and-delimited-text-format-source) sezione.
- Per copiare da altri formati, ad esempio ORC, Avro, JSON o formato binario, vedere la [altra origine formato](#other-format-source) sezione.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e testo delimitato formato origine

Per copiare dati da Data Lake Storage Gen2 in formato testo delimitato o parquet, vedere la [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articolo sull'origine dell'attività copy in base al formato e le impostazioni supportate. Sono supportate le proprietà seguenti per Data Lake Storage Gen2 sotto `storeSettings` le impostazioni di origine della copia in base al formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà del tipo sotto `storeSettings` deve essere impostata su **AzureBlobFSReadSetting**. | Sì                                           |
| recursive                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando recursive è impostata su true e il sink è un archivio basato su file, una cartella vuota o una sottocartella non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No                                            |
| wildcardFolderPath       | Il percorso della cartella con caratteri jolly nel file system specificato configurato nel set di dati nelle cartelle di origine di filtro. <br>Consentiti sono i caratteri jolly `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o un singolo carattere). Usare `^` di escape se il nome effettivo della cartella contiene un carattere jolly o questo carattere di escape all'interno. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| wildcardFileName         | Il nome di file con caratteri jolly con il file system specificato + folderPath/wildcardFolderPath per filtrare i file di origine. <br>Consentiti sono i caratteri jolly `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o un singolo carattere). Usare `^` di escape se il nome effettivo della cartella contiene un carattere jolly o questo carattere di escape all'interno. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì se `fileName` non è specificato nel set di dati |
| modifiedDatetimeStart    | Filtro di file basato sull'attributo Data ultima modifica. I file vengono selezionati se loro ora ultima modifica è compreso nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. Viene applicata l'ora al fuso orario UTC nel formato "2018-12-01T05:00:00Z". <br> La proprietà può essere NULL, il che significa che non verrà applicato alcun filtro di attributi di file per il set di dati. Quando `modifiedDatetimeStart` ha un valore data/ora ma `modifiedDatetimeEnd` è NULL, significa che i file la cui ultima modifica attributo è maggiore o uguale al valore di data/ora vengono selezionate. Quando `modifiedDatetimeEnd` ha un valore data/ora ma `modifiedDatetimeStart` è NULL, significa che i file il cui ultimo attributo modificato è minore del valore data/ora siano selezionati. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| maxConcurrentConnections | Il numero di connessioni per connettersi all'archivio di archiviazione simultaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No                                            |

> [!NOTE]
> Per il parquet o il formato di testo delimitato, il **AzureBlobFSSource** origine dell'attività copy tipo indicato nella sezione seguente è ancora supportata perché è per la compatibilità con le versioni precedenti. È consigliabile usare questo nuovo modello in futuro. La creazione dell'interfaccia utente di Data Factory genera questi nuovi tipi.

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

Per copiare dati da Data Lake Storage Gen2 in formato binario, JSON, Avro o ORC, le proprietà seguenti sono supportate nell'attività di copia **origine** sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **AzureBlobFSSource**. |Sì |
| recursive | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando recursive è impostata su true e il sink è un archivio basato su file, una cartella vuota o una sottocartella non viene copiata o creata nel sink.<br/>I valori consentiti sono **true** (predefinito) e **false**. | No |
| maxConcurrentConnections | Il numero di connessioni per connettersi all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No |

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

- Per copiare il parquet o il formato di testo delimitato, vedere la [Parquet e testo delimitato formato sink](#parquet-and-delimited-text-format-sink) sezione.
- Per copiare in altri formati, ad esempio ORC, Avro, JSON o formato binario, vedere la [altri sink formato](#other-format-sink) sezione.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet e il sink di formato di testo delimitato

Per copiare dati in Data Lake Storage Gen2 in formato testo delimitato o parquet, vedere la [formato Parquet](format-parquet.md) e [formato di testo delimitato](format-delimited-text.md) articoli nel sink dell'attività Copia in base al formato e le impostazioni supportate. Sono supportate le proprietà seguenti per Data Lake Storage Gen2 sotto `storeSettings` impostazioni nel sink di copia in base al formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà del tipo sotto `storeSettings` deve essere impostata su **AzureBlobFSWriteSetting**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| maxConcurrentConnections | Il numero di connessioni per connettersi all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No       |

> [!NOTE]
> Per il parquet o il formato di testo delimitato, il **AzureBlobFSSink** sink dell'attività Copia tipo indicato nella sezione seguente è ancora supportato in quanto sono per la compatibilità con le versioni precedenti. È consigliabile usare questo nuovo modello in futuro. La creazione dell'interfaccia utente di Data Factory genera questi nuovi tipi.

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

Per copiare dati in Data Lake Storage Gen2 in formato binario, JSON, Avro o ORC, le proprietà seguenti sono supportate nel **sink** sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **AzureBlobFSSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (impostazione predefinita)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se si specifica il nome di file, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No |
| maxConcurrentConnections | Il numero di connessioni per connettersi all'archivio dati contemporaneamente. Specificare solo quando si desidera limitare le connessioni simultanee all'archivio dati. | No |

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
| `Folder*` | (Vuota, utilizzare l'impostazione predefinita) | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vuota, utilizzare l'impostazione predefinita) | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alcuni esempi dei valori recursive e copyBehavior

In questa sezione descrive il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| recursive | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La Sottocartella1 con File3, File4 e File5 non viene recuperato. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>La Sottocartella1 con File3, File4 e File5 non viene recuperato. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome di file generato automaticamente. Nome generato automaticamente per File1<br/><br/>La Sottocartella1 con File3, File4 e File5 non viene recuperato. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Mantenere gli ACL da Data Lake Store Gen1

>[!TIP]
>Per copiarne i dati da Azure Data Lake archiviazione Gen1 Gen2 in generale, vedere [copiare dati da Azure Data Lake archiviazione Gen1 a Gen2 con Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) per una procedura dettagliata e procedure consigliate.

Quando si copiano i file da Azure Data Lake archiviazione Gen1 a Gen2, è possibile scegliere di mantenere gli elenchi di controllo di accesso (ACL) POSIX insieme ai dati. Per altre informazioni sul controllo degli accessi, vedere [controllo degli accessi in Azure Data Lake archiviazione Gen1](../data-lake-store/data-lake-store-access-control.md) e [controllo degli accessi in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

I seguenti tipi di ACL possono essere conservati, usando l'attività di copia di Azure Data Factory. È possibile selezionare uno o più tipi:

- **ACL**: Copiare e mantenere elenchi di controllo di accesso POSIX su file e directory. Copia gli ACL esistenti completi dall'origine al sink. 
- **Proprietario**: Copiare e mantenere l'utente proprietario di file e directory. È necessario l'accesso utente con privilegi avanzati al sink Data Lake Storage Gen2.
- **Gruppo**: Copiare e mantenere il gruppo proprietario di file e directory. È necessario l'accesso utente con privilegi avanzati a sink Data Lake Storage Gen2 o l'utente proprietario (se l'utente proprietario sia anche membro del gruppo di destinazione).

Se si sceglie di copiare da una cartella, Data Factory esegue la replica gli ACL per la cartella specificata e il file e directory, se `recursive` è impostato su true. Se si sceglie di copiare da un singolo file, vengono copiati gli ACL di tale file.

>[!IMPORTANT]
>Quando si sceglie di conservare gli ACL, assicurarsi di che concedere alto delle autorizzazioni sufficienti per Data Factory agire su sink account Data Lake Storage Gen2. Ad esempio, usare l'autenticazione con chiave account o assegnare il ruolo di proprietario dei dati di archiviazione Blob per l'identità del servizio gestito o dell'entità.

Quando si Configura origine come Data Lake archiviazione Gen1 con l'opzione copia binaria o formato binario e il sink come Data Lake Storage Gen2 con l'opzione copia binaria o di un formato binario, è possibile trovare il **Preserve** opzione di **copia Le impostazioni degli strumenti dati** pagina o scegliere il **attività di copia** > **impostazioni** scheda per la creazione di attività.

![Data Lake Store Gen1 su Preserve Gen2 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Ecco un esempio di configurazione JSON (vedere `preserve`): 

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

Altre informazioni sulle [trasformazione sorgente](data-flow-source.md) e [sink trasformazione](data-flow-sink.md) nella funzionalità di flusso di dati di mapping.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
