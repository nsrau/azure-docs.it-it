---
title: Come configurare funzionalità multimaster in Azure Cosmos DB
description: Informazioni su come configurare funzionalità multimaster nelle applicazioni in Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: 86f5d64391dd5312d8c51a5b639b790e62b6710d
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560249"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Configurare funzionalità multimaster nelle applicazioni che usano Azure Cosmos DB

Per usare le funzionalità multimaster nelle applicazioni, è necessario abilitare le scritture in più aree e configurare la funzionalità di multihoming in Azure Cosmos DB. Per configurare il multihoming, impostare l'area in cui viene distribuita l'applicazione.

## <a id="netv2"></a>.NET SDK v2

Per abilitare le funzionalità multimaster nell'applicazione, impostare `UseMultipleWriteLocations` su `true`. Inoltre, impostare `SetCurrentLocation` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Azure Cosmos DB:

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

Per abilitare le funzionalità multimaster nelle applicazioni, impostare `UseCurrentRegion` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB:

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Per abilitare le funzionalità multimaster nelle applicazioni, impostare `policy.setUsingMultipleWriteLocations(true)` e `policy.setPreferredLocations` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB:

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

## <a id="javascript"></a>Node.js, JavaScript e TypeScript SDK

Per abilitare le funzionalità multimaster nell'applicazione, impostare `connectionPolicy.UseMultipleWriteLocations` su `true`. Inoltre, impostare `connectionPolicy.PreferredLocations` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB:

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

Per abilitare le funzionalità multimaster nell'applicazione, impostare `connection_policy.UseMultipleWriteLocations` su `true`. Inoltre, impostare `connection_policy.PreferredLocations` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Passaggi successivi

Leggere gli articoli seguenti:

* [Usare token di sessione per gestire la coerenza in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipi di conflitto e criteri di risoluzione in Azure Cosmos DB](conflict-resolution-policies.md)
* [Disponibilità elevata in Azure Cosmos DB](high-availability.md)
* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Scegliere il livello di coerenza ottimale in Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromessi tra coerenza, disponibilità e prestazioni in Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Distribuzione globale: dietro le quinte](global-dist-under-the-hood.md)
