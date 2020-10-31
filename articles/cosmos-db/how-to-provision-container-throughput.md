---
title: Provisioning della velocità effettiva del contenitore nell'API Azure Cosmos DB SQL
description: Informazioni su come effettuare il provisioning della velocità effettiva a livello di contenitore in Azure Cosmos DB API SQL con portale di Azure, CLI, PowerShell e vari altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100100"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Eseguire il provisioning della velocità effettiva standard (manuale) in un contenitore Azure Cosmos-API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo illustra come effettuare il provisioning della velocità effettiva standard (manuale) in un contenitore in Azure Cosmos DB API SQL. È possibile effettuare il provisioning della velocità effettiva per un singolo contenitore oppure [effettuare il provisioning della velocità effettiva per un database](how-to-provision-database-throughput.md) e condividerlo tra i contenitori all'interno del database. Il provisioning della velocità effettiva per un contenitore può essere effettuato usando il portale di Azure, l'interfaccia della riga di comando di Azure o gli SDK di Azure Cosmos DB.

Se si usa un'API diversa, vedere l'articolo relativo alle API [per MongoDB](how-to-provision-throughput-mongodb.md), [API Cassandra](how-to-provision-throughput-cassandra.md), articoli sull' [API Gremlin](how-to-provision-throughput-gremlin.md) per eseguire il provisioning della velocità effettiva.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuovo contenitore** . Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID contenitore.
   * Immettere un valore della chiave di partizione, ad esempio `/ItemID`.
   * Immettere una velocità effettiva di cui si desidera eseguire il provisioning (ad esempio, 1000 UR).
   * Selezionare **OK** .

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Screenshot del riquadro Esplora dati con l'opzione Nuova raccolta evidenziata":::

## <a name="azure-cli-or-powershell"></a>Interfaccia della riga di comando di Azure o PowerShell

Per creare un contenitore con una velocità effettiva dedicata, vedere:

* [Creare un contenitore tramite l'interfaccia della riga di comando di Azure](manage-with-cli.md#create-a-container)
* [Creare un contenitore usando PowerShell](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Usare Cosmos SDK per l'API SQL per eseguire il provisioning della velocità effettiva per tutte le API di Cosmos DB, ad eccezione di Cassandra e API MongoDB.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni sul provisioning della velocità effettiva in Azure Cosmos DB:

* [Effettuare il provisioning della velocità effettiva standard (manuale) in un database](how-to-provision-database-throughput.md)
* [Effettuare il provisioning della velocità effettiva con scalabilità automatica in un database](how-to-provision-autoscale-throughput.md)
* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
