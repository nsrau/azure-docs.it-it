---
title: Come configurare funzionalità multimaster in Azure Cosmos DB
description: Informazioni su come configurare funzionalità multimaster nelle applicazioni in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: effe6fa942ce0cabace08e72dba90baf8646680e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118826"
---
# <a name="how-to-configure-multi-master-in-your-applications-in-azure-cosmos-db"></a>Come configurare funzionalità multimaster nelle applicazioni in Azure Cosmos DB

Per usare le funzionalità multimaster nelle applicazioni, è necessario abilitare le scritture in più aree nell'applicazione e configurare la funzionalità multihoming impostando l'area corrente in cui è distribuita l'applicazione.

## <a id="netv2"></a>.NET SDK v2

Per abilitare le funzionalità multimaster nelle applicazioni, impostare `UseMultipleWriteLocations` su true e configurare `SetCurrentLocation` sull'area in cui l'applicazione viene distribuita e Cosmos DB viene replicato.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3 (anteprima)

Per abilitare le funzionalità multimaster nelle applicazioni, configurare `UseCurrentRegion` sull'area in cui l'applicazione viene distribuita e Cosmos DB viene replicato.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Per abilitare le funzionalità multimaster nelle applicazioni, impostare `policy.setUsingMultipleWriteLocations(true)` su true e configurare `policy.setPreferredLocations` sull'area in cui l'applicazione viene distribuita e Cosmos DB viene replicato.

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

## <a id="javascript"></a>Node.js, JavaScript, TypeScript SDK

Per abilitare le funzionalità multimaster nelle applicazioni, impostare `connectionPolicy.UseMultipleWriteLocations` su true e configurare `connectionPolicy.PreferredLocations` sull'area in cui l'applicazione viene distribuita e Cosmos DB viene replicato.

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

## <a id="python"></a>Python SDK

Per abilitare le funzionalità multimaster nelle applicazioni, impostare `connection_policy.UseMultipleWriteLocations` su true e configurare `connection_policy.PreferredLocations` sull'area in cui l'applicazione viene distribuita e Cosmos DB viene replicato.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su multimaster, distribuzione globale e coerenza in Azure Cosmos DB. Vedere gli articoli seguenti:

* [Utilizzare i token di sessione per gestire la coerenza in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)

* [Tipi di conflitto e criteri di risoluzione in Azure Cosmos DB](conflict-resolution-policies.md)

* [Disponibilità elevata in Azure Cosmos DB](high-availability.md)

* [Scelta del livello di coerenza ottimale in Azure Cosmos DB](consistency-levels-choosing.md)

* [Compromessi tra coerenza, disponibilità e prestazioni in Azure Cosmos DB](consistency-levels-tradeoffs.md)
