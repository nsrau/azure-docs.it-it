---
title: Copiare dati da/in Azure Data Lake Store usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi dati di origine supportati ad Azure Data Lake Store o da Data Lake Store ad archivi di sink supportati usando Data Factory.
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
ms.openlocfilehash: 69707931402de597c9d6a329da349723da2a782a
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="copy-data-to-or-from-azure-data-lake-store-by-using-azure-data-factory"></a>Copiare dati da e in Azure Data Lake Store usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-azure-datalake-connector.md)
> * [Versione 2 - Anteprima](connector-azure-data-lake-store.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure Data Lake Store. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore Azure Data Lake Store nella versione 1](v1/data-factory-azure-datalake-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da qualsiasi archivio dati di origine supportato in Azure Data Lake Store o da Azure Data Lake Store in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Azure Data Lake Store supporta:

- Copia di file mediante l'autenticazione basata su **entità servizio** o **identità del servizio gestito (MSI)**.
- La copia di file così come sono o l'analisi/generazione di file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Azure Data Lake Store.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Azure Data Lake Store sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureDataLakeStore**. | Sì |
| dataLakeStoreUri | Informazioni sull'account Azure Data Lake Store. Queste informazioni accettano uno dei seguenti formati: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |
| subscriptionId | ID sottoscrizione di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| resourceGroupName | Nome del gruppo di risorse di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

Fare riferimento alle sezioni seguenti per altre proprietà e altri esempi JSON per tipi di autenticazione diversi:

- [Uso dell'autenticazione basata sull'entità servizio](#using-service-principal-authentication)
- [Uso dell'autenticazione basata sull'identità del servizio gestito](#using-managed-service-identity-authentication)

### <a name="using-service-principal-authentication"></a>Uso dell'autenticazione basata sull'entità servizio

Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory (Azure AD) e concedere all'entità l'accesso a Data Lake Store. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

- ID applicazione
- Chiave applicazione
- ID tenant

>[!TIP]
> Assicurarsi di concedere all'entità servizio un'autorizzazione appropriata in Azure Data Lake Store:
>- Come origine, concedere almeno l'autorizzazione di accesso ai dati **Lettura ed esecuzione** per l'elenco e la copia del contenuto di una cartella oppure l'autorizzazione **Lettura** per la copia di un file singolo. Nessun requisito per il controllo di accesso a livello di account (IAM).
>- Come sink, concedere almeno l'autorizzazione di accesso ai dati **Scrittura ad esecuzione** per creare elementi figlio nella cartella. E se si esegue la copia tramite runtime di integrazione di Azure (sia origine che sink sono nel cloud) per permettere a Data Factory di rilevare l'area di Data Lake Store, concedere almeno il ruolo **Lettore** nel controllo di accesso dell'account (IAM). Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store ed eseguire l'associazione nel servizio collegato di Data Lake Store, come nell'esempio seguente:

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString. | Sì |

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

### <a name="using-managed-service-identity-authentication"></a>Uso dell'autenticazione basata sull'identità del servizio gestito

Una data factory può essere associata a un'[identità del servizio gestito](data-factory-service-identity.md), che rappresenta la data factory specificata. È possibile usare direttamente questa identità del servizio per l'autenticazione con Data Lake Store, analogamente all'uso dell'entità del servizio. Consente alla factory designata di accedere e copiare i dati da/in Data Lake Store.

Per usare l'autenticazione basata sull'identità del servizio gestito (MSI):

1. [Recuperare l'identità del servizio Data Factory](data-factory-service-identity.md#retrieve-service-identity) copiando il valore di "SERVICE IDENTITY APPLICATION ID" generato con la factory.
2. Concedere all'identità del servizio l'accesso a Data Lake Store usando la stessa procedura valida per l'entità del servizio. Per la procedura dettagliata, vedere [Autenticazione da servizio a servizio: Assegnare l'applicazione Azure AD al file o alla cartella dell'account di Azure Data Lake Store](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder).

>[!TIP]
> Assicurarsi di concedere all'identità del servizio Data factory un'autorizzazione appropriata in Azure Data Lake Store:
>- Come origine, concedere almeno l'autorizzazione di accesso ai dati **Lettura ed esecuzione** per l'elenco e la copia del contenuto di una cartella oppure l'autorizzazione **Lettura** per la copia di un file singolo. Nessun requisito per il controllo di accesso a livello di account (IAM).
>- Come sink, concedere almeno l'autorizzazione di accesso ai dati **Scrittura ad esecuzione** per creare elementi figlio nella cartella. E se si esegue la copia tramite runtime di integrazione di Azure (sia origine che sink sono nel cloud) per permettere a Data Factory di rilevare l'area di Data Lake Store, concedere almeno il ruolo **Lettore** nel controllo di accesso dell'account (IAM). Se si vuole evitare questo ruolo IAM, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Data Lake Store ed eseguire l'associazione nel servizio collegato di Data Lake Store, come nell'esempio seguente:

In Azure Data Factory non è necessario specificare alcuna proprietà oltre alle informazioni generali di Data Lake Store nel servizio collegato.

**Esempio:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Azure Data Lake Store.

Per copiare dati da/in Azure Data Lake Store, impostare la proprietà type del set di dati su **AzureDataLakeStoreFile**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **AzureDataLakeStoreFile** |Sì |
| folderPath | Percorso del contenitore e della cartella nell'archiviazione file. Esempio: rootfolder/subfolder/ |Sì |
| fileName | Se si vuole copiare da/in un file specifico, specificare il nome del file in **folderPath**. Se non si specifica alcun valore per questa proprietà, il set di dati seleziona tutti i file nella cartella.<br/><br/>Se fileName non è specificato per un set di dati di output e **preserveHierarchy** non è specificato nel sink dell'attività, l'attività di copia genera automaticamente il nome del file con il formato seguente: `Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`. Ad esempio: `Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz`. |No |
| format | Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](supported-file-formats-and-compression-codecs.md#text-format), [JsonFormat](supported-file-formats-and-compression-codecs.md#json-format), [AvroFormat](supported-file-formats-and-compression-codecs.md#avro-format), [OrcFormat](supported-file-formats-and-compression-codecs.md#orc-format) e [ParquetFormat](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Azure Data Lake.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store come origine

Per copiare dati da Azure Data Lake Store, impostare il tipo di origine nell'attività di copia su **AzureDataLakeStoreSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **AzureDataLakeStoreSource** |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata.<br/>I valori consentiti sono: **true** (predefinito), **false** | No |

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

Per copiare dati in BLOB di Azure, impostare il tipo di sink nell'attività di copia su **AzureDataLakeStoreSink**. Nella sezione **sink** sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su: **AzureDataLakeStoreSink** |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (predefinito)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente. | No |

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

### <a name="recursive-and-copybehavior-examples"></a>esempi ricorsivi e copyBehavior

In questa sezione viene descritto il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La cartella di destinazione Cartella1 viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5. |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome file generato automaticamente |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome file generato automaticamente. Nome generato automaticamente per File1<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).