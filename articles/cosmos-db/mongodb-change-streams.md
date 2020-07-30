---
title: Flussi di modifiche nell'API di Azure Cosmos DB per MongoDB
description: Informazioni su come usare i flussi di modifiche nell'API di Azure Cosmos DB per MongoDB per ottenere le modifiche apportate ai dati.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/04/2020
ms.author: srchi
ms.custom: devx-track-javascript
ms.openlocfilehash: 845398744637d0e0092934248d4eb4533d7b9344
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87415534"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Flussi di modifiche nell'API di Azure Cosmos DB per MongoDB

Il supporto del [feed di modifiche](change-feed.md) nell'API di Azure Cosmos DB per MongoDB è disponibile tramite l'API dei flussi di modifiche. Con l'API dei flussi di modifiche, le applicazioni possono ottenere le modifiche apportate alla raccolta o agli elementi in una singola partizione. In seguito sarà possibile eseguire altre azioni in base ai risultati. Le modifiche apportate agli elementi nella raccolta vengono acquisite in base all'ora della modifica e l'ordinamento è garantito per ogni chiave di partizione.

> [!NOTE]
> Per usare i flussi di modifiche, creare l'account con la versione 3.6 dell'API di Azure Cosmos DB per MongoDB o una versione successiva. Se gli esempi dei flussi di modifiche vengono eseguiti con una versione precedente, potrebbe essere visualizzato l'errore `Unrecognized pipeline stage name: $changeStream`.

## <a name="current-limitations"></a>Limitazioni correnti

Quando si usano i flussi di modifiche, sono applicabili le limitazioni seguenti:

* Le proprietà `operationType` e `updateDescription` non sono ancora supportate nel documento di output.
* I tipi di operazioni `insert`, `update` e `replace` sono attualmente supportati. 
* L'operazione di eliminazione o altri eventi non sono ancora supportati.

A causa di queste limitazioni, sono necessarie la fase $match, la fase $project e le opzioni fullDocument, come illustrato negli esempi precedenti.

A differenza del feed di modifiche nell'API SQL di Azure Cosmos DB, non è presente una [libreria del processore dei feed di modifiche](change-feed-processor.md) separata per utilizzare i flussi di modifiche e non è necessario un contenitore dei lease. Al momento non è disponibile il supporto dei [trigger di Funzioni di Azure](change-feed-functions.md) per l'elaborazione dei flussi di modifiche.

## <a name="error-handling"></a>Gestione degli errori

Quando si usano i flussi di modifiche, sono supportati i codici e messaggi di errore seguenti:

* **Codice di errore HTTP 16500**: quando il flusso di modifiche è rallentato, viene restituita una pagina vuota.

* **NamespaceNotFound (Invalidate OperationType)** : se si esegue il flusso di modifiche su una raccolta inesistente o se la raccolta viene eliminata, viene restituito un errore `NamespaceNotFound`. Poiché la proprietà `operationType` non può essere restituita nel documento di output, invece dell'errore `operationType Invalidate` viene restituito l'errore `NamespaceNotFound`.

## <a name="examples"></a>Esempi

Nell'esempio seguente viene illustrato come ottenere i flussi di modifiche in tutti gli elementi della raccolta. Questo esempio crea un cursore che consente di controllare gli elementi quando vengono inseriti, aggiornati o sostituiti. Per ottenere i flussi di modifiche, sono necessarie la fase `$match`, la fase `$project` e l'opzione `fullDocument`. Al momento non è supportata l'individuazione delle operazioni di eliminazione tramite i flussi di modifiche. Come soluzione alternativa, è possibile aggiungere un soft marker sugli elementi da eliminare. Ad esempio, è possibile aggiungere un attributo nell'elemento denominato "deleted". Quando si vuole eliminare l'elemento, è possibile impostare "deleted" su `true` e impostare una durata TTL per l'elemento. Poiché l'aggiornamento di "deleted" su `true` è un aggiornamento, questa modifica sarà visibile nel flusso di modifiche.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="java"></a>[Java](#tab/java)

L'esempio seguente illustra come usare la funzionalità di Change Stream in Java. per l'esempio completo, vedere questo [repository GitHub](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/master/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java). In questo esempio viene inoltre illustrato come utilizzare il `resumeAfter` metodo per cercare tutte le modifiche dall'ultima lettura. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>Modifiche all'interno di una singola partizione

L'esempio seguente illustra come ottenere le modifiche agli elementi all'interno di una singola partizione. Questo esempio illustra le modifiche apportate agli elementi con una chiave di partizione uguale ad "a" e il valore della chiave di partizione uguale a "1". È possibile che client diversi leggano le modifiche da diverse partizioni in parallelo.

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

## <a name="current-limitations"></a>Limitazioni correnti

Quando si usano i flussi di modifiche, sono applicabili le limitazioni seguenti:

* Le proprietà `operationType` e `updateDescription` non sono ancora supportate nel documento di output.
* I tipi di operazioni `insert`, `update` e `replace` sono attualmente supportati. L'operazione di eliminazione o altri eventi non sono ancora supportati.

A causa di queste limitazioni, sono necessarie la fase $match, la fase $project e le opzioni fullDocument, come illustrato negli esempi precedenti.

## <a name="error-handling"></a>Gestione degli errori

Quando si usano i flussi di modifiche, sono supportati i codici e messaggi di errore seguenti:

* **Codice di errore HTTP 429**: quando il flusso di modifiche è rallentato, viene restituita una pagina vuota.

* **NamespaceNotFound (Invalidate OperationType)** : se si esegue il flusso di modifiche su una raccolta inesistente o se la raccolta viene eliminata, viene restituito un errore `NamespaceNotFound`. Poiché la proprietà `operationType` non può essere restituita nel documento di output, invece dell'errore `operationType Invalidate` viene restituito l'errore `NamespaceNotFound`.

## <a name="next-steps"></a>Passaggi successivi

* [Usare la durata per la scadenza automatica dei dati nell'API Azure Cosmos DB per MongoDB](mongodb-time-to-live.md)
* [Indicizzazione nell'API di Azure Cosmos DB per MongoDB](mongodb-indexing.md)
