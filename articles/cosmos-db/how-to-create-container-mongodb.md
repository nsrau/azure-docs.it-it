---
title: Creare un contenitore nell'API Azure Cosmos DB per MongoDB
description: Informazioni su come creare un contenitore nell'API Azure Cosmos DB per MongoDB usando portale di Azure, .NET, Java, Node.js e altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 0fa7f122c5a9957db0800d2ccf4e5c1f8effd574
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491173"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Creare un contenitore nell'API Azure Cosmos DB per MongoDB

Questo articolo illustra i diversi modi per creare un contenitore nell'API Azure Cosmos DB per MongoDB. Viene illustrato come creare un contenitore usando portale di Azure, l'interfaccia della riga di comando di Azure, PowerShell o gli SDK supportati. Questo articolo spiega come creare un contenitore, specificare la chiave di partizione ed effettuare il provisioning della velocità effettiva.

Questo articolo illustra i diversi modi per creare un contenitore nell'API Azure Cosmos DB per MongoDB. Se si usa un'API diversa, vedere l'API [SQL](how-to-create-container.md), [API Cassandra](how-to-create-container-cassandra.md), l' [api Gremlin](how-to-create-container-gremlin.md)e gli articoli [API tabella](how-to-create-container-table.md) per creare il contenitore.

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con una combinazione diversa di maiuscole/minuscole. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/conflitti dei dati di telemetria e delle azioni sui contenitori con tali nomi.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Creare usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)oppure selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuovo contenitore**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID contenitore.
   * Immettere una chiave di partizione.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Screenshot dell'API Azure Cosmos DB per MongoDB, finestra di dialogo Aggiungi contenitore":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Creare con .NET SDK

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Il protocollo di collegamento MongoDB non comprende il concetto di [unità richiesta](request-units.md). Per creare una nuova raccolta con velocità effettiva con provisioning, usare il portale di Azure o gli SDK di Cosmos DB per l'API SQL.

Se si verifica un'eccezione di timeout durante la creazione di una raccolta, eseguire un'operazione di lettura per verificare se la raccolta è stata creata correttamente. L'operazione di lettura genera un'eccezione fino al completamento dell'operazione di creazione della raccolta. Per l'elenco dei codici di stato supportati dall'operazione di creazione, vedere l'articolo relativo ai [codici di stato HTTP per Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Creare usando l'interfaccia della riga di comando

[Creare una raccolta per Azure Cosmos DB API MongoDB con l'interfaccia della](./scripts/cli/mongodb/create.md)riga di comando di Azure. Per un elenco di tutti gli esempi dell'interfaccia della riga di comando di Azure in tutte le API di Azure Cosmos DB, vedere [esempi di Azure CLI per Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Creare usando PowerShell

[Creare una raccolta per Azure Cosmos DB API MongoDB con PowerShell](./scripts/powershell/mongodb/create.md). Per un elenco di tutti gli esempi di PowerShell in tutte le API di Azure Cosmos DB, vedere [esempi di PowerShell](powershell-samples.md)

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Creare un contenitore usando modelli di Azure Resource Manager

[Creare una raccolta per Azure Cosmos DB API MongoDB con gestione risorse modello](./manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](./account-databases-containers-items.md)