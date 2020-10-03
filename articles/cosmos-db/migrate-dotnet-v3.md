---
title: Eseguire la migrazione dell'applicazione per l'uso di Azure Cosmos DB .NET SDK 3,0 (com. Azure. Cosmos)
description: Informazioni su come aggiornare l'applicazione .NET esistente da V2 SDK al nuovo .NET SDK V3 (pacchetto com. Azure. Cosmos) per l'API di base (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 68d1656e96264107be60d114b392d759ccfe367c
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671314"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Eseguire la migrazione dell'applicazione per l'uso di Azure Cosmos DB .NET SDK V3

> [!IMPORTANT]
> Per informazioni su Azure Cosmos DB .NET SDK V3, vedere le [Note sulla versione](sql-api-sdk-dotnet-standard.md), il [repository .NET GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3), i [suggerimenti sulle prestazioni](performance-tips-dotnet-sdk-v3-sql.md)di .NET SDK V3 e la [Guida alla risoluzione dei problemi](troubleshoot-dot-net-sdk.md).
>

Questo articolo illustra alcune considerazioni relative all'aggiornamento dell'applicazione .NET esistente alla versione più recente Azure Cosmos DB .NET SDK V3 per l'API principale (SQL). Azure Cosmos DB .NET SDK V3 corrisponde allo spazio dei nomi Microsoft. Azure. Cosmos. È possibile usare le informazioni fornite in questo documento se si esegue la migrazione dell'applicazione da uno dei seguenti Azure Cosmos DB SDK .NET:

* Azure Cosmos DB .NET Framework SDK v2 per l'API SQL
* Azure Cosmos DB .NET Core SDK v2 per l'API SQL

Le istruzioni riportate in questo articolo consentono inoltre di eseguire la migrazione delle librerie esterne seguenti che fanno ora parte dell'API Azure Cosmos DB .NET SDK V3 per core (SQL):

* Libreria del processore del feed delle modifiche .NET 2,0
* Libreria di esecuzioni bulk .NET 1,1 o successiva

## <a name="whats-new-in-the-net-v3-sdk"></a>Novità di .NET V3 SDK

Il V3 SDK contiene molti miglioramenti di usabilità e prestazioni, tra cui:

* Denominazione intuitiva dei modelli di programmazione
* .NET Standard 2,0 * *
* Miglioramento delle prestazioni tramite il supporto dell'API Stream
* Gerarchia Fluent che sostituisce la necessità della Factory URI
* Supporto incorporato per la libreria del processore dei feed delle modifiche
* Supporto incorporato per operazioni bulk
* API Mockabale per il testing unità più semplice
* Supporto di batch e blazer transazionale
* Serializzatori collegabili
* Ridimensionare i contenitori non partizionati e con scalabilità automatica

* * L'SDK è destinato a .NET Standard 2,0 che unifica il .NET Framework di Azure Cosmos DB esistente e gli SDK di .NET Core in un unico .NET SDK. È possibile usare .NET SDK in qualsiasi piattaforma che implementi .NET Standard 2,0, incluse le applicazioni .NET Framework 4.6.1 + e .NET Core 2.0 +.

La maggior parte della rete, la logica di ripetizione dei tentativi e i livelli inferiori dell'SDK rimangono sostanzialmente invariati.

**Il Azure Cosmos DB .NET SDK V3 è ora Open Source.** Si accettano le richieste pull e si registrano problemi e si verificano commenti e suggerimenti su [github.](https://github.com/Azure/azure-cosmos-dotnet-v3/) Si utilizzeranno le funzionalità che miglioreranno l'esperienza dei clienti.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Vantaggi della migrazione a .NET V3 SDK

Oltre ai numerosi miglioramenti all'usabilità e alle prestazioni, i nuovi investimenti per le funzionalità apportati nell'SDK più recente non verranno restituiti a versioni precedenti.

Sebbene non esistano piani immediati per [disattivare il supporto per gli sdk 2,0](sql-api-sdk-dotnet.md), gli SDK verranno sostituiti da versioni più recenti in futuro e l'SDK passerà alla modalità manutenzione. Per un'esperienza di sviluppo ottimale, è consigliabile iniziare sempre con la versione supportata più recente dell'SDK.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>Modifiche dei nomi principali da V2 SDK a V3 SDK

In .NET 3,0 SDK sono state applicate le modifiche al nome seguenti per essere allineate alle convenzioni di denominazione API per l'API Core (SQL):

* `DocumentClient` viene rinominato in `CosmosClient`
* `Collection` viene rinominato in `Container`
* `Document` viene rinominato in `Item`

Tutti gli oggetti risorsa vengono rinominati con proprietà aggiuntive, che include il nome della risorsa per maggiore chiarezza.

Di seguito sono riportate alcune delle principali modifiche al nome della classe:

| SDK di .NET V3 | SDK di .NET v2 |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>Classi sostituite in .NET V3 SDK

Le seguenti classi sono state sostituite in 3,0 SDK:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

Microsoft.Azure.Documents. La classe UriFactory è stata sostituita dalla progettazione Fluent. La progettazione Fluent compila gli URL internamente e consente `Container` di passare un singolo oggetto invece di `DocumentClient` , `DatabaseName` e `DocumentCollection` .

### <a name="changes-to-item-id-generation"></a>Modifiche alla generazione dell'ID elemento

L'ID elemento non è più popolato automaticamente in .NET V3 SDK. Pertanto, l'ID dell'elemento deve includere un ID generato in modo specifico. Vedere l'esempio seguente:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Modifica del comportamento predefinito per la modalità di connessione

Per impostazione predefinita, l'SDK V3 prevede le modalità di connessione diretta + TCP rispetto alla versione V2 SDK precedente, che è impostata per impostazione predefinita su gateway + connessioni HTTPS. Questa modifica offre prestazioni e scalabilità migliorate.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>Modifiche a FeedOptions (QueryRequestOptions in v 3.0 SDK)

La `FeedOptions` classe in SDK v2 è stata rinominata `QueryRequestOptions` in SDK V3 e all'interno della classe, diverse proprietà hanno modificato il nome e/o il valore predefinito oppure sono state rimosse completamente.  

`FeedOptions.MaxDegreeOfParallelism` è stato rinominato in `QueryRequestOptions.MaxConcurrency` e il valore predefinito e il comportamento associato rimangono invariati, le operazioni eseguite sul lato client durante l'esecuzione di query parallele verranno eseguite in modo seriale senza parallelismo.

`FeedOptions.EnableCrossPartitionQuery` è stato rimosso e il comportamento predefinito nell'SDK 3,0 è che le query tra partizioni verranno eseguite senza la necessità di abilitare la proprietà in modo specifico.

`FeedOptions.PopulateQueryMetrics` è abilitato per impostazione predefinita con i risultati presenti nella proprietà di diagnostica della risposta.

`FeedOptions.RequestContinuation` Ora è stato promosso ai metodi di query stessi.

Sono state rimosse le proprietà seguenti:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>Creazione di un client

.NET SDK V3 fornisce una classe Fluent `CosmosClientBuilder` che sostituisce la necessità della Factory URI SDK v2.

Nell'esempio seguente viene creato un nuovo oggetto `CosmosClientBuilder` con un ConsistencyLevel forte e un elenco di percorsi preferiti:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>Uso delle API del processore dei feed delle modifiche direttamente da V3 SDK

V3 SDK dispone del supporto incorporato per le API del processore dei feed delle modifiche, consentendo di utilizzare lo stesso SDK per la compilazione dell'applicazione e l'implementazione del processore del feed di modifiche. In precedenza era necessario usare una libreria del processore del feed delle modifiche distinta.

Per ulteriori informazioni, vedere [come eseguire la migrazione dalla libreria del processore dei feed delle modifiche al Azure Cosmos DB .NET V3 SDK](how-to-migrate-from-change-feed-library.md)

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>Uso della libreria dell'esecutore bulk direttamente da V3 SDK

V3 SDK dispone del supporto incorporato per la libreria dell'executor in blocco, che consente di utilizzare lo stesso SDK per la compilazione dell'applicazione e l'esecuzione di operazioni bulk. In precedenza era necessario usare una libreria di esecutori bulk separata.

Per ulteriori informazioni, vedere [come eseguire la migrazione dalla libreria dell'executor bulk per supportare in blocco in Azure Cosmos DB .NET V3 SDK](how-to-migrate-from-bulk-executor-library.md) .

## <a name="code-snippet-comparisons"></a>Confronti tra frammenti di codice

Il frammento di codice seguente mostra le differenze nella modalità di creazione delle risorse tra gli SDK di .NET v2 e V3:

## <a name="database-operations"></a>Operazioni del database

### <a name="create-a-database"></a>Creazione di un database

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>Leggere un database in base all'ID

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Eliminare un database

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Operazioni del contenitore

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Creazione di un contenitore (scalabilità automatica + durata (TTL) con scadenza)

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Leggere le proprietà del contenitore

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Eliminare un contenitore

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Operazioni di query e di elemento

### <a name="create-an-item"></a>Creare un elemento

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Leggere tutti gli elementi in un contenitore

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Eseguire query sugli elementi

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Eliminare un elemento

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app console](sql-api-get-started.md) per gestire i dati dell'API SQL di Azure Cosmos DB usando v3 SDK
* Altre informazioni sulle [operazioni che è possibile eseguire con l'SDK V3](sql-api-dotnet-v3sdk-samples.md)