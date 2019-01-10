---
title: Copiare dati da o verso Azure Cosmos DB (API MongoDB) usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi dati di origine supportati da o verso Azure Cosmos DB (API MongoDB) in archivi sink supportati usando Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: a28d0e819243810486179e7219ad3cb48487a299
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107175"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-mongodb-api-by-using-azure-data-factory"></a>Copiare dati da o verso Azure Cosmos DB (API MongoDB) usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso Azure Cosmos DB (API MongoDB). È basato sull'articolo [Attività di copia in Azure Data Factory](copy-activity-overview.md), che presenta una panoramica generale dell'attività di copia.

>[!NOTE]
>Questo connettore supporta solo la copia di dati da o verso l'API MongoDB di Cosmos DB. Per l'API SQL, vedere il [connettore API SQL di Cosmos DB](connector-azure-cosmos-db.md). Al momento, non sono supportati altri tipi di API.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Azure Cosmos DB (API MongoDB) in qualsiasi archivio dati sink supportato o da qualsiasi archivio dati di origine supportato in Azure Cosmos DB (API MongoDB). Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

È possibile usare il connettore Azure Cosmos DB (API MongoDB) per:

- Copiare dati da e verso l'[API MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) di Azure Cosmos DB.
- Scrivere in Azure Cosmos DB tramite **insert** o **upsert**.
- Importare ed esportare documenti JSON così come sono o copiare dati da o in un set di dati tabulari, ad esempio un database SQL e un file CSV. Per copiare documenti così come sono da o verso file JSON o da o verso un'altra raccolta di Azure Cosmos DB, vedere [Importare o esportare documenti JSON](#importexport-json-documents).

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti presentano informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche per Azure Cosmos DB (API MongoDB).

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Azure Cosmos DB (API MongoDB) sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **CosmosDbMongoDbApi**. | Yes |
| connectionString |Specificare la stringa di connessione per l'API MongoDB di Azure Cosmos DB. È possibile trovarla nel portale di Azure -> pannello di Cosmos DB -> stringa di connessione primaria o secondaria, con il criterio di `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Contrassegnare questo campo come tipo **SecureString** per archiviare la password in modo sicuro in Data Factory. È anche possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| database | Nome del database a cui si desidera accedere. | Yes |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, viene usato il tipo Azure Integration Runtime predefinito. |No  |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md). Per il set di dati di Azure Cosmos DB (API MongoDB) sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **CosmosDbMongoDbApiCollection**. |Yes |
| collectionName |Il nome della raccolta di Azure Cosmos DB. |Yes |

**Esempio**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB MongoDB API linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Azure Cosmos DB (API MongoDB).

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipelines](concepts-pipelines-activities.md) (Pipeline).

### <a name="azure-cosmos-db-mongodb-api-as-source"></a>Azure Cosmos DB (API MongoDB) come origine

Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **CosmosDbMongoDbApiSource**. |Yes |
| filter | Specifica il filtro di selezione usando gli operatori di query. Per restituire tutti i documenti in una raccolta, omettere questo parametro o passare un documento vuoto ({}). | No  |
| cursorMethods.project | Specifica i campi da restituire nei documenti per la proiezione. Per restituire tutti i campi nei documenti corrispondenti, omettere questo parametro. | No  |
| cursorMethods.sort | Specifica l'ordine in cui la query restituisce i documenti corrispondenti. Fare riferimento a [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No  |
| cursorMethods.limit | Specifica il numero massimo di documenti restituibili dal server. Fare riferimento a [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No  | 
| cursorMethods.skip | Specifica il numero di documenti da ignorare e la posizione da cui MongoDB inizia a restituire i risultati. Fare riferimento a [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No  |
| batchSize | Specifica il numero di documenti da restituire in ogni batch di risposta dall'istanza di MongoDB. Nella maggior parte dei casi, la modifica delle dimensioni del batch non influirà sull'utente o sull'applicazione. I limiti di Cosmos DB per ciascun batch sono di 40 MB, che è la somma del numero di batchSize delle dimensioni dei documenti. Diminuire il valore se il documento è troppo grande. | No <br/>(il valore predefinito è **100**) |

>[!TIP]
>Il supporto per Azure Data Factory usa documenti BSON in **modalità strict**. Assicurarsi che la query di filtro sia in modalità strict anziché in modalità shell. Per altre informazioni consultare il [manuale di MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Esempio**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB MongoDB API input dataset name>",
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

### <a name="azure-cosmos-db-mongodb-api-as-sink"></a>Azure Cosmos DB (API MongoDB) come sink

Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del sink dell'attività di copia deve essere impostata su **CosmosDbMongoDbApiSink**. |Yes |
| writebehavior |Descrive come scrivere i dati in Azure Cosmos DB. Valori consentiti: **insert** e **upsert**.<br/><br/>Il comportamento di **upsert** consiste nella sostituzione del documento se esiste già un documento con lo stesso ID; in caso contrario, il documento viene inserito.<br /><br />**Nota**: Data Factory genera automaticamente un ID per un documento se non è specificato nel documento originale o tramite il mapping di colonna. Questo vuol dire che è necessario assicurarsi che il documento abbia un ID in modo che **upsert** funzioni come previsto. |No <br />(il valore predefinito è **insert**) |
| writeBatchSize | La proprietà **writeBatchSize** controlla le dimensioni dei documenti da scrivere in ciascun batch. È possibile provare ad aumentare il valore di **writeBatchSize** per migliorare le prestazioni e a ridurre il valore se le dimensioni dei documenti sono troppo grandi. |No <br />(il valore predefinito è **10.000**) |
| writeBatchTimeout | Il tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout. Il valore consentito è timespan. | No <br/>(il valore predefinito è **00:30:00** - 30 minuti) |

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
>Per importare documenti JSON così come sono, fare riferimento alla sezione [Importare o esportare documenti JSON](#import-or-export-json-documents); per copiare da dati in forma tabulare, fare riferimento a [Mapping dello schema](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Importare o esportare documenti JSON

È possibile usare questo connettore Azure Cosmos DB per eseguire facilmente le operazioni seguenti:

* Importare in Azure Cosmos DB documenti JSON da varie origini, tra cui Archiviazione BLOB di Azure, Azure Data Lake Store e altri archivi basati su file supportati da Azure Data Factory.
* Esportare documenti JSON da una raccolta Azure Cosmos DB in diversi archivi basati su file.
* Copiare documenti tra due raccolte Azure Cosmos DB così come sono.

Per ottenere la copia senza schema, ignorare la sezione "struttura" (chiamata anche *schema*) nel set di dati e il mapping dello schema nell'attività di copia.

## <a name="schema-mapping"></a>Mapping dello schema

Per copiare dati dall'API MongoDB di Cosmos DB API nel sink tabulare o viceversa, fare riferimento a [Mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping).

Appositamente per la scrittura in Cosmos DB, per assicurarsi di popolare Cosmos DB con l'ID dell'oggetto corretto dai dati di origine, è disponibile una colonna "id" nella tabella del database SQL; se si desidera usare il relativo valore come ID del documento in MongoDB per insert/upsert, è necessario impostare il mapping dello schema appropriato in base alla definizione della modalità strict di MongoDB (`_id.$oid`) come illustrato di seguito:

![ID mappa del sink MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Dopo l'esecuzione dell'attività di copia, l'ObjectId BSON viene generato nel sink:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
