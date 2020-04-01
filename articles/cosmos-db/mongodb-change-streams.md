---
title: Modificare i flussi nell'API di Azure Cosmos DB per MongoDBChange streams in Azure Cosmos DB's API for MongoDB
description: Informazioni su come usare i flussi di modifiche nell'API di Azure Cosmos DB per MongoDB per ottenere le modifiche apportate ai dati.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: ecfa98241f74aac43a827b645a6ed877624d643d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437817"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Modificare i flussi nell'API di Azure Cosmos DB per MongoDBChange streams in Azure Cosmos DB's API for MongoDB

[Il](change-feed.md) supporto dei feed di modifiche nell'API di Azure Cosmos DB per MongoDB è disponibile tramite l'API dei flussi di modifiche. Utilizzando l'API dei flussi di modifiche, le applicazioni possono ottenere le modifiche apportate alla raccolta o agli elementi in una singola partizione. Successivamente è possibile eseguire ulteriori azioni in base ai risultati. Le modifiche apportate agli elementi nella raccolta vengono acquisite nell'ordine dell'ora di modifica e l'ordinamento è garantito in base alla chiave di partizione.

> [!NOTE]
> Per usare i flussi di modifica, creare l'account con la versione 3.6 dell'API di Azure Cosmos DB per MongoDB o versione successiva. Se si eseguono gli esempi di flusso di `Unrecognized pipeline stage name: $changeStream` modifica rispetto a una versione precedente, è possibile che venga visualizzato l'errore.

## <a name="current-limitations"></a>Limitazioni correnti

Le limitazioni seguenti sono applicabili quando si usano flussi di modifiche:The following limitations are applicable when using change streams:

* Le `operationType` `updateDescription` proprietà e non sono ancora supportate nel documento di output.
* I `insert` `update`tipi `replace` di operazioni , e sono attualmente supportati. L'operazione di eliminazione o altri eventi non sono ancora supportati.

A causa di queste limitazioni, la fase $match, $project fase e le opzioni fullDocument sono necessarie, come illustrato negli esempi precedenti.

A differenza del feed di modifiche nell'API SQL di Azure Cosmos DB, non esiste una libreria del processore del [feed](change-feed-processor.md) di modifiche separata per l'utilizzo di flussi di modifiche o la necessità di un contenitore di lease. Non è attualmente disponibile il supporto per [i trigger di Funzioni](change-feed-functions.md) di Azure per elaborare i flussi di modifiche.

## <a name="error-handling"></a>Gestione degli errori

I seguenti codici di errore e messaggi sono supportati quando si usano i flussi di modifica:

* **Codice di errore HTTP 16500** - Quando il flusso di modifiche viene limitato, restituisce una pagina vuota.

* **NamespaceNotFound (OperationType Invalidate)** - Se si esegue il flusso di modifica nella raccolta `NamespaceNotFound` che non esiste o se la raccolta viene eliminata, viene restituito un errore. Poiché `operationType` la proprietà non può essere restituita nel `operationType Invalidate` documento `NamespaceNotFound` di output, anziché l'errore, viene restituito l'errore.

## <a name="examples"></a>Esempi

Nell'esempio seguente viene illustrato come ottenere flussi di modifica su tutti gli elementi nella raccolta. In questo esempio viene creato un cursore per controllare gli elementi quando vengono inseriti, aggiornati o sostituiti. La `$match` fase, `$project` la `fullDocument` fase e l'opzione sono necessarie per ottenere i flussi di modifica. Il controllo delle operazioni di eliminazione tramite flussi di modifiche non è attualmente supportato. In alternativa, è possibile aggiungere un indicatore soft marker sugli elementi che vengono eliminati. Ad esempio, è possibile aggiungere un attributo nell'elemento denominato "deleted". Quando si desidera eliminare l'elemento, è possibile `true` impostare "eliminato" su e impostare un valore TTL sull'elemento. Poiché l'aggiornamento `true` di "eliminato" a è un aggiornamento, questa modifica sarà visibile nel flusso di modifiche.

### <a name="javascript"></a>JavaScript:

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

### <a name="c"></a>C#:

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Modifiche all'interno di una singola partizioneChanges within a single shard

Nell'esempio seguente viene illustrato come ottenere le modifiche agli elementi all'interno di una singola partizione. In questo esempio vengono ottenute le modifiche degli elementi con chiave di partizione uguale a "a" e il valore della chiave di partizione uguale a "1". È possibile avere client diversi che leggono le modifiche da partizioni diverse in parallelo.

```javascript
var cursor = db.coll.watch(
    [
        {
            $match: {
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="next-steps"></a>Passaggi successivi

* [Usare il tempo di attesa per la scadenza automatica dei dati nell'API di Azure Cosmos DB per MongoDBUse time to live to expire data automatically in Azure Cosmos DB's API for MongoDB](mongodb-time-to-live.md)
* [Indicizzazione nell'API di Azure Cosmos DB per MongoDB](mongodb-indexing.md)
