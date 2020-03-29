---
title: Effettuare il provisioning della velocità effettiva per il database in Azure Cosmos DB
description: Informazioni su come eseguire il provisioning della velocità effettiva a livello di database in Azure Cosmos DB usando il portale di Azure, l'interfaccia della riga di comando, PowerShell e vari altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: ef7d06dfb074a3453f5589284cbdaf079c48d111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933770"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Effettuare il provisioning della velocità effettiva in un database in Azure Cosmos DB

Questo articolo illustra come effettuare il provisioning della velocità effettiva in un database in Azure Cosmos DB. È possibile eseguire il provisioning della velocità effettiva per un singolo [contenitore](how-to-provision-container-throughput.md)o per un database e condividere la velocità effettiva tra i contenitori in esso contenuti. Per informazioni su quando usare la velocità effettiva a livello di contenitore e di database, vedere l'articolo Casi d'uso per il [provisioning della velocità effettiva in contenitori e database.](set-throughput.md) È possibile effettuare il provisioning della velocità effettiva a livello di database usando il portale di Azure o gli SDK di Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Effettuare il provisioning della velocità effettiva usando il portale di Azure

### <a name="sql-core-api"></a><a id="portal-sql"></a>API SQL (Core)

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuovo database**. Specificare i dettagli seguenti:

   * Immettere un ID database.
   * Selezionare **Provisioning velocità effettiva**.
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

    ![Screenshot della finestra di dialogo Nuovo database](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Effettuare il provisioning della velocità effettiva usando l'interfaccia della riga di comando di Azure o PowerShellProvision throughput using Azure CLI or PowerShell

Per creare un database con velocità effettiva condivisa, vedere,

* [Creare un database usando l'interfaccia della riga di comando di AzureCreate a database using Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Creare un database usando PowershellCreate a database using Powershell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Effettuare il provisioning della velocità effettiva usando .NET SDK

> [!Note]
> Usare gli SDK Cosmos per l'API SQL per effettuare il provisioning della velocità effettiva per tutte le API. Facoltativamente, è possibile usare l'esempio seguente anche per l'API Cassandra.

### <a name="all-apis"></a><a id="dotnet-all"></a>Tutte le API

### <a name="net-v2-sdk"></a>.NET V2 SDK

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

### <a name="net-v3-sdk"></a>.NET V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API
Comando simile può essere eseguito tramite qualsiasi driver conforme cQL. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sulla velocità effettiva sottoposta a provisioning in Azure Cosmos DB:

* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Come effettuare il provisioning della velocità effettiva per un contenitore](how-to-provision-container-throughput.md)
* [Richiedere unità e velocità effettiva in Azure Cosmos DBRequest units and throughput in Azure Cosmos DB](request-units.md)
