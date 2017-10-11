---
title: Copiare dati da/in Archiviazione tabelle di Azure usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi di origine supportati in Archiviazione tabelle di Azure o da Archiviazione tabelle in archivi di sink supportati usando Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2fa03b82750585454430da0c29392db57b20d3c9
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>Copiare dati da o in Tabella di Azure usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-azure-table-connector.md)
> * [Versione 2 - Anteprima](connector-azure-table-storage.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Tabella di Azure. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore Archiviazione tabelle di Azure nella versione 1](v1/data-factory-azure-table-connector.md).

## <a name="supported-scenarios"></a>Scenari supportati

È possibile copiare dati da qualsiasi archivio dati di origine supportato in Tabella di Azure o da Tabella di Azure in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Tabella di Azure supporta la copia dei dati usando sia l'autenticazione basata sulla **chiave dell'account** sia l'autenticazione basata sulla **firma di accesso condiviso del servizio**.

## <a name="get-started"></a>Attività iniziali
È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Archiviazione tabelle di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

### <a name="using-account-key"></a>Uso della chiave dell'account

È possibile creare un servizio collegato di Archiviazione di Azure usando la chiave dell'account, che garantisce a Data Factory l'accesso globale ad Archiviazione di Azure. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureStorage** |Sì |
| connectionString | Specificare le informazioni necessarie per connettersi all’archivio Azure per la proprietà connectionString. Contrassegnare questo campo come SecureString. |Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>Uso dell'autenticazione basata sulla firma di accesso condiviso del servizio

È possibile anche creare un servizio collegato di Archiviazione di Azure usando la firma di accesso condiviso, che offre a Data Factory l'accesso limitato o a scadenza a tutte le risorse o a risorse specifiche nella risorsa di archiviazione.

Una firma di accesso condiviso fornisce accesso delegato alle risorse nell'account di archiviazione. Consente di concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato. Per informazioni dettagliate sulle firme di accesso condiviso, vedere [Firme di accesso condiviso, parte 1: conoscere il modello di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory supporta attualmente solo il **servizio di firma di accesso condiviso**, ma non la firma di accesso condiviso dell'account. Vedere [Tipi di firme di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) per informazioni dettagliate su questi due tipi e sulla modalità di costruzione. L'URL di firma di accesso condiviso generabile dal portale di Azure o da Storage Explorer è una firma di accesso condiviso dell'account, che non è supportata.
>

Per usare l'autenticazione basata sulla firma di accesso condiviso del servizio, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureStorage** |Sì |
| sasUri | Specificare l'URI della firma di accesso condiviso per le risorse di Archiviazione di Azure come BLOB, contenitore o tabella. Contrassegnare questo campo come SecureString. |Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Quando si crea un **URI della firma di accesso condiviso**, considerare quanto segue:

- Impostare le **autorizzazioni** appropriate di lettura o scrittura per gli oggetti in base al modo in cui il servizio collegato (lettura, scrittura, lettura/scrittura) viene usato nella data factory.
- Impostare **Ora di scadenza** in modo appropriato. L'accesso agli oggetti di Archiviazione di Azure non deve scadere nel periodo attivo della pipeline.
- L'URI deve essere creato al giusto livello di tabella, in base alle necessità.

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Tabella di Azure.

Per copiare dati da/in Tabella di Azure, impostare la proprietà type del set di dati su **AzureTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **AzureTable** |Sì |
| tableName |Nome della tabella nell'istanza del database di tabelle di Azure a cui fa riferimento il servizio collegato. |Sì |

**Esempio:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema da Data Factory

Per gli archivi di dati privi di schema, ad esempio Tabella di Azure, il servizio Data Factory deduce lo schema in uno dei modi seguenti:

1. Se si specifica la struttura dei dati usando la proprietà **structure** nella definizione del set di dati, il servizio Data Factory considera la struttura come schema. In questo caso, se una riga non contiene un valore per una colonna, viene inserito un valore null.
2. Se non si specifica la struttura dei dati usando la proprietà **structure** nella definizione del set di dati, Data Factory deduce lo schema usando la prima riga di dati. In questo caso, se la prima riga non contiene lo schema completo, alcune colonne non sono presenti nel risultato dell'operazione di copia.

Di conseguenza, per le origini dati prive di schema, la procedura consigliata consiste nello specificare la struttura dei dati usando la proprietà **structure** .

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Tabella di Azure.

### <a name="azure-table-as-source"></a>Tabella di Azure come origine

Per copiare dati da Tabella di Azure, impostare il tipo di origine nell'attività di copia su **AzureTableSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **AzureTableSource** |Sì |
| AzureTableSourceQuery |Usare la query personalizzata di Tabella di Azure per leggere i dati. Vedere gli esempi nella sezione successiva. |No |
| azureTableSourceIgnoreTableNotFound |Indica se ignorare l'eccezione di tabella inesistente.<br/>I valori consentiti sono: **True**, **False** (predefinito). |No |

### <a name="azuretablesourcequery-examples"></a>esempi di azureTableSourceQuery

Se la colonna della tabella di Azure è di tipo stringa:

```json
"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', <datetime parameter>)"
```

Se la colonna della tabella di Azure è di tipo datetime:

```json
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', <datetime parameter>, <datetime parameter>)"
```

### <a name="azure-table-as-sink"></a>Tabella di Azure come sink

Per copiare dati da Tabella di Azure, impostare il tipo di origine nell'attività di copia su **AzureTableSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **AzureTableSink** |Sì |
| azureTableDefaultPartitionKeyValue |Valore predefinito della chiave di partizione che può essere usato dal sink. |No |
| azureTablePartitionKeyName |Specificare il nome della colonna i cui valori vengono usati come chiavi di partizione. Se non specificato, viene usato "AzureTableDefaultPartitionKeyValue" come chiave di partizione. |No |
| azureTableRowKeyName |Specificare il nome della colonna i cui valori vengono usati come chiavi di riga. Se non specificato, usare un GUID per ogni riga. |No |
| azureTableInsertType |Modalità di inserimento dei dati in una tabella di Azure. Questa proprietà verifica se per le righe esistenti nella tabella di output con chiavi di partizione e di riga corrispondenti i valori vengono sostituiti o uniti. <br/><br/>I valori consentiti sono: **Merge** (predefinito) e **Sostituisci**. <br/><br> Queste impostazioni vengono applicate a livello di riga, non a livello di tabella, e nessuna delle due opzioni elimina le righe della tabella di output che non esistono nell'input. Per scoprire come funzionano queste impostazioni (unione e sostituzione), vedere gli argomenti [Insert or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) (Inserire o unire un'entità) e [Insert or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) (Inserire o sostituire un'entità). |No |
| writeBatchSize |Inserisce dati nella tabella di Azure quando viene raggiunto il writeBatchSize o writeBatchTimeout.<br/>I valori consentiti sono: integer (il numero di righe) |No (il valore predefinito è 10.000) |
| writeBatchTimeout |Inserisce dati nella tabella di Azure quando viene raggiunto il writeBatchSize o writeBatchTimeout.<br/>I valori consentiti sono: intervallo di tempo. Ad esempio: "00:20:00" (20 minuti) |No (il valore predefinito è 90 secondi, il timeout predefinito del client di archiviazione) |

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

È necessario eseguire il mapping di una colonna di origine a una colonna di destinazione usando la proprietà di conversione prima di poter usare la colonna di destinazione come azureTablePartitionKeyName.

Nell'esempio seguente, viene eseguito il mapping della colonna di origine DivisionID alla colonna di destinazione DivisionID.

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

Quando si copiano dati da/in Tabella di Azure, vengono usati i mapping seguenti tra i tipi di dati di Tabella di Azure e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

Quando si spostano i dati da e verso tabelle di Azure, i seguenti [mapping definiti dal servizio tabelle di Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) vengono usati dai tipi di OData di tabelle di Azure al tipo di .NET e viceversa.

| Tipo di dati di Tabella di Azure | Tipo di dati provvisori di Data Factory | Dettagli |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Una matrice di byte di dimensioni fino a 64 KB. |
| Edm.Boolean |bool |Valore booleano. |
| Edm.DateTime |DateTime |Un valore a 64 bit espresso come Coordinated Universal Time (UTC). L'intervallo DateTime supportato inizia dalle 00:00, 1 gennaio, 1601 D.C. (C.E.), UTC. L'intervallo termina il 31 dicembre 9999. |
| Edm.Double |double |Un valore a virgola mobile a 64 bit. |
| Edm.Guid |Guid |Un identificatore univoco globale a 128 bit. |
| Edm.Int32 |Int32 |Un valore integer a 32 bit. |
| Edm.Int64 |Int64 |Un valore integer a 64 bit. |
| Edm.String |String |Un valore con codifica UTF-16. I valori delle stringhe possono essere di dimensioni fino a 64 KB. |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
