---
title: Ottenere le metriche di esecuzione delle prestazioni delle query SQLGet SQL query performance & execution metrics
description: Informazioni su come recuperare le metriche di esecuzione delle query SQL e profilare le prestazioni delle query SQL delle richieste del database Cosmos di Azure.Learn how to retrieve SQL query execution metrics and profile SQL query performance of Azure Cosmos DB requests.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998374"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Ottenere metriche di esecuzione delle query SQL e analizzare le prestazioni delle query usando .NET SDKGet SQL query execution metrics and analyze query performance using .NET SDK

Questo articolo illustra come profilare le prestazioni delle query SQL nel database Cosmos di Azure.This article presents how to profile SQL query performance on Azure Cosmos DB. Questa profilatura può `QueryMetrics` essere eseguita utilizzando recuperato da .NET SDK ed è descritto in dettaglio qui. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) è un oggetto fortemente tipizzato con informazioni sull'esecuzione della query back-end. Queste metriche sono documentate in modo più dettagliato nell'articolo [Ottimizzare le prestazioni delle query.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="set-the-feedoptions-parameter"></a>Impostare il parametro FeedOptions

Tutti gli overload per [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) accettano un parametro [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) facoltativo. Questa opzione consente di tonare e parametrizzare l'esecuzione della query. 

Per raccogliere le metriche di esecuzione delle query SQL, è `true`necessario impostare il parametro [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) in [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) su . L'impostazione `PopulateQueryMetrics` su true `FeedResponse` renderà in `QueryMetrics`modo che il conterrà il pertinente . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Ottenere metriche di query con AsDocumentQuery()
L'esempio di codice seguente mostra come eseguire il recupero delle metriche quando si utilizza il metodo [AsDocumentQuery():](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Aggregazione di QueryMetrics

Nella sezione precedente, si noti che sono presenti più chiamate al metodo [ExecuteNextAsync.In](https://msdn.microsoft.com/library/azure/dn850294.aspx) the previous section, notice that there were multiple calls to ExecuteNextAsync method. Ogni chiamata ha `FeedResponse` restituito un oggetto `QueryMetrics`che dispone di un dizionario di ; uno per ogni continuazione della query. L'esempio seguente mostra `QueryMetrics` come aggregare questi usando LINQ:The following example shows how to aggregate these using LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Raggruppamento delle metriche di query in base all'ID partizione

È possibile `QueryMetrics` raggruppare il per ID partizione. Il raggruppamento in base all'ID partizione consente di verificare se una partizione specifica causa problemi di prestazioni rispetto ad altri. Nell'esempio seguente viene `QueryMetrics` illustrato come eseguire il raggruppamento con LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ su DocumentQuery

È anche possibile `FeedResponse` ottenere il da `AsDocumentQuery()` una query LINQ utilizzando il metodo :You can also get the from a LINQ Query using the method:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Query dispendiose

È possibile acquisire le unità di richiesta utilizzate da ogni query per analizzare le query o le query costose che utilizzano una velocità effettiva elevata. È possibile ottenere l'addebito per le richieste usando la proprietà [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) in `FeedResponse`. Per altre informazioni su come ottenere l'addebito della richiesta usando il portale di Azure e SDK diversi, vedere [l'articolo Addebito unità](find-request-unit-charge.md) richiesta.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Ottenere il tempo di esecuzione della queryGet the query execution time

Quando si calcola il tempo necessario per eseguire una query sul lato `ExecuteNextAsync` client, assicurarsi di includere solo il tempo necessario per chiamare il metodo e non altre parti della base di codice. Solo queste chiamate consentono di calcolare la durata dell'esecuzione della query, come illustrato nell'esempio seguente:Just these calls help you in calculating how long the query execution took as shown in the following example:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Scansione di query (comunemente lente e costose)

Una query di analisi fa riferimento a una query che non è stata servita dall'indice, a causa della quale, molti documenti vengono caricati prima di restituire il set di risultati.

Di seguito è riportato un esempio di query di scansione:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Il filtro di questa query utilizza la funzione di sistema MAIUSC, che non viene servita dall'indice. L'esecuzione di questa query su una raccolta di grandi dimensioni ha prodotto le metriche di query seguenti per la prima continuazione:Executing this query against a large collection produced the following query metrics for the first continuation:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Tenere presente i valori seguenti dall'output delle metriche di query:Note the following values from the query metrics output:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Questa query ha caricato 60.951 documenti, per un totale di 399.998.938 byte. Il caricamento di questo numero di byte comporta un costo elevato o un costo unitario di richiesta. Richiede inoltre molto tempo per eseguire la query, che è chiaro con la proprietà tempo totale trascorso:It also takes a long time to execute the query, which is clear with the total time spent property:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Ciò significa che l'esecuzione della query ha richiesto 4,5 secondi (e questa era solo una continuazione).

Per ottimizzare questa query di esempio, evitare l'uso di MAIUSC nel filtro. Al contrario, quando i `c.description` documenti vengono creati o aggiornati, i valori devono essere inseriti in tutti i caratteri maiuscoli. La query diventa quindi: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Questa query può ora essere gestita dall'indice.

Per altre informazioni sull'ottimizzazione delle prestazioni delle query, vedere l'articolo [Ottimizzare le prestazioni delle query.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="references"></a><a id="References"></a>Riferimenti

- [Specifica SQL di Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011 (informazioni IN LINGUA INGLESE)](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [Json](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Passaggi successivi

- [Ottimizzare le prestazioni delle query](sql-api-query-metrics.md)
- [Panoramica dell'indicizzazione](index-overview.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
