---
title: Effettuare il provisioning della velocità effettiva per un contenitore in Azure Cosmos DB
description: Informazioni su come effettuare il provisioning della velocità effettiva a livello di contenitore in Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: dd47976bca75569142f1912eee06c66061e92fa6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097666"
---
# <a name="provision-throughput-for-an-azure-cosmos-db-container"></a>Effettuare il provisioning della velocità effettiva per un contenitore di Azure Cosmos DB

Questo articolo illustra come effettuare il provisioning della velocità effettiva per un contenitore (raccolta, grafo, tabella) in Azure Cosmos DB. È possibile effettuare il provisioning della velocità effettiva per un singolo contenitore oppure [effettuare il provisioning per un database](how-to-provision-database-throughput.md) e condividere la velocità effettiva tra i contenitori all'interno del database. Il provisioning della velocità effettiva per un contenitore può essere effettuato tramite il portale di Azure, l'interfaccia della riga di comando di Azure o gli SDK di COSMOS DB.

## <a name="provision-throughput-using-azure-portal"></a>Effettuare il provisioning della velocità effettiva usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova raccolta**. Compilare quindi il modulo con i dettagli seguenti:

   * Creare un nuovo database o usarne uno esistente.
   * Immettere un ID raccolta (o tabella o grafo).
   * Immettere un valore della chiave di partizione, ad esempio `/userid`.
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

![Effettuare il provisioning della velocità effettiva per un contenitore tramite l'API SQL](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Effettuare il provisioning della velocità effettiva usando l'interfaccia della riga di comando di Azure

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

Se si effettua il provisioning della velocità effettiva per un account di API MongoDB, usare '/myShardKey' per il percorso della chiave di partizione e, durante il provisioning della velocità effettiva per un account di API Cassandra, usare '/myPrimaryKey' per il percorso della chiave di partizione.

## <a name="provision-throughput-using-net-sdk"></a>Effettuare il provisioning della velocità effettiva usando .NET SDK

> [!Note]
> Usare l'API SQL per effettuare il provisioning della velocità effettiva per tutte le API ad eccezione dell'API Cassandra.

### <a id="dotnet-most"></a>API SQL, MongoDB, Gremlin e Tabella

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sul provisioning della velocità effettiva in Cosmos DB:

* [Come effettuare il provisioning della velocità effettiva per un database](how-to-provision-database-throughput.md)
* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
