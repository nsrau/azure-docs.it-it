---
title: Esercitazione sulla distribuzione globale con l'API di Azure Cosmos DB per MongoDB
description: Informazioni su come configurare la distribuzione globale usando l'API di Azure Cosmos DB per MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 5a9c618fdcf2e904a2a3c03b3ae6b5477cd47c4e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754785"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Configurare un database distribuito a livello globale usando l'API di Azure Cosmos DB per MongoDB

Questo articolo illustra come usare il portale di Azure per configurare un database distribuito a livello globale e quindi connettersi a esso usando l'API di Azure Cosmos DB per MongoDB.

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale usando l'[API di Azure Cosmos DB per MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Verifica della configurazione a livello di area 
Un modo semplice per verificare la configurazione globale con l'API di Cosmos DB per MongoDB consiste nell'eseguire il comando *isMaster()* da Mongo Shell.

Da Mongo Shell:

   ```
      db.isMaster()
   ```
   
Risultati dell'esempio:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Connessione a un'area preferita 

L'API di Azure Cosmos DB per MongoDB consente di specificare le preferenze di lettura della raccolta per un database distribuito globalmente. Per ottenere letture a bassa latenza e disponibilità elevata globale, è consigliabile impostare le preferenze di lettura della raccolta su *nearest*. Una preferenza di lettura di tipo *nearest* viene configurata per la lettura dall'area più vicina.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Per applicazioni con un'area di lettura/scrittura primaria e un'area secondaria per scenari di ripristino di emergenza, è consigliabile impostare le preferenze di lettura della raccolta su *secondary preferred*. Una preferenza di lettura di tipo *secondary preferred* viene configurata per la lettura dall'area secondaria quando l'area primaria non è disponibile.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Se infine si vogliono specificare manualmente le aree di lettura, è possibile impostare il valore Tag per l'area entro la preferenza di lettura.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

L'esercitazione è terminata. Per informazioni su come gestire la coerenza dell'account con replica globale, vedere [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md). Per informazioni sul funzionamento della replica di database globale in Azure Cosmos DB, vedere [Distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale usando l'API di Cosmos DB per MongoDB

È ora possibile passare all'esercitazione successiva per imparare a sviluppare in locale usando l'emulatore locale di Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Sviluppare in locale con l'emulatore di Azure Cosmos DB](local-emulator.md)
