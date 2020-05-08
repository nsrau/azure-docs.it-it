---
title: Modificare i flussi nell'API Azure Cosmos DB per MongoDB
description: Informazioni su come usare i flussi delle modifiche nell'API Azure Cosmos DB per MongoDB per ottenere le modifiche apportate ai dati.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 7a6060448175530ada5ba95ceda470056a7be002
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872150"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Modificare i flussi nell'API Azure Cosmos DB per MongoDB

Il supporto del [feed delle modifiche](change-feed.md) nell'API Azure Cosmos DB per MongoDB è disponibile tramite l'API Change Streams. Usando l'API Change Streams, le applicazioni possono ottenere le modifiche apportate alla raccolta o agli elementi in una singola partizione. In seguito è possibile eseguire ulteriori azioni in base ai risultati. Le modifiche apportate agli elementi nella raccolta vengono acquisite nell'ordine in cui sono state modificate e il tipo di ordinamento è garantito per ogni chiave di partizione.

> [!NOTE]
> Per usare i flussi delle modifiche, creare l'account con la versione 3,6 dell'API Azure Cosmos DB per MongoDB o una versione successiva. Se si eseguono gli esempi del flusso di modifiche rispetto a una versione precedente, è `Unrecognized pipeline stage name: $changeStream` possibile che venga visualizzato l'errore.

## <a name="current-limitations"></a>Limitazioni correnti

Quando si usano i flussi di modifiche, si applicano le limitazioni seguenti:

* Le `operationType` proprietà `updateDescription` e non sono ancora supportate nel documento di output.
* I `insert`tipi `update`di operazioni `replace` , e sono attualmente supportati. 
* L'operazione di eliminazione o altri eventi non sono ancora supportati.

A causa di queste limitazioni, le opzioni $match stage, $project stage e fullDocument sono obbligatorie, come illustrato negli esempi precedenti.

A differenza del feed delle modifiche nell'API SQL di Azure Cosmos DB, non è disponibile una [libreria del processore dei feed delle modifiche](change-feed-processor.md) separata per utilizzare i flussi di modifica o la necessità di un contenitore lease. Attualmente non è disponibile il supporto per i [trigger di funzioni di Azure](change-feed-functions.md) per elaborare i flussi delle modifiche.

## <a name="error-handling"></a>Gestione degli errori

Quando si usano i flussi di modifiche, sono supportati i codici di errore e i messaggi seguenti:

* **Codice di errore HTTP 16500** : quando il flusso di modifiche è limitato, viene restituita una pagina vuota.

* **NamespaceNotFound (OperationType Invalidate)** : se si esegue Change Stream sulla raccolta che non esiste o se la raccolta viene eliminata, viene restituito un `NamespaceNotFound` errore. Poiché la `operationType` proprietà non può essere restituita nel documento di output, anziché `operationType Invalidate` l'errore, `NamespaceNotFound` viene restituito l'errore.

## <a name="examples"></a>Esempi

Nell'esempio seguente viene illustrato come ottenere i flussi delle modifiche in tutti gli elementi della raccolta. Questo esempio crea un cursore per controllare gli elementi quando vengono inseriti, aggiornati o sostituiti. La `$match` fase, `$project` la fase e `fullDocument` l'opzione sono necessarie per ottenere i flussi delle modifiche. Il monitoraggio delle operazioni di eliminazione mediante flussi di modifiche non è attualmente supportato. Come soluzione alternativa, è possibile aggiungere un marcatore Soft sugli elementi da eliminare. Ad esempio, è possibile aggiungere un attributo nell'elemento denominato "Deleted". Quando si desidera eliminare l'elemento, è possibile impostare "eliminato" su e impostare `true` una durata (TTL) per l'elemento. Poiché l'aggiornamento di "Deleted `true` " a è un aggiornamento, questa modifica sarà visibile nel flusso di modifiche.

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

## <a name="changes-within-a-single-shard"></a>Modifiche all'interno di una singola partizione

Nell'esempio seguente viene illustrato come ottenere le modifiche agli elementi all'interno di una singola partizione. Questo esempio Mostra come ottenere le modifiche apportate agli elementi con una chiave di partizione uguale a "a" e il valore della chiave di partizione uguale a "1". È possibile che client diversi leggano le modifiche da diverse partizioni in parallelo.

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

* [Usare la durata (TTL) per la scadenza automatica dei dati nell'API Azure Cosmos DB per MongoDB](mongodb-time-to-live.md)
* [Indicizzazione nell'API di Azure Cosmos DB per MongoDB](mongodb-indexing.md)
