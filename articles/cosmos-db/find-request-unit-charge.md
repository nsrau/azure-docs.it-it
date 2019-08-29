---
title: Trovare l'addebito delle unità richieste (UR) in Azure Cosmos DB
description: Informazioni su come trovare l'addebito delle unità richiesta (UR) per qualsiasi operazione eseguita con un contenitore di Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 682273524269682f62cd386de1c9161888747f16
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093717"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Trovare l'addebito delle unità richiesta in Azure Cosmos DB

Questo articolo presenta i vari modi in cui è possibile trovare il consumo di [unità richiesta](request-units.md) (UR) per qualsiasi operazione eseguita con un contenitore in Azure Cosmos DB. È attualmente possibile misurare questo consumo solo usando il portale di Azure oppure esaminando la risposta inviata da Azure Cosmos DB tramite uno degli SDK.

## <a name="sql-core-api"></a>API SQL (Core)

Se si usa l'API SQL, sono disponibili diverse opzioni per trovare il consumo di UR per un'operazione con un contenitore di Azure Cosmos.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È attualmente possibile trovare l'addebito relativo alla richiesta nel portale di Azure solo per una query SQL.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account di Azure Cosmos](create-sql-api-dotnet.md#create-account) e inserirvi dati oppure selezionare un account Azure Cosmos esistente che contiene già dati.

1. Passare al riquadro **Esplora dati** e quindi selezionare il contenitore da usare.

1. Fare clic su **Nuova query SQL**.

1. Immettere una query valida e quindi fare clic su **Esegui query**.

1. Fare clic su **Statistiche query** per visualizzare l'addebito effettivo per la richiesta eseguita.

![Screenshot dell'addebito per la richiesta relativa a una query SQL nel portale di Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>Usare .NET SDK
### <a name="net-v2-sdk"></a>.Net V2 SDK

Gli oggetti restituiti da [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) espongono una proprietà `RequestCharge`:

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

Gli oggetti restituiti da [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) espongono una proprietà `RequestCharge`:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Per altre informazioni, vedere [Avvio rapido: Compilare un'app Web .NET usando l'account API SQL in Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Usare Java SDK

Gli oggetti restituiti da [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) espongono un metodo `getRequestCharge()`:

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

Per altre informazioni, vedere [Avvio rapido: Creare un'applicazione Java usando l'account API SQL di Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Usare Node.js SDK

Gli oggetti restituiti da [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) espongono un oggetto secondario `headers` che viene mappato a tutte le intestazione restituite dall'API HTTP sottostante. L'addebito della richiesta è disponibile sotto la chiave `x-ms-request-charge`:

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

Per altre informazioni, vedere [Avvio rapido: Compilare un'app Node.js usando un account API SQL di Azure Cosmos DB](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Usare Python SDK

L'oggetto `CosmosClient` proveniente da [Python SDK](https://pypi.org/project/azure-cosmos/) espone un dizionario `last_response_headers` che viene mappato a tutte le intestazione restituite dall'API HTTP sottostante per l'ultima operazione eseguita. L'addebito della richiesta è disponibile sotto la chiave `x-ms-request-charge`:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Per altre informazioni, vedere [Avvio rapido: Creare un'applicazione Python usando l'account API SQL di Azure Cosmos DB](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API Azure Cosmos DB per MongoDB

L'addebito delle UR è esposto da un [comando di database](https://docs.mongodb.com/manual/reference/command/) personalizzato denominato `getLastRequestStatistics`. Il comando restituisce un documento contenente il nome dell'ultima operazione eseguita, il relativo addebito della richiesta e la durata. Se si usa l'API di Azure Cosmos DB per MongoDB, sono disponibili diverse opzioni per il recupero dell'addebito delle UR.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È attualmente possibile trovare l'addebito relativo alla richiesta nel portale di Azure solo per una query.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account di Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) e inserirvi dati oppure selezionare un account esistente che contiene già dati.

1. Passare al riquadro **Esplora dati** e quindi selezionare la raccolta da usare.

1. Scegliere **Nuova query**.

1. Immettere una query valida e quindi fare clic su **Esegui query**.

1. Fare clic su **Statistiche query** per visualizzare l'addebito effettivo per la richiesta eseguita.

![Screenshot dell'addebito per la richiesta relativa a una query MongoDB nel portale di Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Usare il driver .NET di MongoDB

Quando si usa il [driver .NET MongoDB ufficiale](https://docs.mongodb.com/ecosystem/drivers/csharp/), è possibile eseguire i comandi chiamando il metodo `RunCommand` per un oggetto `IMongoDatabase`. Questo metodo richiede un'implementazione della classe astratta `Command<>`:

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

Per altre informazioni, vedere [Avvio rapido: Creare un'app Web .NET usando l'API Azure Cosmos DB per MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Usare il driver Java di MongoDB


Quando si usa il [driver Java MongoDB ufficiale](https://mongodb.github.io/mongo-java-driver/), è possibile eseguire i comandi chiamando il metodo `runCommand` per un oggetto `MongoDatabase`:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Per altre informazioni, vedere [Avvio rapido: Creare un'app Web usando l'API Azure Cosmos DB per MongoDB e Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Usare il driver Node.js di MongoDB

Quando si usa il [driver Node.js MongoDB ufficiale](https://mongodb.github.io/node-mongodb-native/), è possibile eseguire i comandi chiamando il metodo `command` per un oggetto `db`:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Per altre informazioni, vedere [Avvio rapido: Eseguire la migrazione di un'app Web Node.js MongoDB esistente ad Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>API Cassandra

Quando si eseguono operazioni con l'API Cassandra di Azure Cosmos DB, l'addebito delle UR viene restituito nel payload in ingresso come campo denominato `RequestCharge`. Sono disponibili diverse opzioni per il recupero dell'addebito delle UR.

### <a name="use-the-net-sdk"></a>Usare .NET SDK

Quando si usa [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/), è possibile recuperare il payload in ingresso sotto la proprietà `Info` di un oggetto `RowSet`:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Per altre informazioni, vedere [Avvio rapido: Creare un'app Cassandra con .NET SDK e Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Usare Java SDK

Quando si usa [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), è possibile recuperare il payload in ingresso chiamando il metodo `getExecutionInfo()` per un oggetto `ResultSet`:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Per altre informazioni, vedere [Avvio rapido: Creare un'app Cassandra con Java SDK e Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>API Gremlin

Quando si usa l'API Gremlin, sono disponibili diverse opzioni per trovare il consumo di UR per un'operazione con un contenitore di Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Usare driver e SDK

Le intestazioni restituite dall'API Gremlin vengono mappate agli attributi di stato personalizzati attualmente esposti da Gremlin .NET e Java SDK. L'addebito della richiesta è disponibile sotto la chiave `x-ms-request-charge`.

### <a name="use-the-net-sdk"></a>Usare .NET SDK

Quando si usa [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/), gli attributi di stato sono disponibili sotto la proprietà `StatusAttributes` dell'oggetto `ResultSet<>`:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Per altre informazioni, vedere [Avvio rapido: Creare un'applicazione .NET Framework o Core usando l'account dell'API Gremlin di Azure Cosmos DB](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Usare Java SDK

Quando si usa [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), è possibile recuperare gli attributi di stato chiamando il metodo `statusAttributes()` per l'oggetto `ResultSet`:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Per altre informazioni, vedere [Avvio rapido: Creare un database a grafo in Azure Cosmos DB usando Java SDK](create-graph-java.md).

## <a name="table-api"></a>API di tabella

Attualmente l'unico SDK che restituisce l'addebito delle UR per le operazioni di tabella è [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). L'oggetto `TableResult` espone una proprietà `RequestCharge` che viene popolata dall'SDK quando viene usata con l'API Tabella di Azure Cosmos DB:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Per altre informazioni, vedere [Avvio rapido: Creare un'app dell'API Tabella con .NET SDK e Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottimizzare il consumo di UR, vedere questi articoli:

* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
* [Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md)
* [Ottimizzare il costo delle query in Azure Cosmos DB](optimize-cost-queries.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Effettuare il provisioning della velocità effettiva per un contenitore](how-to-provision-container-throughput.md)
* [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md)
