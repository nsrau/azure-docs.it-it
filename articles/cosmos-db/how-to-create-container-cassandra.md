---
title: Creazione di un contenitore in Azure Cosmos DB API Cassandra
description: Informazioni su come creare un contenitore in Azure Cosmos DB API Cassandra tramite portale di Azure, .NET, Java, Python, Node.js e altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 01030a563c15eee7786058c2eae30d23803dfe42
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101647"
---
# <a name="create-a-container-in-azure-cosmos-db-cassandra-api"></a>Creazione di un contenitore in Azure Cosmos DB API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Questo articolo illustra le diverse modalità di creazione di un contenitore in Azure Cosmos DB API Cassandra. Viene illustrato come creare un contenitore usando portale di Azure, l'interfaccia della riga di comando di Azure, PowerShell o gli SDK supportati. Questo articolo spiega come creare un contenitore, specificare la chiave di partizione ed effettuare il provisioning della velocità effettiva.

Questo articolo illustra le diverse modalità di creazione di un contenitore in Azure Cosmos DB API Cassandra. Se si usa un'API diversa, vedere gli articoli API [per MongoDB](how-to-create-container-mongodb.md), [api Gremlin](how-to-create-container-gremlin.md), [API tabella](how-to-create-container-table.md)e [API SQL](how-to-create-container.md) per creare il contenitore.

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con una combinazione diversa di maiuscole/minuscole. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/conflitti dei dati di telemetria e delle azioni sui contenitori con tali nomi.

## <a name="create-using-azure-portal"></a><a id="portal-cassandra"></a>Creare usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)oppure selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuova tabella** . Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo keyspace o usarne uno esistente.
   * Immettere un nome per la tabella.
   * Immettere le proprietà e specificare una chiave primaria.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK** .

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Screenshot dell'API Cassandra con la finestra di dialogo Aggiungi tabella":::

> [!NOTE]
> Per l'API Cassandra, la chiave primaria viene usata come chiave di partizione.

## <a name="create-using-net-sdk"></a><a id="dotnet-cassandra"></a>Creare con .NET SDK

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

Se si verifica un'eccezione di timeout durante la creazione di una raccolta, eseguire un'operazione di lettura per verificare se la raccolta è stata creata correttamente. L'operazione di lettura genera un'eccezione fino al completamento dell'operazione di creazione della raccolta. Per l'elenco dei codici di stato supportati dall'operazione di creazione, vedere l'articolo relativo ai [codici di stato HTTP per Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Creare usando l'interfaccia della riga di comando

[Creare una tabella Cassandra con l'interfaccia della riga di comando di Azure](./scripts/cli/cassandra/create.md). Per un elenco di tutti gli esempi dell'interfaccia della riga di comando di Azure in tutte le API di Azure Cosmos DB, vedere [esempi di Azure CLI per Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Creare usando PowerShell

[Creare una tabella Cassandra con PowerShell](./scripts/powershell/cassandra/create.md). Per un elenco di tutti gli esempi di PowerShell in tutte le API di Azure Cosmos DB, vedere [esempi di PowerShell](powershell-samples.md)

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](./account-databases-containers-items.md)