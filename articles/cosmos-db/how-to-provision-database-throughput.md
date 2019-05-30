---
title: Effettuare il provisioning della velocità effettiva per il database in Azure Cosmos DB
description: Informazioni su come effettuare il provisioning della velocità effettiva a livello di database in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: d73dd5ffe8cc8ed00288209b628d7175b795b335
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243761"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Effettuare il provisioning della velocità effettiva in un database in Azure Cosmos DB

Questo articolo illustra come effettuare il provisioning della velocità effettiva in un database in Azure Cosmos DB. È possibile effettuare il provisioning della velocità effettiva per un singolo [contenitore](how-to-provision-container-throughput.md) oppure per un database e condividere la velocità effettiva tra i contenitori al suo interno. Per informazioni su quando usare la velocità effettiva a livello di contenitore e a livello di database, vedere l'articolo sui [casi d'uso per il provisioning della velocità effettiva in contenitori e database](set-throughput.md). È possibile effettuare il provisioning della velocità effettiva a livello di database usando il portale di Azure o gli SDK di Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Effettuare il provisioning della velocità effettiva usando il portale di Azure

### <a id="portal-sql"></a>API SQL (Core)

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuovo database**. Specificare i dettagli seguenti:

   * Immettere un ID database. 
   * Selezionare **Provisioning velocità effettiva**.
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

![Screenshot della finestra di dialogo Nuovo database](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Effettuare il provisioning della velocità effettiva usando .NET SDK

> [!Note]
> Usare gli SDK Cosmos per l'API SQL per effettuare il provisioning della velocità effettiva per tutte le API. Facoltativamente, è possibile usare l'esempio seguente anche per l'API Cassandra.

### <a id="dotnet-all"></a>Tutte le API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sulla velocità effettiva sottoposta a provisioning in Azure Cosmos DB:

* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Come effettuare il provisioning della velocità effettiva per un contenitore](how-to-provision-container-throughput.md)
* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)