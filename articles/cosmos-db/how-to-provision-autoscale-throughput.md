---
title: Effettuare il provisioning della velocità effettiva per la scalabilità automatica in Azure Cosmos DB
description: Informazioni su come effettuare il provisioning della velocità effettiva per la scalabilità automatica a livello di contenitore e di database in Azure Cosmos DB usando il portale di Azure, l'interfaccia della riga di comando, PowerShell e altri SDK.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4e7c5f3f4bf84b7a267cb883df5f375f2a8cf981
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89017142"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db"></a>Effettuare il provisioning della velocità effettiva per la scalabilità automatica a livello di database o contenitore in Azure Cosmos DB

Questo articolo illustra come effettuare il provisioning della velocità effettiva per la scalabilità automatica a livello di database o contenitore (raccolta, grafo o tabella) in Azure Cosmos DB. È possibile abilitare la scalabilità automatica per un singolo contenitore oppure effettuare il provisioning della velocità effettiva per la scalabilità automatica a livello di database e condividerla tra tutti i contenitori nel database.

## <a name="azure-portal"></a>Portale di Azure

### <a name="create-new-database-or-container-with-autoscale"></a>Creare un nuovo database o contenitore con scalabilità automatica

1. Accedere al [portale di Azure](https://portal.azure.com) o a [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Passare all'account Azure Cosmos DB e aprire la scheda **Esplora dati**.

1. Selezionare **Nuovo contenitore**. Immettere un nome per il database, il contenitore e una chiave di partizione. In **Velocità effettiva** selezionare l'opzione **Scalabilità automatica** e impostare la [velocità effettiva massima (UR/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) per il dimensionamento del database o del contenitore.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Creazione di un contenitore e configurazione del provisioning della velocità effettiva per la scalabilità automatica":::

1. Selezionare **OK**.

Per effettuare il provisioning della scalabilità automatica per il database con velocità effettiva condivisa, selezionare l'opzione **Provision database throughput** (Effettua il provisioning della velocità effettiva del database) durante la creazione di un nuovo database. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Abilitare la scalabilità automatica per un database o un contenitore esistente

> [!IMPORTANT]
> Nella versione corrente per eseguire la migrazione dal provisioning della velocità effettiva per la scalabilità automatica a quello standard (manuale), è possibile usare solo il portale di Azure. 

1. Accedere al [portale di Azure](https://portal.azure.com) o a [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Passare all'account Azure Cosmos DB e aprire la scheda **Esplora dati**.

1. Selezionare **Scalabilità e impostazioni** per il contenitore oppure **Dimensiona** per il database.

1. In **Dimensiona** selezionare l'opzione **Scalabilità automatica** e fare clic su **Salva**.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Creazione di un contenitore e configurazione del provisioning della velocità effettiva per la scalabilità automatica":::

> [!NOTE]
> Quando si abilita la scalabilità automatica per un database o un contenitore esistente, il valore iniziale per il numero massimo di UR/s è determinato dal sistema, in base alle impostazioni correnti del provisioning della velocità effettiva manuale e allo spazio di archiviazione. Al termine dell'operazione, se necessario, è possibile modificare il numero massimo di UR/s. [Altre informazioni.](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk-for-sql-api"></a>Azure Cosmos DB .NET V3 SDK per API SQL

Usare la [versione 3.9 o successiva](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) di Azure Cosmos DB .NET SDK per API SQL per gestire le risorse con scalabilità automatica. 

> [!IMPORTANT]
> È possibile usare .NET SDK per creare nuove risorse con scalabilità automatica. L'SDK non supporta la migrazione tra scalabilità automatica e velocità effettiva standard (manuale). Lo scenario di migrazione è attualmente supportato solo nel portale di Azure. 

### <a name="create-database-with-shared-throughput"></a>Creare il database con velocità effettiva condivisa

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Creare il contenitore con velocità effettiva dedicata

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Leggere la velocità effettiva corrente (UR/s)

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Modificare la velocità effettiva massima per la scalabilità automatica (UR/s)

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk-for-sql-api"></a>Azure Cosmos DB Java V4 SDK per API SQL

È possibile usare la [versione 4.0 o successiva](https://mvnrepository.com/artifact/com.azure/azure-cosmos) di Azure Cosmos DB Java SDK per API SQL per gestire le risorse con scalabilità automatica.

> [!IMPORTANT]
> È possibile usare Java SDK per creare nuove risorse con scalabilità automatica. L'SDK non supporta la migrazione tra scalabilità automatica e velocità effettiva standard (manuale). Lo scenario di migrazione è attualmente supportato solo nel portale di Azure.

### <a name="create-database-with-shared-throughput"></a>Creare il database con velocità effettiva condivisa

#### <a name="async"></a>[Asincrono](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Sincronizza](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Creare il contenitore con velocità effettiva dedicata

#### <a name="async"></a>[Asincrono](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Sincronizza](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Leggere la velocità effettiva corrente (UR/s)

#### <a name="async"></a>[Asincrono](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Sincrono](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Modificare la velocità effettiva massima per la scalabilità automatica (UR/s)

#### <a name="async"></a>[Asincrono](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Sincrono](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="cassandra-api"></a>API Cassandra

È possibile eseguire il provisioning degli account Azure Cosmos DB per API Cassandra per la scalabilità automatica usando i [comandi di CQL](manage-scale-cassandra.md#use-autoscale), l'interfaccia della riga di comando di [Azure](cli-samples.md), i [modelli Azure Resource Manager](resource-manager-samples.md) [Azure PowerShell](powershell-samples.md)

## <a name="azure-cosmos-db-api-for-mongodb"></a>API Azure Cosmos DB per MongoDB

È possibile eseguire il provisioning degli account di Azure Cosmos DB per l'API MongoDB per la scalabilità automatica usando i [comandi di estensione MongoDB](mongodb-custom-commands.md), l'interfaccia della riga di comando di [Azure](cli-samples.md), [Azure Resource Manager](resource-manager-samples.md) [Azure PowerShell](powershell-samples.md)

## <a name="azure-resource-manager"></a>Azure Resource Manager

È possibile usare i modelli di Azure Resource Manager per eseguire il provisioning della velocità effettiva di ridimensionamento automatico per tutte le API Azure Cosmos DB di database o a livello di contenitore Per esempi, vedere [Azure Resource Manager Templates for Azure Cosmos DB](resource-manager-samples.md) .

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello Azure Cosmos DB di Per esempi, vedere [esempi dell'interfaccia della riga di comando di Azure per Azure Cosmos DB](cli-samples.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell può essere usato per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello di contenitore per tutte le API Azure Cosmos DB Per esempi, vedere [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples.md).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [vantaggi del provisioning della velocità effettiva con la scalabilità automatica](provision-throughput-autoscale.md#benefits-of-autoscale).
* Informazioni su come [scegliere tra la velocità effettiva manuale e per la scalabilità automatica](how-to-choose-offer.md).
* Vedere le [domande frequenti sulla scalabilità automatica](autoscale-faq.md).
