---
title: Copiare dati da o verso l'API Azure Cosmos DB per MongoDB usando Data Factory
description: Informazioni su come copiare dati da archivi dati di origine supportati nell'API di Azure Cosmos DB per MongoDB o da quest'ultima in archivi sink supportati usando Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 0da3452964a7c4bb7d2a22ce4cd5164ad8c1e3fb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280699"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Copiare dati da o verso l'API di Azure Cosmos DB per MongoDB usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da o verso l'API di Azure Cosmos DB per MongoDB. È basato sull'articolo [Attività di copia in Azure Data Factory](copy-activity-overview.md), che presenta una panoramica generale dell'attività di copia.

>[!NOTE]
>Questo connettore supporta solo la copia di dati da o verso l'API di Azure Cosmos DB per MongoDB. Per l'API SQL, vedere il [connettore API SQL di Cosmos DB](connector-azure-cosmos-db.md). Al momento, non sono supportati altri tipi di API.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati dall'API di Azure Cosmos DB per MongoDB in qualsiasi archivio dati sink supportato o da qualsiasi archivio dati di origine supportato nell'API di Azure Cosmos DB per MongoDB. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

È possibile usare il connettore API di Azure Cosmos DB per MongoDB per:

- Copiare dati da e verso l'[API di Azure Cosmos DB per MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Scrivere in Azure Cosmos DB tramite **insert** o **upsert**.
- Importare ed esportare documenti JSON come sono o copiare dati da o in un set di dati tabulari, ad esempio un database SQL e un file CSV. Per copiare documenti come sono da o in file JSON oppure da o in un'altra raccolta di Azure Cosmos DB, vedere Importare o esportare documenti JSON.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti forniscono informazioni dettagliate sulle proprietà che è possibile usare per definire le entità di Data Factory specifiche per l'API di Azure Cosmos DB per MongoDB.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato dell'API di Azure Cosmos DB per MongoDB sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | obbligatori |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **CosmosDbMongoDbApi**. | Sì |
| connectionString |Specificare la stringa di connessione per l'API di Azure Cosmos DB per MongoDB. È possibile trovarla nel portale di Azure -> pannello di Cosmos DB -> stringa di connessione primaria o secondaria, con il criterio di `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Contrassegnare questo campo come tipo **SecureString** per archiviare la password in modo sicuro in Data Factory. È anche possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| database | Nome del database a cui si vuole accedere. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, viene usato il tipo Azure Integration Runtime predefinito. |No |

**Esempio**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md). Per il set di dati dell'API di Azure Cosmos DB per MongoDB sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | obbligatori |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **CosmosDbMongoDbApiCollection**. |Sì |
| collectionName |Nome della raccolta di Azure Cosmos DB. |Sì |

**Esempio**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione fornisce un elenco delle proprietà supportate dall'origine e dal sink dell'API di Azure Cosmos DB per MongoDB.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipelines](concepts-pipelines-activities.md) (Pipeline).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>API di Azure Cosmos DB per MongoDB come origine

Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | obbligatori |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **CosmosDbMongoDbApiSource**. |Sì |
| filter | Specifica il filtro di selezione usando gli operatori di query. Per restituire tutti i documenti in una raccolta, omettere questo parametro o passare un documento vuoto ({}). | No |
| cursorMethods.project | Specifica i campi da restituire nei documenti per la proiezione. Per restituire tutti i campi nei documenti corrispondenti, omettere questo parametro. | No |
| cursorMethods.sort | Specifica l'ordine in cui la query restituisce i documenti corrispondenti. Fare riferimento a [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods.limit | Specifica il numero massimo di documenti restituiti dal server. Fare riferimento a [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No | 
| cursorMethods.skip | Specifica il numero di documenti da ignorare e la posizione da cui MongoDB inizia a restituire i risultati. Fare riferimento a [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Specifica il numero di documenti da restituire in ogni batch di risposta dall'istanza di MongoDB. Nella maggior parte dei casi, la modifica della dimensione del batch non influisce sull'utente o sull'applicazione. Il limite massimo di Cosmos DB per ogni batch è di 40 MB, che corrisponde alla somma delle dimensioni del numero di documenti definiti in batchSize. Diminuire questo valore se si hanno documenti di grandi dimensioni. | No<br/>(il valore predefinito è **100**) |

>[!TIP]
>Azure Data Factory supporta l'utilizzo di documenti BSON in **modalità strict**. Assicurarsi che la query di filtro sia in modalità strict anziché in modalità shell. Per altre informazioni consultare il [manuale di MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Esempio**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>API di Azure Cosmos DB per MongoDB come sink

Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | obbligatori |
|:--- |:--- |:--- |
| type | La proprietà **type** del sink dell'attività di copia deve essere impostata su **CosmosDbMongoDbApiSink**. |Sì |
| writebehavior |Descrive come scrivere i dati in Azure Cosmos DB. Valori consentiti: **insert** e **upsert**.<br/><br/>Il comportamento di **Upsert** consiste nel sostituire il documento se esiste già un documento con lo stesso `_id`; in caso contrario, inserire il documento.<br /><br />**Nota**: data factory genera automaticamente un `_id` per un documento se non è specificato alcun `_id` nel documento originale o in base al mapping delle colonne. Questo vuol dire che è necessario assicurarsi che il documento abbia un ID in modo che **upsert** funzioni come previsto. |No<br />(il valore predefinito è **insert**) |
| writeBatchSize | La proprietà **writeBatchSize** controlla le dimensioni dei documenti da scrivere in ogni batch. È possibile provare ad aumentare il valore di **writeBatchSize** per migliorare le prestazioni e a ridurre il valore se le dimensioni dei documenti sono troppo grandi. |No<br />(il valore predefinito è **10.000**) |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout. Il valore consentito è TimeSpan. | No<br/>(il valore predefinito è **00:30:00** - 30 minuti) |

**Esempio**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
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
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Per importare documenti JSON come sono, fare riferimento alla sezione [Importare o esportare documenti JSON](#import-or-export-json-documents). Per copiare da dati in forma tabulare, fare riferimento a [Mapping dello schema](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Importare o esportare documenti JSON

È possibile usare questo connettore di Azure Cosmos DB per eseguire facilmente le operazioni seguenti:

* Importare in Azure Cosmos DB documenti JSON da varie origini, tra cui Archiviazione BLOB di Azure, Azure Data Lake Storage e altri archivi basati su file supportati da Azure Data Factory.
* Esportare documenti JSON da una raccolta di Azure Cosmos DB in diversi archivi basati su file.
* Copiare documenti tra due raccolte Azure Cosmos DB così come sono.

Per ottenere la copia senza schema, ignorare la sezione "struttura" (chiamata anche *schema*) nel set di dati e il mapping dello schema nell'attività di copia.

## <a name="schema-mapping"></a>Mapping dello schema

Per copiare dati dall'API di Azure Cosmos DB per MongoDB in un sink tabulare o viceversa, fare riferimento al [mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping).

Appositamente per la scrittura in Cosmos DB, per assicurarsi di popolare Cosmos DB con l'ID dell'oggetto corretto dai dati di origine, è disponibile una colonna "id" nella tabella del database SQL; se si desidera usare il relativo valore come ID del documento in MongoDB per insert/upsert, è necessario impostare il mapping dello schema appropriato in base alla definizione della modalità strict di MongoDB (`_id.$oid`) come illustrato di seguito:

![Eseguire il mapping dell'ID nel sink MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Dopo l'esecuzione dell'attività di copia, nel sink viene generato il valore ObjectId BSON indicato di seguito:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
