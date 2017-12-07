---
title: Esercitazione sulla distribuzione globale in Azure Cosmos DB per l'API MongoDB | Documentazione Microsoft
description: Informazioni su come configurare la distribuzione globale in Azure Cosmos DB usando l'API MongoDB.
services: cosmos-db
keywords: distribuzione globale, MongoDB
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: a934643be02abeadecf9e8384e29bd3e0e1a36f5
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>Procedura di configurazione della distribuzione globale in Azure Cosmos DB usando l'API MongoDB

Questo articolo illustra come usare il portale di Azure per configurare la distribuzione globale in Azure Cosmos DB e quindi connettersi tramite l'API MongoDB.

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite l'[API MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>Verifica della configurazione a livello di area usando l'API MongoDB
Il modo più semplice per verificare in modo approfondito la configurazione globale nell'API per MongoDB consiste nell'eseguire il comando *isMaster()* da Mongo Shell.

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

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>Connessione a un'area preferita tramite l'API MongoDB

L'API MongoDB consente di specificare le preferenze di lettura della raccolta per un database distribuito globalmente. Per ottenere letture a bassa latenza e disponibilità elevata globale, è consigliabile impostare le preferenze di lettura della raccolta su *nearest*. Una preferenza di lettura di tipo *nearest* viene configurata per la lettura dall'area più vicina.

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
> * Configurare la distribuzione globale tramite le API di DocumentDB

È ora possibile passare all'esercitazione successiva per imparare a sviluppare in locale usando l'emulatore locale di Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Sviluppare in locale con l'emulatore](local-emulator.md)
