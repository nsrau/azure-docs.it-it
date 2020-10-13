---
title: Come configurare le Scritture in più aree in Azure Cosmos DB
description: Informazioni su come configurare le Scritture in più aree per le applicazioni usando SDK diversi in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/10/2020
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8079fb3ab04d5f613566816735491203d7df951a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570672"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Configurare Scritture in più aree nelle applicazioni che usano Azure Cosmos DB

Dopo aver creato un account con più aree di scrittura abilitate, è necessario apportare due modifiche nell'applicazione a ConnectionPolicy per DocumentClient per abilitare le Scritture in più aree e le funzionalità multihosting in Azure Cosmos DB. In ConnectionPolicy, impostare UseMultipleWriteLocations su true e passare il nome dell'area in cui viene distribuita l'applicazione su SetCurrentLocation. Questo popolerà la proprietà PreferredLocations in base alla prossimità geografica della posizione passata. Se viene aggiunta una nuova area all'account in un secondo momento, l'applicazione non deve essere aggiornata o ridistribuita, ma rileverà automaticamente l'area più vicina ed eseguirà l'homing automatico su di essa in caso di eventi a livello di area.

> [!Note]
> Gli account Cosmos inizialmente configurati con un'area di scrittura singola possono essere configurati su più aree di scrittura con tempi di inattività pari a Per altre informazioni, consultare [Configurare più aree di scrittura](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Per abilitare le Scritture in più aree dell'applicazione, impostare `UseMultipleWriteLocations` su `true` . Inoltre, impostare `SetCurrentLocation` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Azure Cosmos DB:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

Per abilitare le Scritture in più aree dell'applicazione, impostare sull' `ApplicationRegion` area in cui viene distribuita l'applicazione e dove Cosmos DB viene replicata:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Facoltativamente, è possibile usare `CosmosClientBuilder` e `WithApplicationRegion` per ottenere lo stesso risultato:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Java V4 SDK

Per abilitare le Scritture in più aree nell'applicazione, chiamare `.multipleWriteRegionsEnabled(true)` e `.preferredRegions(preferredRegions)` nel generatore client, dove `preferredRegions` è un oggetto `List` che contiene un elemento, ovvero l'area in cui viene distribuita l'applicazione e dove Cosmos DB viene replicata:

# <a name="async"></a>[Asincrona](#tab/api-async)

   API asincrona [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Sincronizza](#tab/api-sync)

   API sincrona [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> SDK Java V2 asincrono

Java V2 SDK ha usato l'oggetto Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Per abilitare le Scritture in più aree dell'applicazione, impostare `policy.setUsingMultipleWriteLocations(true)` e impostare `policy.setPreferredLocations` sull'area in cui viene distribuita l'applicazione e dove Cosmos DB viene replicata:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript e TypeScript SDK

Per abilitare le Scritture in più aree dell'applicazione, impostare `connectionPolicy.UseMultipleWriteLocations` su `true` . Inoltre, impostare `connectionPolicy.PreferredLocations` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Per abilitare le Scritture in più aree dell'applicazione, impostare `connection_policy.UseMultipleWriteLocations` su `true` . Inoltre, impostare `connection_policy.PreferredLocations` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Passaggi successivi

Leggere gli articoli seguenti:

* [Usare token di sessione per gestire la coerenza in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipi di conflitto e criteri di risoluzione in Azure Cosmos DB](conflict-resolution-policies.md)
* [Disponibilità elevata in Azure Cosmos DB](high-availability.md)
* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Scegliere il livello di coerenza ottimale in Azure Cosmos DB](consistency-levels-choosing.md)
* [Coerenza, disponibilità e compromessi delle prestazioni in Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Distribuzione globale: dietro le quinte](global-dist-under-the-hood.md)
