---
title: Trova il costo dell'unità richiesta (UR) per una query SQL in Azure Cosmos DB
description: Informazioni su come trovare l'addebito delle unità richiesta (UR) per le query SQL eseguite su un contenitore di Azure Cosmos. È possibile usare i linguaggi portale di Azure, .NET, Java, Python e Node.js per trovare l'addebito delle UR.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 23f334d28ef5045c68bb84fc0bc34e8f847fe0f9
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281845"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Trovare l'addebito delle unità richiesta per le operazioni eseguite in Azure Cosmos DB API SQL

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità.

Il costo di tutte le operazioni di database viene normalizzato da Azure Cosmos DB ed è espresso in termini di unità richiesta (o in breve UR). È possibile considerare le UR come una valuta delle prestazioni astraendo le risorse di sistema, ad esempio CPU, IOPS e memoria, necessarie per eseguire le operazioni del database supportate da Azure Cosmos DB. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Il fatto che l'operazione di database sia una scrittura, un punto di lettura o una query, i costi vengono sempre misurati in ur. Per altre informazioni, vedere l'articolo relativo alle [unità richiesta e alle relative considerazioni](request-units.md) .

Questo articolo illustra i diversi modi in cui è possibile trovare il consumo di [unità richiesta](request-units.md) (UR) per qualsiasi operazione eseguita su un contenitore in Azure Cosmos DB API SQL. Se si usa un'API diversa, vedere l'articolo [relativo all'API per MongoDB](find-request-unit-charge-mongodb.md), [API Cassandra](find-request-unit-charge-cassandra.md), l' [api Gremlin](find-request-unit-charge-gremlin.md)e gli articoli [API tabella](find-request-unit-charge-table.md) per trovare il costo di Ur/s.

È attualmente possibile misurare questo consumo solo usando il portale di Azure oppure esaminando la risposta inviata da Azure Cosmos DB tramite uno degli SDK. Se si usa l'API SQL, sono disponibili diverse opzioni per trovare il consumo di UR per un'operazione con un contenitore di Azure Cosmos.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account di Azure Cosmos](create-sql-api-dotnet.md#create-account) e inserirvi dati oppure selezionare un account Azure Cosmos esistente che contiene già dati.

1. Passare al riquadro **Esplora dati** e quindi selezionare il contenitore da usare.

1. Selezionare **Nuova query SQL**.

1. Immettere una query valida e quindi fare clic su **Esegui query**.

1. Fare clic su **Statistiche query** per visualizzare l'addebito effettivo per la richiesta eseguita.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Screenshot dell'addebito per la richiesta relativa a una query SQL nel portale di Azure":::

## <a name="use-the-net-sdk"></a>Usare .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Gli oggetti restituiti da [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) espongono una proprietà `RequestCharge`:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Per altre informazioni, vedere [Guida introduttiva: creare un'app Web .NET usando un account API SQL in Azure Cosmos DB](create-sql-api-dotnet.md).

---

## <a name="use-the-java-sdk"></a>Usare Java SDK

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

Per altre informazioni, vedere [Guida introduttiva: creare un'applicazione Java usando un account API SQL Azure Cosmos DB](create-sql-api-java.md).

## <a name="use-the-nodejs-sdk"></a>Usare Node.js SDK

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

Per altre informazioni, vedere [Guida introduttiva: creare un'app Node.js usando un account di Azure Cosmos DB API SQL](create-sql-api-nodejs.md). 

## <a name="use-the-python-sdk"></a>Usare Python SDK

L'oggetto `CosmosClient` proveniente da [Python SDK](https://pypi.org/project/azure-cosmos/) espone un dizionario `last_response_headers` che viene mappato a tutte le intestazione restituite dall'API HTTP sottostante per l'ultima operazione eseguita. L'addebito della richiesta è disponibile sotto la chiave `x-ms-request-charge`:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Per altre informazioni, vedere [Guida introduttiva: creare un'app Python usando un account API SQL Azure Cosmos DB](create-sql-api-python.md). 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottimizzare il consumo di UR, vedere questi articoli:

* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
* [Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md)
* [Ottimizzare il costo delle query in Azure Cosmos DB](optimize-cost-queries.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Effettuare il provisioning della velocità effettiva per un contenitore](how-to-provision-container-throughput.md)
* [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md)
