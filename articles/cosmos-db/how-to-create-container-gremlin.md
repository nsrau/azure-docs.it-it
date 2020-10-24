---
title: Creare un contenitore in Azure Cosmos DB API Gremlin
description: Informazioni su come creare un contenitore in Azure Cosmos DB API Gremlin usando portale di Azure, .NET e altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 55dabccf5a5ab9e81ff0644056002f3beb644fa9
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491156"
---
# <a name="create-a-container-in-azure-cosmos-db-gremlin-api"></a>Creare un contenitore in Azure Cosmos DB API Gremlin

Questo articolo illustra i diversi modi per creare un contenitore in Azure Cosmos DB API Gremlin. Viene illustrato come creare un contenitore usando portale di Azure, l'interfaccia della riga di comando di Azure, PowerShell o gli SDK supportati. Questo articolo spiega come creare un contenitore, specificare la chiave di partizione ed effettuare il provisioning della velocità effettiva.

Questo articolo illustra i diversi modi per creare un contenitore in Azure Cosmos DB API Gremlin. Se si usa un'API diversa, vedere gli articoli [API per MongoDB](how-to-create-container-mongodb.md), [API Cassandra](how-to-create-container-cassandra.md), [API tabella](how-to-create-container-table.md)e [API SQL](how-to-create-container.md) per creare il contenitore.

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con una combinazione diversa di maiuscole/minuscole. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/conflitti dei dati di telemetria e delle azioni sui contenitori con tali nomi.

## <a name="create-using-azure-portal"></a><a id="portal-gremlin"></a>Creare usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-graph-dotnet.md#create-a-database-account)oppure selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuovo grafico**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID grafo.
   * Selezionare una capacità di archiviazione **Illimitata**.
   * Immettere una chiave di partizione per i vertici.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Screenshot dell'API Gremlin con la finestra di dialogo Aggiungi Graph":::

## <a name="create-using-net-sdk"></a><a id="dotnet-sql-graph"></a>Creare con .NET SDK

Se si verifica un'eccezione di timeout durante la creazione di una raccolta, eseguire un'operazione di lettura per verificare se la raccolta è stata creata correttamente. L'operazione di lettura genera un'eccezione fino al completamento dell'operazione di creazione della raccolta. Per l'elenco dei codici di stato supportati dall'operazione di creazione, vedere l'articolo relativo ai [codici di stato HTTP per Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

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

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Creare usando l'interfaccia della riga di comando

[Creare un grafo Gremlin con l'interfaccia della riga di comando di Azure](./scripts/cli/gremlin/create.md). Per un elenco di tutti gli esempi dell'interfaccia della riga di comando di Azure in tutte le API di Azure Cosmos DB, vedere [esempi di Azure CLI per Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Creare usando PowerShell

[Creare un grafo Gremlin con PowerShell](./scripts/powershell/gremlin/create.md). Per un elenco di tutti gli esempi di PowerShell in tutte le API di Azure Cosmos DB, vedere [esempi di PowerShell](powershell-samples.md)

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](./account-databases-containers-items.md)