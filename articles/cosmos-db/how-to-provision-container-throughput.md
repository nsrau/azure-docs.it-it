---
title: Effettuare il provisioning della velocità effettiva per un contenitore in Azure Cosmos DB
description: Informazioni su come effettuare il provisioning della velocità effettiva a livello di contenitore in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: f195eaa0f5d22160de8c1e9e2f429073de001828
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986018"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos

Questo articolo illustra come effettuare il provisioning della velocità effettiva per un contenitore (raccolta, grafo o tabella) in Azure Cosmos DB. È possibile effettuare il provisioning della velocità effettiva per un singolo contenitore oppure [effettuare il provisioning della velocità effettiva per un database](how-to-provision-database-throughput.md) e condividerlo tra i contenitori all'interno del database. Il provisioning della velocità effettiva per un contenitore può essere effettuato usando il portale di Azure, l'interfaccia della riga di comando di Azure o gli SDK di Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Effettuare il provisioning della velocità effettiva usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova raccolta**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID contenitore (o Table o Graph).
   * Immettere un valore della chiave di partizione, ad esempio `/userid`.
   * Immettere una velocità effettiva di cui si desidera eseguire il provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

![Screenshot del riquadro Esplora dati con l'opzione Nuova raccolta evidenziata](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Effettuare il provisioning della velocità effettiva usando l'interfaccia della riga di comando di Azure

```azurecli-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

## <a name="provision-throughput-using-powershell"></a>Effettuare il provisioning della velocità effettiva usando PowerShell

```azurepowershell-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

Se si effettua il provisioning della velocità effettiva in un contenitore in un account Azure Cosmos configurato con l'API Azure Cosmos DB per MongoDB, usare `/myShardKey` come percorso della chiave di partizione. Se si effettua il provisioning della velocità effettiva in un contenitore in un account Azure Cosmos configurato con l'API Cassandra, usare `/myPrimaryKey` come percorso della chiave di partizione.

## <a name="provision-throughput-by-using-net-sdk"></a>Effettuare il provisioning della velocità effettiva usando .NET SDK

> [!Note]
> Usare l'API SQL di Cosmos SDK per effettuare il provisioning della velocità effettiva per tutte le API Cosmos DB ad eccezione dell'API Cassandra.

### <a id="dotnet-most"></a>API SQL, MongoDB, Gremlin e Tabella
### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni sul provisioning della velocità effettiva in Azure Cosmos DB:

* [Come effettuare il provisioning della velocità effettiva per un database](how-to-provision-database-throughput.md)
* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
