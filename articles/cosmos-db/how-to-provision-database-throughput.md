---
title: Provisioning della velocità effettiva del database in Azure Cosmos DB API SQL
description: Informazioni su come effettuare il provisioning della velocità effettiva a livello di database in Azure Cosmos DB API SQL con portale di Azure, CLI, PowerShell e vari altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 347da75d8e3f58d3875b02000d74533b36a9b18c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486107"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Eseguire il provisioning della velocità effettiva standard (manuale) in un database nell'API Azure Cosmos DB-SQL

Questo articolo illustra come effettuare il provisioning della velocità effettiva standard (manuale) in un database in Azure Cosmos DB API SQL. È possibile effettuare il provisioning della velocità effettiva per un singolo [contenitore](how-to-provision-container-throughput.md) oppure per un database e condividere la velocità effettiva tra i contenitori al suo interno. Per informazioni su quando usare la velocità effettiva a livello di contenitore e a livello di database, vedere l'articolo sui [casi d'uso per il provisioning della velocità effettiva in contenitori e database](set-throughput.md). È possibile effettuare il provisioning della velocità effettiva a livello di database usando il portale di Azure o gli SDK di Azure Cosmos DB.

Se si usa un'API diversa, vedere l'articolo relativo alle API [per MongoDB](how-to-provision-throughput-mongodb.md), [API Cassandra](how-to-provision-throughput-cassandra.md), articoli sull' [API Gremlin](how-to-provision-throughput-gremlin.md) per eseguire il provisioning della velocità effettiva.

## <a name="provision-throughput-using-azure-portal"></a>Effettuare il provisioning della velocità effettiva usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuovo database**. Specificare i dettagli seguenti:

   * Immettere un ID database.
   * Selezionare l'opzione **provisioning database throughput** .
   * Immettere una velocità effettiva, ad esempio 1000 UR/sec.
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Screenshot della finestra di dialogo Nuovo database":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Effettuare il provisioning della velocità effettiva usando l'interfaccia della riga di comando di Azure o PowerShell

Per creare un database con velocità effettiva condivisa, vedere

* [Creare un database con l'interfaccia della riga di comando di Azure](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Creare un database usando PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Effettuare il provisioning della velocità effettiva usando .NET SDK

> [!Note]
> È possibile usare Azure Cosmos SDK per l'API SQL per eseguire il provisioning della velocità effettiva per tutte le API. Facoltativamente, è possibile usare l'esempio seguente anche per l'API Cassandra.

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

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sulla velocità effettiva sottoposta a provisioning in Azure Cosmos DB:

* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](./request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Effettuare il provisioning della velocità effettiva standard (manuale) per un contenitore](how-to-provision-container-throughput.md)
* [Come effettuare il provisioning della velocità effettiva con scalabilità automatica per un contenitore](how-to-provision-autoscale-throughput.md)
* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)