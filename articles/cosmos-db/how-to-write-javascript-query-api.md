---
title: Come scrivere stored procedure e trigger usando l'API di query di Javascript in Azure Cosmos DB
description: Informazioni su come scrivere stored procedure e trigger usando l'API di query di Javascript in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: f465ac91936b766d2c19ea8efd67b3acc8df6d75
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243939"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Come scrivere stored procedure e trigger in Azure Cosmos DB usando l'API di query di Javascript

Azure Cosmos DB consente di eseguire query ottimizzate usando un'interfaccia JavaScript intuitiva senza alcuna conoscenza di linguaggio SQL, che possa essere usata per scrivere stored procedure o trigger. Per altre informazioni sul supporto delle API di query JavaScript in Azure Cosmos DB, vedere l'articolo [Utilizzo dell'API di query integrata nel linguaggio JavaScript in Azure Cosmos DB](javascript-query-api.md).

## <a id="stored-procedures"></a>Stored procedure usando l'API di query JavaScript

L'esempio di codice seguente illustra come si usa l'API Query JavaScript nel contesto di una stored procedure. La stored procedure inserisce un elemento di Azure Cosmos DB specificato da un parametro di input, e aggiorna un documento di metadati usando il metodo `__.filter()`, con minSize, maxSize e totalSize in base alla proprietà relativa alle dimensioni dell'elemento di input.

> [!NOTE]
> `__` (doppio carattere di sottolineatura) è un alias per `getContext().getCollection()` quando si usa l'API di query JavaScript.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per ottenere informazioni su stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB:

* [Come usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Come registrare e usare stored procedure in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Come registrare e usare [pre-trigger](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [post-trigger](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) in Azure Cosmos DB

* [Come registrare e usare funzioni definite dall'utente in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Synthetic partition keys in Azure Cosmos DB](synthetic-partition-keys.md) (Chiavi di partizione sintetiche in Azure Cosmos DB)
