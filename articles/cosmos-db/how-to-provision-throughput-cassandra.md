---
title: Provisioning della velocità effettiva sulle risorse Azure Cosmos DB API Cassandra
description: Informazioni su come effettuare il provisioning della velocità effettiva di contenitori, database e scalabilità automatica in Azure Cosmos DB risorse API Cassandra. Si utilizzeranno portale di Azure, l'interfaccia della riga di comando, PowerShell e vari altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 6b8b145c919a1f4e2ea9129a032da69bd30e6b71
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284121"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Provisioning di velocità effettiva di database, contenitori o scalabilità automatica in Azure Cosmos DB risorse API Cassandra

Questo articolo illustra come effettuare il provisioning della velocità effettiva in Azure Cosmos DB API Cassandra. È possibile effettuare il provisioning della velocità effettiva standard (manuale) o di scalabilità automatica in un contenitore o in un database e condividerla tra i contenitori all'interno del database. Puoi effettuare il provisioning della velocità effettiva usando portale di Azure, l'interfaccia della riga di comando di Azure o Azure Cosmos DB

Se si usa un'API diversa, vedere gli articoli API [SQL](how-to-provision-container-throughput.md), [API per MongoDB](how-to-provision-throughput-mongodb.md), [API Gremlin](how-to-provision-throughput-gremlin.md) per eseguire il provisioning della velocità effettiva.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuova tabella**. Specificare quindi i dettagli seguenti:

   * Indica se si sta creando un nuovo spazio per le nuove o se ne usa uno esistente. Selezionare l'opzione **provisioning database throughput** se si vuole eseguire il provisioning della velocità effettiva a livello di spazio.
   * Immettere l'ID tabella nel comando CQL.
   * Immettere un valore di chiave primaria (ad esempio, `/userrID` ).
   * Immettere una velocità effettiva di cui si desidera eseguire il provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Screenshot della Esplora dati quando si crea una nuova raccolta con velocità effettiva a livello di database":::

> [!Note]
> Se si effettua il provisioning della velocità effettiva in un contenitore in un account Azure Cosmos configurato con l'API Cassandra, usare `/myPrimaryKey` come percorso della chiave di partizione.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Provisioning della velocità effettiva per una tabella Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Comandi simili possono essere eseguiti tramite qualsiasi driver conforme a CQL.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Modificare o modificare la velocità effettiva per una tabella Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Comandi simili possono essere eseguiti tramite qualsiasi driver conforme a CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

È possibile usare i modelli di Azure Resource Manager per eseguire il provisioning della velocità effettiva di ridimensionamento automatico per tutte le API Azure Cosmos DB di database o a livello di contenitore Per esempi, vedere [Azure Resource Manager Templates for Azure Cosmos DB](templates-samples-cassandra.md) .

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello Azure Cosmos DB di Per esempi, vedere [esempi dell'interfaccia della riga di comando di Azure per Azure Cosmos DB](cli-samples-cassandra.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell può essere usato per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello di contenitore per tutte le API Azure Cosmos DB Per esempi, vedere [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni sul provisioning della velocità effettiva in Azure Cosmos DB:

* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)