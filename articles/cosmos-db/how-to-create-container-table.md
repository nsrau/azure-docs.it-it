---
title: Creazione di un contenitore in Azure Cosmos DB API Tabella
description: Informazioni su come creare un contenitore in Azure Cosmos DB API Tabella tramite portale di Azure, .NET, Java, Python, Node.js e altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: ccda92f094d28b27e48de689c3b39c4f8a9bfaa3
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284136"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Creazione di un contenitore in Azure Cosmos DB API Tabella

Questo articolo illustra le diverse modalità di creazione di un contenitore in Azure Cosmos DB API Tabella. Viene illustrato come creare un contenitore usando portale di Azure, l'interfaccia della riga di comando di Azure, PowerShell o gli SDK supportati. Questo articolo spiega come creare un contenitore, specificare la chiave di partizione ed effettuare il provisioning della velocità effettiva.

Questo articolo illustra le diverse modalità di creazione di un contenitore in Azure Cosmos DB API Tabella. Se si usa un'API diversa, vedere gli articoli [API per MongoDB](how-to-create-container-mongodb.md), [API Cassandra](how-to-create-container-cassandra.md), [API Gremlin](how-to-create-container-gremlin.md)e [API SQL](how-to-create-container.md) per creare il contenitore.

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con una combinazione diversa di maiuscole/minuscole. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/conflitti dei dati di telemetria e delle azioni sui contenitori con tali nomi.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Creare usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-table-dotnet.md#create-a-database-account)oppure selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuova tabella**. Specificare quindi i dettagli seguenti:

   * Immettere un ID tabella.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Screenshot dell'API Tabella con la finestra di dialogo Aggiungi tabella":::

> [!Note]
> Per l'API Tabella, la chiave di partizione viene specificata ogni volta che si aggiunge una nuova riga.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Creare usando l'interfaccia della riga di comando

[Creare una tabella API tabella con l'interfaccia della riga di comando di Azure](./scripts/cli/table/create.md). Per un elenco di tutti gli esempi dell'interfaccia della riga di comando di Azure in tutte le API di Azure Cosmos DB, vedere [esempi di Azure CLI per Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Creare usando PowerShell

[Creare una tabella API tabella con PowerShell](./scripts/powershell/table/create.md). Per un elenco di tutti gli esempi di PowerShell in tutte le API di Azure Cosmos DB, vedere [esempi di PowerShell](powershell-samples.md)

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](account-overview.md)

