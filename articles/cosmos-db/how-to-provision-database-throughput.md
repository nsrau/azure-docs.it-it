---
title: Effettuare il provisioning della velocità effettiva per il database in Azure Cosmos DB
description: Informazioni su come effettuare il provisioning della velocità effettiva a livello di database in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 64a8bc9f4c9f5192dad22cb55cb4d2c4816d4fa5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575150"
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

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Provisioning della velocità effettiva con l'interfaccia della riga

Per creare un database con velocità effettiva condivisa, vedere.

* [Creare un database usando l'interfaccia della riga di comando di Azure](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Creare un database con PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Effettuare il provisioning della velocità effettiva usando .NET SDK

> [!Note]
> Usare gli SDK Cosmos per l'API SQL per effettuare il provisioning della velocità effettiva per tutte le API. Facoltativamente, è possibile usare l'esempio seguente anche per l'API Cassandra.

### <a id="dotnet-all"></a>Tutte le API

### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>API Cassandra
Un comando simile può essere eseguito tramite un driver conforme a CQL. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sulla velocità effettiva sottoposta a provisioning in Azure Cosmos DB:

* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Come effettuare il provisioning della velocità effettiva per un contenitore](how-to-provision-container-throughput.md)
* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
