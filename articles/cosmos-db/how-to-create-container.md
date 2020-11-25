---
title: Creare un contenitore nell'API Azure Cosmos DB SQL
description: Informazioni su come creare un contenitore nell'API Azure Cosmos DB SQL con portale di Azure, .NET, Java, Python, Node.js e altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006849"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Creare un contenitore nell'API Azure Cosmos DB SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo illustra le diverse modalità di creazione di un contenitore in Azure Cosmos DB API SQL. Viene illustrato come creare un contenitore usando il portale di Azure, l'interfaccia della riga di comando di Azure, PowerShell o gli SDK supportati. Questo articolo spiega come creare un contenitore, specificare la chiave di partizione ed effettuare il provisioning della velocità effettiva.

Questo articolo illustra le diverse modalità di creazione di un contenitore in Azure Cosmos DB API SQL. Se si usa un'API diversa, vedere [API per MongoDB](how-to-create-container-mongodb.md), [API Cassandra](how-to-create-container-cassandra.md), [API Gremlin](how-to-create-container-gremlin.md)ed articoli [API tabella](how-to-create-container-table.md) per creare il contenitore.

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con una combinazione diversa di maiuscole/minuscole. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/conflitti dei dati di telemetria e delle azioni sui contenitori con tali nomi.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Creare un contenitore tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuovo contenitore**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID contenitore.
   * Immettere una chiave di partizione.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Screenshot del riquadro Esplora dati con il nuovo contenitore evidenziato":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Creare un contenitore usando l'interfaccia della riga di comando di Azure

[Creare un contenitore con l'interfaccia della riga di comando di Azure](manage-with-cli.md#create-a-container) Per un elenco di tutti gli esempi dell'interfaccia della riga di comando di Azure in tutte le API di Azure Cosmos DB, vedere [esempi di Azure CLI per Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Creare un contenitore usando PowerShell

[Creare un contenitore con PowerShell](manage-with-powershell.md#create-container). Per un elenco di tutti gli esempi di PowerShell in tutte le API di Azure Cosmos DB, vedere [esempi di PowerShell](powershell-samples.md)

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Creare un contenitore tramite .NET SDK

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

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](./account-databases-containers-items.md)