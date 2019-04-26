---
title: Come configurare funzionalità multimaster in Azure Cosmos DB
description: Informazioni su come configurare funzionalità multimaster nelle applicazioni in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: b862c59002369662d37b6d6a9de28370b0000497
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682271"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Come configurare funzionalità multimaster nelle applicazioni che usano Azure Cosmos DB

Per usare le funzionalità multimaster nelle applicazioni, è necessario abilitare le scritture in più aree e configurare la funzionalità di multihoming in Azure Cosmos DB. Il multihoming viene configurato impostando la regione in cui viene distribuita l'applicazione.

## <a id="netv2"></a>.NET SDK v2

Per abilitare le funzionalità multimaster nelle applicazioni, impostare `UseMultipleWriteLocations` su true e configurare `SetCurrentLocation` sull'area in cui l'applicazione viene distribuita e Azure Cosmos DB viene replicato.

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

Successivamente si possono leggere gli articoli seguenti:

* [Utilizzare i token di sessione per gestire la coerenza in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipi di conflitto e criteri di risoluzione in Azure Cosmos DB](conflict-resolution-policies.md)
* [Disponibilità elevata in Azure Cosmos DB](high-availability.md)
* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Scelta del livello di coerenza ottimale in Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromessi tra coerenza, disponibilità e prestazioni in Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
