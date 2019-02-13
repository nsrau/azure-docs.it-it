---
title: Copiare dati in e da Archiviazione tabelle di Azure usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi di origine supportati in Archiviazione tabelle di Azure o da Archiviazione tabelle in archivi di sink supportati, usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 32fc3f1c93261f6fb19c084f51dea4942310ac47
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664149"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Copiare dati in e da Archiviazione tabelle di Azure usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1](v1/data-factory-azure-table-connector.md)
> * [Versione corrente](connector-azure-table-storage.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati in e da Archiviazione tabelle di Azure. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da qualsiasi archivio di dati di origine supportato in Archiviazione tabelle. È anche possibile copiare dati da Archiviazione tabelle in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Tabella di Azure supporta la copia dei dati usando sia l'autenticazione basata sulla chiave dell'account sia l'autenticazione basata sulla firma di accesso condiviso del servizio.

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per Archiviazione tabelle.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

### <a name="use-an-account-key"></a>Usare una chiave dell'account

È possibile creare un servizio collegato di Archiviazione di Azure usando la chiave dell'account, che garantisce l'accesso globale all'archiviazione dalla data factory. Sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureTableStorage**. |Sì |
| connectionString | Specificare le informazioni necessarie per connettersi all'archiviazione per la proprietà connectionString. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la chiave dell'account in Azure Key Vault ed eseguire il pull della configurazione di `accountKey` dalla stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

>[!NOTE]
>L'uso di un servizio collegato di tipo "AzureStorage", è ancora supportato così com'è, tuttavia in futuro verrà consigliato di usare il nuovo tipo di servizio collegato "AzureTableStorage".

**Esempio:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la chiave dell'account in Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-shared-access-signature-authentication"></a>Uso dell'autenticazione con firma di accesso condiviso

È anche possibile creare un servizio collegato di archiviazione tramite una firma di accesso condiviso. Consente alla data factory l'accesso con restrizioni o limiti di tempo a tutte le risorse o a risorse specifiche nell'archiviazione.

Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile usarla per concedere a un client autorizzazioni limitate per gli oggetti nell'account di archiviazione per un periodo di tempo e con un set di autorizzazioni specificati. Non è necessario condividere le chiavi di accesso degli account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma di accesso condiviso al costruttore o al metodo appropriato. Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Data Factory supporta attualmente sia le **firme di accesso condiviso del servizio** che le **firme di accesso condiviso dell'account**. Per altre informazioni su questi due tipi e sulla modalità di costruzione, vedere [Tipi di firme di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> Per generare una firma di accesso condiviso del servizio per l'account di archiviazione, è possibile eseguire i comandi di PowerShell seguenti. Sostituire i segnaposto e concedere l'autorizzazione necessaria.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Per usare l'autenticazione basata sulla firma di accesso condiviso, sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureTableStorage**. |Sì |
| sasUri | Specificare l'URI SAS dell'URI della firma di accesso condiviso alla tabella. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire il token di firma di accesso condiviso in Azure Key Vault per sfruttare la rotazione automatica e rimuovere la parte del token. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

>[!NOTE]
>L'uso di un servizio collegato di tipo "AzureStorage", è ancora supportato così com'è, tuttavia in futuro verrà consigliato di usare il nuovo tipo di servizio collegato "AzureTableStorage".

**Esempio:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la chiave dell'account in Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Quando si crea un URI di firma di accesso condiviso, tenere presente quanto segue:

- Impostare le autorizzazioni appropriate di lettura o scrittura per gli oggetti in base al modo in cui il servizio collegato (lettura, scrittura, lettura/scrittura) viene usato nella data factory.
- Impostare **Ora di scadenza** in modo appropriato. Assicurarsi che l'accesso agli oggetti di archiviazione non scada nel periodo attivo della pipeline.
- L'URI deve essere creato al giusto livello di tabella, in base alle necessità.

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Tabella di Azure.

Per copiare dati in e da Tabella di Azure, impostare la proprietà type del set di dati su **AzureTable**. Sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureTable**. |Sì |
| tableName |Nome della tabella nell'istanza del database di Archiviazione tabelle a cui fa riferimento il servizio collegato. |Sì |

**Esempio:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema da Data Factory

Per gli archivi di dati privi di schema, ad esempio Tabella di Azure, Data Factory deduce lo schema in uno dei modi seguenti:

* Se si specifica la struttura dei dati usando la proprietà **structure** nella definizione del set di dati, Data Factory considera la struttura come schema. In questo caso, se una riga non contiene un valore per una colonna, viene inserito un valore null.
* Se non si specifica la struttura dei dati usando la proprietà **structure** nella definizione del set di dati, Data Factory deduce lo schema usando la prima riga di dati. In questo caso, se la prima riga non contiene lo schema completo, alcune colonne risultano mancanti nel risultato dell'operazione di copia.

Per le origini dati prive di schema, la procedura consigliata consiste nello specificare la struttura dei dati usando la proprietà **structure**.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Tabella di Azure.

### <a name="azure-table-as-a-source-type"></a>Tabelle di Azure come tipo di origine

Per copiare dati da Tabella di Azure, impostare il tipo di origine nell'attività di copia su **AzureTableSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **AzureTableSource**. |Sì |
| AzureTableSourceQuery |Usare la query di Archiviazione tabelle personalizzata per leggere i dati. Vedere gli esempi nella sezione seguente. |No  |
| azureTableSourceIgnoreTableNotFound |Indica se consentire l'eccezione di tabella non esistente.<br/>I valori consentiti sono **True** e **False** (predefinito). |No  |

### <a name="azuretablesourcequery-examples"></a>esempi di azureTableSourceQuery

Se la colonna della tabella di Azure è di tipo datetime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Se la colonna della tabella di Azure è di tipo string:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Se si usa un parametro della pipeline, eseguire il cast del valore datetime al formato corretto come illustrato negli esempi precedenti.

### <a name="azure-table-as-a-sink-type"></a>Tabelle di Azure come tipo di sink

Per copiare dati in Tabella di Azure, impostare il tipo di sink nell'attività di copia su **AzureTableSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **AzureTableSink**. |Sì |
| azureTableDefaultPartitionKeyValue |Valore predefinito della chiave di partizione che può essere usato dal sink. |No  |
| azureTablePartitionKeyName |Specificare il nome della colonna i cui valori vengono usati come chiavi di partizione. Se non specificato, viene usato "AzureTableDefaultPartitionKeyValue" come chiave di partizione. |No  |
| azureTableRowKeyName |Specificare il nome della colonna i cui valori vengono usati come chiave di riga. Se non specificato, usare un GUID per ogni riga. |No  |
| azureTableInsertType |Modalità di inserimento dei dati in una tabella di Azure. Questa proprietà verifica se per le righe esistenti nella tabella di output con chiavi di partizione e di riga corrispondenti i valori vengono sostituiti o uniti. <br/><br/>I valori consentiti sono: **merge** (predefinito) e **replace**. <br/><br> Questa impostazione si applica a livello di riga e non a livello di tabella. Nessuna delle due opzioni consente di eliminare righe nella tabella di output che non esistono nell'input. Per scoprire come funzionano le impostazioni merge e replace, vedere[Insert or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) (Inserire o unire un'entità) e [Insert or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) (Inserire o sostituire un'entità). |No  |
| writeBatchSize |Inserisce dati in Tabella di Azure quando viene raggiunto il valore di writeBatchSize o writeBatchTimeout.<br/>I valori consentiti sono integer (numero di righe). |No (il valore predefinito è 10.000) |
| writeBatchTimeout |Inserisce dati in Tabella di Azure quando viene raggiunto il valore di writeBatchSize o writeBatchTimeout.<br/>I valori consentiti sono un intervallo di tempo. Ad esempio "00:20:00" (20 minuti). |No (il valore predefinito è 90 secondi, il timeout predefinito del client di archiviazione) |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

È necessario eseguire il mapping di una colonna di origine a una colonna di destinazione usando la proprietà **translator** prima di poter usare la colonna di destinazione come azureTablePartitionKeyName.

Nell'esempio seguente viene eseguito il mapping della colonna di origine DivisionID alla colonna di destinazione DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" è specificato come chiave di partizione.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapping dei tipi di dati per Tabella di Azure

Quando si copiano dati da e in Tabella di Azure, vengono usati i mapping seguenti tra i tipi di dati di Tabella di Azure e i tipi di dati provvisori di Data Factory. Per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

Quando si spostano i dati da e verso Tabella di Azure, i seguenti [mapping definiti da Tabella di Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) vengono usati dai tipi OData di Tabella di Azure al tipo .NET e viceversa.

| Tipo di dati di Tabella di Azure | Tipo di dati provvisorio di Data Factory | Dettagli |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Una matrice di byte di dimensioni fino a 64 KB. |
| Edm.Boolean |bool |Valore booleano. |
| Edm.DateTime |DateTime |Un valore a 64 bit espresso come Coordinated Universal Time (UTC). L'intervallo DateTime supportato inizia a mezzanotte del 1 gennaio 1601 D.C. (C.E.), UTC. L'intervallo termina il 31 dicembre 9999. |
| Edm.Double |Double |Un valore a virgola mobile a 64 bit. |
| Edm.Guid |Guid |Un identificatore univoco globale a 128 bit. |
| Edm.Int32 |Int32 |Un valore integer a 32 bit. |
| Edm.Int64 |Int64 |Un valore integer a 64 bit. |
| Edm.String |string |Un valore con codifica UTF-16. I valori string possono avere dimensioni fino a 64 KB. |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
