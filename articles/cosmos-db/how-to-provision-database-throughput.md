---
title: Effettuare il provisioning della velocità effettiva per il database in Azure Cosmos DB
description: Informazioni su come effettuare il provisioning della velocità effettiva a livello di database in Azure Cosmos DB usando il portale di Azure, l'interfaccia della riga di comando, PowerShell e altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 668aa51bdb57dc4bcde0e3a95c481bb60e3d8ed3
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997371"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db"></a>Effettuare il provisioning della velocità effettiva standard (manuale) per un database in Azure Cosmos DB

Questo articolo illustra come effettuare il provisioning della velocità effettiva standard (manuale) in un database in Azure Cosmos DB. È possibile effettuare il provisioning della velocità effettiva per un singolo [contenitore](how-to-provision-container-throughput.md) oppure per un database e condividere la velocità effettiva tra i contenitori al suo interno. Per informazioni su quando usare la velocità effettiva a livello di contenitore e a livello di database, vedere l'articolo sui [casi d'uso per il provisioning della velocità effettiva in contenitori e database](set-throughput.md). È possibile effettuare il provisioning della velocità effettiva a livello di database usando il portale di Azure o gli SDK di Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Effettuare il provisioning della velocità effettiva usando il portale di Azure

### <a name="sql-core-api"></a><a id="portal-sql"></a>API SQL (Core)

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuovo database**. Specificare i dettagli seguenti:

   * Immettere un ID database.
   * Selezionare **Provisioning velocità effettiva**.
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png" alt-text="Screenshot della finestra di dialogo Nuovo database":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Effettuare il provisioning della velocità effettiva usando l'interfaccia della riga di comando di Azure o PowerShell

Per creare un database con velocità effettiva condivisa, vedere

* [Creare un database con l'interfaccia della riga di comando di Azure](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Creare un database usando PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Effettuare il provisioning della velocità effettiva usando .NET SDK

> [!Note]
> Usare gli SDK Cosmos per l'API SQL per effettuare il provisioning della velocità effettiva per tutte le API. Facoltativamente, è possibile usare l'esempio seguente anche per l'API Cassandra.

### <a name="all-apis"></a><a id="dotnet-all"></a>Tutte le API

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API Cassandra

Comandi simili possono essere eseguiti tramite qualsiasi driver conforme a CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sulla velocità effettiva sottoposta a provisioning in Azure Cosmos DB:

* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Effettuare il provisioning della velocità effettiva standard (manuale) per un contenitore](how-to-provision-container-throughput.md)
* [Come effettuare il provisioning della velocità effettiva con scalabilità automatica per un contenitore](how-to-provision-autoscale-throughput.md)
* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
