---
title: Copiare e trasformare i dati in Azure Cosmos DB (API SQL)
description: Informazioni su come copiare dati da e verso Azure Cosmos DB (API SQL) e e trasformare i dati in Azure Cosmos DB (API SQL) usando Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/13/2019
ms.openlocfilehash: bf24c12e8f1e5b7ee5c529ebffa6c15dd8acbcfc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913414"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiare e trasformare i dati in Azure Cosmos DB (API SQL) utilizzando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versione corrente](connector-azure-cosmos-db.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure Cosmos DB (API SQL) e usare il flusso di dati per trasformare i dati in Azure Cosmos DB (API SQL). Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

>[!NOTE]
>Questo connettore supporta solo Cosmos DB API SQL. Per l'API MongoDB, fare riferimento al [connettore per l'API di Azure Cosmos DB per MongoDB](connector-azure-cosmos-db-mongodb-api.md). Al momento non sono supportati altri tipi di API.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Azure Cosmos DB (API SQL) è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

Per l'attività di copia, questo connettore Azure Cosmos DB (API SQL) supporta:

- Copiare dati da e verso l'[API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) di Azure Cosmos DB.
- Scrivere in Azure Cosmos DB tramite **insert** o **upsert**.
- Importare ed esportare documenti JSON come sono o copiare dati da o in un set di dati tabulari, ad esempio un database SQL e un file CSV. Per copiare documenti come sono da o in file JSON oppure da o in un'altra raccolta di Azure Cosmos DB, vedere Importare o esportare documenti JSON.

Data Factory si integra con la [libreria dell'executor bulk di Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) per offrire le migliori prestazioni durante la scrittura in Azure Cosmos DB.

> [!TIP]
> Il [video sulla migrazione dei dati](https://youtu.be/5-SRNiC_qOU) illustra la procedura di copia dei dati da Archiviazione BLOB di Azure ad Azure Cosmos DB. Il video presenta inoltre alcune considerazioni generali sull'ottimizzazione delle prestazioni per l'inserimento di dati in Azure Cosmos DB.

## <a name="get-started"></a>Inizia oggi stesso

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti presentano informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche per Azure Cosmos DB (API SQL).

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Azure Cosmos DB (API SQL) sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **CosmosDb**. | SÌ |
| connectionString |Specificare le informazioni richieste per connettersi al database di Azure Cosmos DB.<br />**Nota**: è necessario specificare le informazioni sul database nella stringa di connessione come illustrato negli esempi seguenti. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la chiave dell'account in Azure Key Vault e rimuovere la configurazione di `accountKey` dalla stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. |SÌ |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, come valore predefinito viene usato Azure Integration Runtime. |No |

**Esempio**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
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

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md).

Per il set di dati Azure Cosmos DB (API SQL) sono supportate le proprietà seguenti: 

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del set di dati deve essere impostata su **CosmosDbSqlApiCollection**. |SÌ |
| collectionName |Nome della raccolta documenti di Azure Cosmos DB. |SÌ |

Se si usa il set di dati di tipo "DocumentDbCollection", è ancora supportato così com'è per la compatibilità con le versioni precedenti per l'attività di copia e ricerca, non è supportato per il flusso di dati. Si consiglia di utilizzare il nuovo modello in futuro.

**Esempio**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema da Data Factory

Per gli archivi dati privi di schema come Azure Cosmos DB, l'attività di copia deduce lo schema in uno dei modi descritti nell'elenco seguente. A meno che non si voglia [importare o esportare documenti JSON come così sono](#import-or-export-json-documents), la procedura consigliata è quella di specificare la struttura di dati nella sezione **structure**.

Data Factory rispetta il mapping specificato nell'attività. Se una riga non contiene un valore per una colonna, viene inserito un valore null per il valore di colonna.

Se non si specifica un mapping, il servizio Data Factory deduce lo schema usando la prima riga dei dati. Se la prima riga non contiene lo schema completo, alcune colonne non saranno presenti nel risultato dell'operazione di attività.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di Azure Cosmos DB (API SQL).

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API SQL) come origine

Per copiare dati da Azure Cosmos DB (API SQL), impostare il tipo **source** nell'attività di copia su **DocumentDbCollectionSource**. 

Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** dell'origine dell'attività di copia deve essere impostata su **CosmosDbSqlApiSource**. |SÌ |
| query |Specificare la query Azure Cosmos DB per leggere i dati.<br/><br/>Esempio:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Se non specificato, viene eseguita questa istruzione SQL: `select <columns defined in structure> from mycollection` |
| preferredRegions | Elenco preferito delle aree a cui connettersi quando recupera i dati da Cosmos DB. | No |
| pageSize | Numero di documenti per pagina del risultato della query. Il valore predefinito è "-1", che indica l'utilizzo delle dimensioni di pagina dinamiche del lato servizio fino a 1000. | No |

Se si usa l'origine del tipo "DocumentDbCollectionSource", è ancora supportata così com'è per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello in futuro, che offre funzionalità più avanzate per la copia dei dati da Cosmos DB.

**Esempio**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API SQL) come sink

Per copiare dati in Azure Cosmos DB (API SQL), impostare il tipo **sink** nell'attività di copia su **DocumentDbCollectionSink**. 

Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del sink dell'attività di copia deve essere impostata su **CosmosDbSqlApiSink**. |SÌ |
| writeBehavior |Descrive come scrivere i dati in Azure Cosmos DB. Valori consentiti: **insert** e **upsert**.<br/><br/>Il comportamento di **upsert** consiste nella sostituzione del documento se esiste già un documento con lo stesso ID. In caso contrario, il documento viene inserito.<br /><br />**Nota**: Data Factory genera automaticamente un ID per un documento se non è specificato nel documento originale o tramite il mapping di colonna. È quindi necessario assicurarsi che il documento contenga un ID in modo che **upsert** funzioni come previsto. |No<br />(il valore predefinito è **insert**) |
| writeBatchSize | Data Factory usa la [libreria dell'executor bulk di Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) per scrivere dati in Azure Cosmos DB. La proprietà **writeBatchSize** controlla le dimensioni dei documenti che Azure Data Factory fornisce alla libreria. È possibile provare ad aumentare il valore per **writeBatchSize** per migliorare le prestazioni e a ridurre il valore se le dimensioni dei documenti diventano grandi. Vedere i suggerimenti di seguito. |No<br />(il valore predefinito è **10.000**) |
| disableMetricsCollection | Data Factory raccoglie le metriche, ad esempio Cosmos DB ur per l'ottimizzazione delle prestazioni di copia e consigli. Se si è interessati a questo comportamento, specificare `true` per disattivarlo. | No (il valore predefinito è `false`) |

>[!TIP]
>Cosmos DB limita le dimensioni per una singola richiesta a 2 MB. La formula è Dimensioni richiesta = Dimensioni singolo documento * Dimensioni batch di scrittura. Se viene generato l'errore **"Dimensioni della richiesta troppo grandi."** , **ridurre il valore `writeBatchSize`** nella configurazione del sink di copia.

Se si usa l'origine del tipo "DocumentDbCollectionSink", è ancora supportata così com'è per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello in futuro, che offre funzionalità più avanzate per la copia dei dati da Cosmos DB.

**Esempio**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Per informazioni dettagliate, vedere [trasformazione origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) nel flusso di dati del mapping.

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="import-or-export-json-documents"></a>Importare o esportare documenti JSON

È possibile usare questo connettore di Azure Cosmos DB (API SQL) per eseguire facilmente le operazioni seguenti:

* Importare in Azure Cosmos DB documenti JSON da varie origini, tra cui Archiviazione BLOB di Azure, Azure Data Lake Storage e altri archivi basati su file supportati da Azure Data Factory.
* Esportare documenti JSON da una raccolta di Azure Cosmos DB in diversi archivi basati su file.
* Copiare documenti come sono da una raccolta di Azure Cosmos DB a un'altra.

Per ottenere la copia senza schema:

* Quando si usa lo strumento Copia dati, selezionare l'opzione **Export as-is to JSON files or Cosmos DB collection** (Esportare così come sono nel file JSON o in una raccolta di Cosmos DB).
* Quando si usa la creazione di attività, scegliere il formato JSON con l'archivio file corrispondente per origine o sink.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
