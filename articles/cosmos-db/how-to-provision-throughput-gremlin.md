---
title: Provisioning della velocità effettiva in Azure Cosmos DB risorse API Gremlin
description: Informazioni su come effettuare il provisioning della velocità effettiva di contenitori, database e scalabilità automatica in Azure Cosmos DB risorse API Gremlin. Si utilizzeranno portale di Azure, l'interfaccia della riga di comando, PowerShell e vari altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 70eecc7843867a5832d962b7efaecda1b6ab4ae4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284004"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Provisioning di velocità effettiva di database, contenitori o scalabilità automatica in Azure Cosmos DB risorse API Gremlin

Questo articolo illustra come effettuare il provisioning della velocità effettiva in Azure Cosmos DB API Gremlin. È possibile effettuare il provisioning della velocità effettiva standard (manuale) o di scalabilità automatica in un contenitore o in un database e condividerla tra i contenitori all'interno del database. Puoi effettuare il provisioning della velocità effettiva usando portale di Azure, l'interfaccia della riga di comando di Azure o Azure Cosmos DB

Se si usa un'API diversa, vedere gli articoli API [SQL](how-to-provision-container-throughput.md), [API Cassandra](how-to-provision-throughput-cassandra.md), [API per MongoDB](how-to-provision-throughput-mongodb.md) per eseguire il provisioning della velocità effettiva.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuovo grafico**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente. Selezionare l'opzione **provisioning database throughput** se si desidera effettuare il provisioning della velocità effettiva a livello di database.
   * Immettere un ID grafo.
   * Immettere un valore della chiave di partizione, ad esempio `/ItemID`.
   * Immettere una velocità effettiva di cui si desidera eseguire il provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Screenshot di Esplora dati, durante la creazione di un nuovo grafico con velocità effettiva a livello di database":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Usare Cosmos SDK per l'API SQL per eseguire il provisioning della velocità effettiva per tutte le API di Azure Cosmos DB, ad eccezione di Cassandra e API MongoDB.

### <a name="provision-container-level-throughput"></a>Provisioning a livello di contenitore velocità effettiva

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

### <a name="provision-database-level-throughput"></a>Provisioning velocità effettiva a livello di database

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

È possibile usare i modelli di Azure Resource Manager per eseguire il provisioning della velocità effettiva di ridimensionamento automatico per tutte le API Azure Cosmos DB di database o a livello di contenitore Per esempi, vedere [Azure Resource Manager Templates for Azure Cosmos DB](templates-samples-gremlin.md) .

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello Azure Cosmos DB di Per esempi, vedere [esempi dell'interfaccia della riga di comando di Azure per Azure Cosmos DB](cli-samples-gremlin.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell può essere usato per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello di contenitore per tutte le API Azure Cosmos DB Per esempi, vedere [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni sul provisioning della velocità effettiva in Azure Cosmos DB:

* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)