---
title: Trovare l'addebito delle unità richieste (UR) in Azure Cosmos DB
description: Informazioni su come trovare l'addebito delle unità richieste per qualsiasi operazione eseguita con un contenitore di Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2019
ms.author: thweiss
ms.openlocfilehash: e3175ee136057c695ceef3cd1976b447a529c803
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59053041"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Trovare l'addebito delle unità richieste (UR) in Azure Cosmos DB

Questo articolo presenta i vari modi disponibili per trovare il consumo di [unità richieste](request-units.md) per qualsiasi operazione eseguita con un contenitore di Azure Cosmos. È attualmente possibile misurare questo consumo usando il portale di Azure oppure esaminando la risposta inviata da Azure Cosmos DB tramite uno degli SDK.

## <a name="core-api"></a>API Core

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Il portale di Azure attualmente consente di trovare l'addebito della richiesta solo per una query di SQL.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account di Azure Cosmos DB](create-sql-api-dotnet.md#create-account) e inserirvi dati oppure selezionare un account esistente che contiene già dati.

1. Aprire il riquadro **Esplora dati** e selezionare il contenitore da usare.

1. Fare clic su **Nuova query SQL**.

1. Immettere una query valida e quindi fare clic su **Esegui query**.

1. Fare clic su **Statistiche query** per visualizzare l'addebito effettivo per la richiesta appena eseguita.

![Screenshot dell'addebito per la richiesta della query SQL nel portale di Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Usare .NET SDK v2

Gli oggetti restituiti da [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (vedere questa [guida di avvio rapido](create-sql-api-dotnet.md) per informazioni sull'utilizzo) espongono una proprietà `RequestCharge`.

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="use-the-java-sdk"></a>Usare Java SDK

Gli oggetti restituiti da [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (vedere questa [guida di avvio rapido](create-sql-api-java.md) per informazioni sull'utilizzo) espongono un metodo `getRequestCharge()`.

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

### <a name="use-the-nodejs-sdk"></a>Usare Node.js SDK

Gli oggetti restituiti da [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (vedere questa [guida di avvio rapido](create-sql-api-nodejs.md) per informazioni sull'utilizzo) espongono un oggetto secondario `headers` che viene mappato a tutte le intestazione restituite dall'API HTTP sottostante. L'addebito della richiesta è disponibile sotto la chiave `x-ms-request-charge`.

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

### <a name="use-the-python-sdk"></a>Usare Python SDK

L'oggetto `CosmosClient` restituito da [Python SDK](https://pypi.org/project/azure-cosmos/) (vedere questa [guida di avvio rapido](create-sql-api-python.md) per informazioni sull'utilizzo) espone un dizionario `last_response_headers` che viene mappato a tutte le intestazione restituite dall'API HTTP sottostante per l'ultima operazione eseguita. L'addebito della richiesta è disponibile sotto la chiave `x-ms-request-charge`.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API Azure Cosmos DB per MongoDB

L'addebito delle unità richieste è esposto da un [comando di database](https://docs.mongodb.com/manual/reference/command/) personalizzato denominato `getLastRequestStatistics`. Questo comando restituisce un documento contenente il nome dell'ultima operazione eseguita, il relativo addebito della richiesta e la durata.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Il portale di Azure attualmente consente di trovare l'addebito della richiesta solo per una query.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account di Azure Cosmos DB](create-mongodb-dotnet.md#create-a-database-account) e inserirvi dati oppure selezionare un account esistente che contiene già dati.

1. Aprire il riquadro **Esplora dati** e selezionare la raccolta da usare.

1. Fare clic su **Nuova query**.

1. Immettere una query valida e quindi fare clic su **Esegui query**.

1. Fare clic su **Statistiche query** per visualizzare l'addebito effettivo per la richiesta appena eseguita.

![Screenshot dell'addebito per la richiesta della query MongoDB nel portale di Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Usare il driver .NET di MongoDB

Quando si usa il [driver ufficiale .NET di MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/) (vedere questa [guida di avvio rapido](create-mongodb-dotnet.md) per informazioni sull'utilizzo), i comandi possono essere eseguiti chiamando il metodo `RunCommand` su un oggetto `IMongoDatabase`. Questo metodo richiede un'implementazione della classe astratta `Command<>`.

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

### <a name="use-the-mongodb-java-driver"></a>Usare il driver Java di MongoDB

Quando si usa il [driver ufficiale Java di MongoDB](http://mongodb.github.io/mongo-java-driver/) (vedere questa [guida di avvio rapido](create-mongodb-java.md) per informazioni sull'utilizzo), i comandi possono essere eseguiti chiamando il metodo `runCommand` su un oggetto `MongoDatabase`.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>Usare il driver Node.js di MongoDB

Quando si usa il [driver ufficiale Node.js di MongoDB](https://mongodb.github.io/node-mongodb-native/) (vedere questa [guida di avvio rapido](create-mongodb-nodejs.md) per informazioni sull'utilizzo), i comandi possono essere eseguiti chiamando il metodo `command` su un oggetto `Db`.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>API Cassandra

Quando si eseguono operazioni con l'API Cassandra di Azure Cosmos DB, l'addebito delle unità richieste viene restituito nel payload in ingresso come campo denominato `RequestCharge`.

### <a name="use-the-net-sdk"></a>Usare .NET SDK

Quando si usa [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) (vedere questa [guida di avvio rapido](create-cassandra-dotnet.md) per informazioni sull'utilizzo), il payload in ingresso può essere recuperato nella proprietà `Info` di un oggetto `RowSet`.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Usare Java SDK

Quando si usa [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (vedere questa [guida di avvio rapido](create-cassandra-java.md) per informazioni sull'utilizzo), il payload in ingresso può essere recuperato chiamando il metodo `getExecutionInfo()` su un oggetto `ResultSet`.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>API Gremlin

### <a name="use-drivers-and-sdk"></a>Usare driver e SDK

Le intestazioni restituite dall'API Gremlin vengono mappate agli attributi di stato personalizzati attualmente esposti da Gremlin .NET e Java SDK. L'addebito della richiesta è disponibile sotto la chiave `x-ms-request-charge`.

### <a name="use-the-net-sdk"></a>Usare .NET SDK

Quando si usa [Gremlin .NET SDK](https://www.nuget.org/packages/Gremlin.Net/) (vedere questa [guida di avvio rapido](create-graph-dotnet.md) per informazioni sull'utilizzo), gli attributi di stato sono disponibili nella proprietà `StatusAttributes` dell'oggetto `ResultSet<>`.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Usare Java SDK

Quando si usa [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (vedere questa [guida di avvio rapido](create-graph-java.md) per informazioni sull'utilizzo), gli attributi di stato possono essere recuperati chiamando il metodo `statusAttributes()` sull'oggetto `ResultSet`.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>API di tabella

L'unico SDK che attualmente restituisce l'addebito delle unità richieste per le operazioni di tabella è [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (vedere questa [guida di avvio rapido](create-table-dotnet.md) per informazioni sull'utilizzo). L'oggetto `TableResult` espone una proprietà `RequestCharge` che viene popolata dall'SDK quando viene usata con l'API Tabella di Azure Cosmos DB.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottimizzare il consumo di unità richieste, vedere gli articoli seguenti:

* [Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md)
* [Ottimizzare il costo delle query in Azure Cosmos DB](optimize-cost-queries.md)