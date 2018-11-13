---
title: Creare un contenitore in Azure Cosmos DB
description: Informazioni su come creare un contenitore in Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: d22103a049d62f48e8b9b9d3f71138842fbfaa89
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262489"
---
# <a name="create-a-container-in-azure-cosmos-db"></a>Creare un contenitore in Azure Cosmos DB

Questo articolo illustra i diversi modi per creare un contenitore (raccolta, tabella, grafo). Un contenitore può essere creato tramite il portale di Azure, l'interfaccia della riga di comando di Azure o gli SDK supportati. Questo articolo spiega come creare un contenitore, specificare la chiave di partizione ed effettuare il provisioning della velocità effettiva.

## <a name="create-a-container-using-azure-portal"></a>Creare un contenitore tramite il portale di Azure

### <a id="portal-sql"></a>API SQL (Core)

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova raccolta**. Compilare quindi il modulo con i dettagli seguenti:

   * Creare un nuovo database o usarne uno esistente.
   * Immettere un ID raccolta.
   * Selezionare una capacità di archiviazione **Illimitata**.
   * Immettere una chiave di partizione.
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

![Viene creata una raccolta con l'API SQL](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API MongoDB

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Cosmos DB](create-mongodb-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova raccolta**. Compilare quindi il modulo con i dettagli seguenti:

   * Creare un nuovo database o usarne uno esistente.
   * Immettere un ID raccolta.
   * Selezionare una capacità di archiviazione **Illimitata**.
   * Immettere una chiave di partizione.
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

![Viene creata una raccolta con l'API MongoDB](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API Cassandra

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova tabella**. Compilare quindi il modulo con i dettagli seguenti:

   * Creare un nuovo keyspace o usarne uno esistente.
   * Immettere un nome per la tabella.
   * Immettere le proprietà e specificare una CHIAVE PRIMARIA.
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

![Viene creata una raccolta con l'API Cassandra](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Per l'API Cassandra, la chiave primaria viene usata come chiave di partizione.

### <a id="portal-gremlin"></a>API Gremlin

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Cosmos DB](create-graph-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuovo grafo**. Compilare quindi il modulo con i dettagli seguenti:

   * Creare un nuovo database o usarne uno esistente.
   * Immettere un ID grafo.
   * Selezionare una capacità di archiviazione **Illimitata**.
   * Immettere una chiave di partizione per i vertici.
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

![Viene creata una raccolta con l'API Gremlin](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API di tabella

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Cosmos DB](create-table-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova tabella**. Compilare quindi il modulo con i dettagli seguenti:

   * Immettere un ID tabella.
   * Selezionare una capacità di archiviazione **Illimitata**.
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

![Viene creata una raccolta con l'API Tabella](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Per l'API Tabella, la chiave di partizione viene specificata ogni volta che si aggiunge una nuova riga.

## <a name="create-a-container-using-azure-cli"></a>Creare un contenitore tramite l'interfaccia della riga di comando di Azure

### <a id="cli-sql"></a>API SQL (Core)

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>API MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>API Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>API Gremlin

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>API di tabella

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-using-net-sdk"></a>Creare un contenitore tramite .NET SDK

### <a id="dotnet-sql-graph"></a>API SQL e API Gremlin

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>API MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB non prevede un concetto di unità di richieste. Per creare una nuova raccolta con velocità effettiva, usare il portale di Azure o l'API SQL, come illustrato negli esempi precedenti.

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sul partizionamento in Cosmos DB:

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
