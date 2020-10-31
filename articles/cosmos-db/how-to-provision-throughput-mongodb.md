---
title: Provisioning della velocità effettiva nell'API Azure Cosmos DB per le risorse MongoDB
description: Informazioni su come effettuare il provisioning della velocità effettiva di contenitori, database e scalabilità automatica in Azure Cosmos DB API per le risorse MongoDB. Si utilizzeranno portale di Azure, l'interfaccia della riga di comando, PowerShell e vari altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 25eaa4a66fb4a73f976edbd30e6f82015ce84f6f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086126"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Provisioning di velocità effettiva di database, contenitori o scalabilità automatica in Azure Cosmos DB API per MongoDB risorse
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Questo articolo illustra come effettuare il provisioning della velocità effettiva nell'API Azure Cosmos DB per MongoDB. È possibile effettuare il provisioning della velocità effettiva standard (manuale) o di scalabilità automatica in un contenitore o in un database e condividerla tra i contenitori all'interno del database. Puoi effettuare il provisioning della velocità effettiva usando portale di Azure, l'interfaccia della riga di comando di Azure o Azure Cosmos DB

Se si usa un'API diversa, vedere l'articolo relativo all' [API SQL](how-to-provision-container-throughput.md), [API Cassandra](how-to-provision-throughput-cassandra.md), articoli sull' [API Gremlin](how-to-provision-throughput-gremlin.md) per eseguire il provisioning della velocità effettiva.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **Nuova raccolta** . Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente. Selezionare l'opzione **provisioning database throughput** se si desidera effettuare il provisioning della velocità effettiva a livello di database.
   * Immettere un ID raccolta.
   * Immettere un valore della chiave di partizione, ad esempio `/ItemID`.
   * Immettere una velocità effettiva di cui si desidera eseguire il provisioning (ad esempio, 1000 UR).
   * Selezionare **OK** .

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Screenshot della Esplora dati quando si crea una nuova raccolta con velocità effettiva a livello di database":::

> [!Note]
> Se si effettua il provisioning della velocità effettiva in un contenitore in un account Azure Cosmos configurato con l'API Azure Cosmos DB per MongoDB, usare `/myShardKey` come percorso della chiave di partizione.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

È possibile usare i modelli di Azure Resource Manager per eseguire il provisioning della velocità effettiva di ridimensionamento automatico per tutte le API Azure Cosmos DB di database o a livello di contenitore Per esempi, vedere [Azure Resource Manager Templates for Azure Cosmos DB](templates-samples-mongodb.md) .

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello Azure Cosmos DB di Per esempi, vedere [esempi dell'interfaccia della riga di comando di Azure per Azure Cosmos DB](cli-samples-mongodb.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell può essere usato per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello di contenitore per tutte le API Azure Cosmos DB Per esempi, vedere [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples-mongodb.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni sul provisioning della velocità effettiva in Azure Cosmos DB:

* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)