---
title: Creare un contenitore in Azure Cosmos DB
description: Informazioni su come creare un contenitore in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/17/2019
ms.author: rimman
ms.openlocfilehash: c075a801a877309709258dd6466e68e46d802eff
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680435"
---
# <a name="create-an-azure-cosmos-container"></a>Creare un contenitore in Azure Cosmos

Questo articolo illustra i diversi modi disponibili per creare un contenitore di Azure Cosmos (raccolta, tabella o grafo). È possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure o gli SDK supportati. Questo articolo spiega come creare un contenitore, specificare la chiave di partizione ed effettuare il provisioning della velocità effettiva.

## <a name="create-a-container-using-azure-portal"></a>Creare un contenitore tramite il portale di Azure

### <a id="portal-sql"></a>API SQL

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova raccolta**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID raccolta.
   * Immettere una chiave di partizione.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

![Screenshot del riquadro Esplora dati con l'opzione Nuova raccolta evidenziata](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API Azure Cosmos DB per MongoDB

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova raccolta**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID raccolta.
   * Immettere una chiave di partizione.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

![Screenshot dell'API Azure Cosmos DB per MongoDB con la finestra di dialogo Aggiungi raccolta](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API Cassandra

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova tabella**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo keyspace o usarne uno esistente.
   * Immettere un nome per la tabella.
   * Immettere le proprietà e specificare una chiave primaria.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

![Screenshot dell'API Cassandra con la finestra di dialogo Aggiungi tabella](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Per l'API Cassandra, la chiave primaria viene usata come chiave di partizione.

### <a id="portal-gremlin"></a>API Gremlin

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-graph-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuovo grafo**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID grafo.
   * Selezionare una capacità di archiviazione **Illimitata**.
   * Immettere una chiave di partizione per i vertici.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

![Screenshot dell'API Gremlin con la finestra di dialogo Aggiungi Graph](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API di tabella

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-table-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova tabella**. Specificare quindi i dettagli seguenti:

   * Immettere un ID tabella.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

![Screenshot dell'API Tabella con la finestra di dialogo Aggiungi tabella](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Per l'API Tabella, la chiave di partizione viene specificata ogni volta che si aggiunge una nuova riga.

## <a name="create-a-container-using-azure-cli"></a>Creare un contenitore tramite l'interfaccia della riga di comando di Azure

### <a id="cli-sql"></a>API SQL

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

### <a id="cli-mongodb"></a>API Azure Cosmos DB per MongoDB

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

### <a id="dotnet-mongodb"></a>API Azure Cosmos DB per MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Il protocollo di collegamento MongoDB non comprende il concetto di [unità richiesta](request-units.md). Per creare una nuova raccolta con velocità effettiva con provisioning, usare il portale di Azure o gli SDK di Cosmos DB per l'API SQL.

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Passaggi successivi

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
- [Unità richiesta in Azure Cosmos DB](request-units.md)
- [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
- [Usare l'account Azure Cosmos](account-overview.md)
