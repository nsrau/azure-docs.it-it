---
title: Copiare dati da e in Azure Data Lake Storage Gen1 usando Azure Data Factory | Microsoft Docs
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
ms.date: 11/05/2018
ms.author: jingwang
ms.openlocfilehash: 411223c2ef7b1400f2019ebc6704b3c4a5d41235
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53387030"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Copiare dati da e in Azure Data Lake Storage di 1° generazione usando Azure Data Factory
> [!div class="op_single_selector" title1="Seleziona la versione del servizio Data Factory che stai utilizzando:"]
> * [Versione 1](v1/data-factory-azure-datalake-connector.md)
> * [Versione corrente](connector-azure-data-lake-store.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure Data Lake Storage Gen1 (noto precedentemente come Azure Data Lake Store). L'articolo si basa sulla [panoramica dell'attività di copia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da qualsiasi archivio dati di origine supportato in Azure Data Lake Store o da Azure Data Lake Store in qualsiasi archivio dati di sink supportato. Vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Azure Data Lake Store supporta:

- Copia di file tramite uno dei seguenti metodi di autenticazione: **entità servizio** o **identità gestite per le risorse di Azure**.
- Copia di file così come sono o analisi/generazione di file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Se si copiano dati tramite il runtime di integrazione self-hosted, configurare il firewall aziendale in modo da consentire il traffico in uscita verso `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` sulla porta 443. Quest'ultimo è il servizio token di sicurezza di Azure con cui deve comunicare il runtime di integrazione per ottenere il token di accesso.

## <a name="get-started"></a>Attività iniziali

> [!TIP]
> Per una procedura dettagliata sull'uso del connettore Azure Data Lake Store, vedere [Caricare i dati in Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Azure Data Lake Store.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato ad Azure Data Lake Store sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà `type` deve essere impostata su **AzureDataLakeStore**. | Yes |
| dataLakeStoreUri | Informazioni sull'account Azure Data Lake Store. Queste informazioni accettano uno dei seguenti formati: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Yes |
| subscriptionId | ID sottoscrizione di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| resourceGroupName | Nome del gruppo di risorse di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, usa il tipo di runtime di integrazione di Azure predefinito. |No  |

### <a name="use-service-principal-authentication"></a>Usare l'autenticazione basata su entità servizio

Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory e concedere all'entità l'accesso a Data Lake Store. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

- ID applicazione
- Chiave applicazione
- ID tenant

>[!IMPORTANT]
> Assicurarsi di concedere all'entità servizio un'autorizzazione appropriata in Data Lake Store:
>- **Come origine**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Lettura + Esecuzione** per elencare e copiare i file in cartelle e sottocartelle. In alternativa, è possibile concedere l'autorizzazione **lettura** per copiare un singolo file. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non è presente alcun requisito per il controllo di accesso a livello di account (IAM).
>- **Come sink**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Scrittura + Esecuzione** per creare elementi figlio nella cartella. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa il runtime di integrazione di Azure per copiare (sia l'origine che il sink si trovano nel cloud), in IAM concedere almeno il ruolo **Lettore** per consentire a Data Factory di rilevare l'area di Data Lake Store. Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store. Effettuare l'associazione nel servizio collegato di Data Lake Store come nell'esempio seguente.

>[!NOTE]
>Per elencare le cartelle a partire dalla radice, è necessario impostare l'autorizzazione dell'entità servizio concessa sul **livello di radice con autorizzazione "Esecuzione"**. Ciò vale quando si usano gli strumenti seguenti:
>- **Strumento Copia dati** per creare la pipeline di copia.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione.

Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Yes |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come `SecureString` per archiviarlo in modo sicuro in Data Factory oppure [riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Yes |

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

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità del servizio per l'autenticazione con Data Lake Store, analogamente a come si usa l'entità servizio. Consente alla factory designata di accedere e copiare i dati da e verso Data Lake Store.

Per usare l'autenticazione basata sulle identità gestite per le risorse di Azure:

1. [Recuperare l'identità del servizio Data Factory](data-factory-service-identity.md#retrieve-service-identity) copiando il valore di "Service Identity Application ID" generato con la factory.
2. Concedere all'identità del servizio l'accesso a Data Lake Store usando la stessa procedura valida per l'entità servizio attenendosi a queste note.

>[!IMPORTANT]
> Assicurarsi di concedere all'identità del servizio Data factory un'autorizzazione appropriata in Azure Data Lake Store:
>- **Come origine**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Lettura + Esecuzione** per elencare e copiare i file in cartelle e sottocartelle. In alternativa, è possibile concedere l'autorizzazione **lettura** per copiare un singolo file. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Non è presente alcun requisito per il controllo di accesso a livello di account (IAM).
>- **Come sink**: In **Esplora dati** > **Accesso** concedere almeno l'autorizzazione **Scrittura + Esecuzione** per creare elementi figlio nella cartella. È possibile scegliere di aggiungere a **Questa cartella e tutti gli elementi figlio** come ricorsiva, e aggiungere come **una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Se si usa il runtime di integrazione di Azure per copiare (sia l'origine che il sink si trovano nel cloud), in IAM concedere almeno il ruolo **Lettore** per consentire a Data Factory di rilevare l'area di Data Lake Store. Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store. Effettuare l'associazione nel servizio collegato di Data Lake Store come nell'esempio seguente.

>[!NOTE]
>Per elencare le cartelle a partire dalla radice, è necessario impostare l'autorizzazione dell'entità servizio concessa sul **livello di radice con autorizzazione "Esecuzione"**. Ciò vale quando si usano gli strumenti seguenti:
>- **Strumento Copia dati** per creare la pipeline di copia.
>- **Interfaccia utente di Data Factory** per testare la connessione e passare alle cartelle durante la creazione.

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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per copiare dati da e verso Azure Data Lake Store, impostare la proprietà`type` del set di dati su **AzureDataLakeStoreFile**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **AzureDataLakeStoreFile** |Yes |
| folderPath | Percorso della cartella in Data Lake Store. Il filtro con caratteri jolly non è supportato. Se il valore non è specificato, punta alla radice. Esempio: rootfolder/subfolder/ |No  |
| fileName | **Filtro con nome o carattere jolly** per i file nell'elemento "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per un filtro, i caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per il carattere escape se il nome effettivo del file include caratteri jolly o escape.<br/><br/>Se non si specifica fileName per un set di dati di output e non si specifica **preserveHierarchy** nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il criterio seguente: "*Data.[GUID ID esecuzione attività].[GUID se FlattenHierarchy].[formato se configurato].[compressione se configurata]*". Un esempio è "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se si copia da un'origine tabulare usando il nome tabella anziché la query, il criterio del nome è "*[nome tabella].[formato].[compressione se configurata]*", ad esempio "MyTable.csv". |No  |
| format | Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si desidera analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](supported-file-formats-and-compression-codecs.md#text-format), [JsonFormat](supported-file-formats-and-compression-codecs.md#json-format), [AvroFormat](supported-file-formats-and-compression-codecs.md#avro-format), [OrcFormat](supported-file-formats-and-compression-codecs.md#orc-format) e [ParquetFormat](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono: **Optimal** (Ottimale) e **Fastest** (Più veloce). |No  |


>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un determinato nome, specificare **folderPath** con il percorso della cartella e **fileName** con il nome del file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con il percorso della cartella e **fileName** con il filtro con caratteri jolly. 

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
            "fileName": "myfile.csv.gz",
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

Per copiare dati da Azure Data Lake Store, impostare il tipo di origine nell'attività di copia su **AzureDataLakeStoreSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà `type` dell'origine dell'attività di copia deve essere impostata su: **AzureDataLakeStoreSource**. |Yes |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando `recursive` è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No  |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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

Per copiare dati in Data Lake Store, impostare il tipo di sink nell'attività di copia su **AzureDataLakeStoreSink**. Nella sezione **sink** sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà `type` del sink dell'attività di copia deve essere impostata su: **AzureDataLakeStoreSink**. |Yes |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (predefinito)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome del file o del BLOB, il nome del file unito sarà il nome specificato. In caso contrario, il nome del file viene generato automaticamente. | No  |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>Esempi di comportamento dell'operazione di copia

Questa sezione descrive il comportamento risultante dell'operazione di copia per diverse combinazioni di valori `recursive` e `copyBehavior`.

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5. |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;I contenuti di File1 + File2 + File3 + File4 + File 5 vengono uniti in un unico file con nome generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome file generato automaticamente. Nome generato automaticamente per File1<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
