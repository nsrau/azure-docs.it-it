---
title: Come configurare le Scritture in più aree in Azure Cosmos DB
description: Informazioni su come configurare le Scritture in più aree per le applicazioni usando SDK diversi in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/10/2020
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, "seo-nov-2020"
ms.openlocfilehash: 6f71f4c0ec353f36614ea6dcabf4d698b31baacb
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94336727"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Configurare Scritture in più aree nelle applicazioni che usano Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dopo aver creato un account con più aree di scrittura abilitate, è necessario apportare due modifiche nell'applicazione a ConnectionPolicy per DocumentClient per abilitare le Scritture in più aree e le funzionalità multihosting in Azure Cosmos DB. In ConnectionPolicy, impostare UseMultipleWriteLocations su true e passare il nome dell'area in cui viene distribuita l'applicazione su SetCurrentLocation. Questo popolerà la proprietà PreferredLocations in base alla prossimità geografica della posizione passata. Se viene aggiunta una nuova area all'account in un secondo momento, l'applicazione non deve essere aggiornata o ridistribuita, ma rileverà automaticamente l'area più vicina ed eseguirà l'homing automatico su di essa in caso di eventi a livello di area.

> [!Note]
> Gli account Cosmos inizialmente configurati con un'area di scrittura singola possono essere configurati su più aree di scrittura con tempi di inattività pari a Per altre informazioni, consultare [Configurare più aree di scrittura](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="azure-portal"></a><a id="portal"></a> portale di Azure

Per abilitare le Scritture in più aree da portale di Azure, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Passare all'account Azure Cosmos e dal menu aprire il riquadro **replicare i dati a livello globale** .

1. Nell'opzione **Scritture** per più aree scegliere **Abilita**. Aggiunge automaticamente le aree esistenti alle aree di lettura e scrittura.

1. È possibile aggiungere altre aree selezionando le icone sulla mappa oppure selezionando il pulsante **Aggiungi area** . Per tutte le aree aggiunte sono abilitate sia la lettura che la scrittura.

1. Dopo aver aggiornato l'elenco Region, selezionare **Save (Salva** ) per applicare le modifiche.

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="Screenshot per abilitare le Scritture in più aree usando portale di Azure" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

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
* [Scegliere il livello di coerenza ottimale in Azure Cosmos DB](./consistency-levels.md)
* [Coerenza, disponibilità e compromessi delle prestazioni in Azure Cosmos DB](./consistency-levels.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](./consistency-levels.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](./request-units.md)
* [Distribuzione globale: dietro le quinte](global-dist-under-the-hood.md)