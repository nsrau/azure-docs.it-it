---
title: Ottiene la metrica di prestazioni e l'esecuzione di query SQL
description: Informazioni su come recuperare metriche di esecuzione delle query SQL e le prestazioni delle query SQL di Azure Cosmos DB richieste di profilo.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481565"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Ottenere metriche di esecuzione delle query SQL e analizzare le prestazioni delle query usando .NET SDK

Questo articolo viene illustrato come eseguire la profilatura delle prestazioni di query SQL in Azure Cosmos DB. Questo profilo può essere eseguita usando `QueryMetrics` recuperate da .NET SDK ed è descritta di seguito. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) è un oggetto fortemente tipizzato con informazioni sull'esecuzione di query del back-end. Queste metriche sono documentate in dettaglio nel [ottimizzare le prestazioni delle Query](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) articolo.

## <a name="set-the-feedoptions-parameter"></a>Impostare il parametro FeedOptions

Tutti gli overload [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) prendere facoltativo [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) parametro. Questa opzione è ciò che consente l'esecuzione di query da ottimizzare e con parametri. 

Per raccogliere le metriche di esecuzione di query Sql, è necessario impostare il parametro [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) nel [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) a `true`. L'impostazione `PopulateQueryMetrics` a true renderà in modo che il `FeedResponse` conterrà pertinente `QueryMetrics`. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Ottenere le metriche di query con AsDocumentQuery()
Esempio di codice seguente viene illustrato come recuperare le metriche quando si usa [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) metodo:

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
## <a name="aggregating-querymetrics"></a>Aggregating QueryMetrics

Nella sezione precedente, si noti che non vi sono più chiamate a [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) (metodo). Ogni chiamata ha restituito un `FeedResponse` oggetto contenente un dizionario di `QueryMetrics`, uno per ogni continuazione della query. Nell'esempio seguente mostra come aggregare questi `QueryMetrics` utilizzando LINQ:

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

## <a name="grouping-query-metrics-by-partition-id"></a>Metriche di query di raggruppamento dall'ID di partizione

È possibile raggruppare il `QueryMetrics` dall'ID della partizione. Il raggruppamento per ID di partizione consente di verificare se una partizione specifica sta provocando problemi di prestazioni rispetto ad altri utenti. Nell'esempio seguente viene illustrato come raggruppare `QueryMetrics` con LINQ:

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

È anche possibile ottenere il `FeedResponse` da una Query LINQ usando il `AsDocumentQuery()` metodo:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Query con costo elevato

È possibile acquisire le unità richiesta utilizzate da ciascuna query per analizzare query con costo elevato o query che utilizzano una velocità effettiva elevata. È possibile ottenere l'addebito richiesta usando il [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) in proprietà `FeedResponse`. Per altre informazioni su come ottenere l'addebito richiesta usando il portale di Azure e diversi SDK, vedere [trovare l'addebito delle unità richiesta](find-request-unit-charge.md) articolo.

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

## <a name="get-the-query-execution-time"></a>Ottenere il tempo di esecuzione di query

Quando si calcola il tempo necessario per eseguire una query sul lato client, assicurarsi di includere solo il tempo necessario per chiamare il `ExecuteNextAsync` metodo e non altre parti della codebase. Solo queste chiamate consentono di calcolare il tempo di esecuzione della query impiegato come illustrato nell'esempio seguente:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Analizzare le query (comunemente lenta e dispendiosa)

Una query di analisi fa riferimento a una query che non è stata servita dall'indice, pertanto, molti documenti vengono caricati prima di restituire il set di risultati.

Di seguito è riportato un esempio di una query di analisi:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Filtro di questa query Usa la funzione di sistema superiore, che non è servito dall'indice. L'esecuzione di questa query su una vasta raccolta prodotta le metriche di query seguenti per la continuazione prima:

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

Prendere nota dei valori seguenti dell'output di metriche di query:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Questa query caricata 60,951 documenti, che sommati 399,998,938 byte. Il caricamento di questo numero di byte conseguente addebito delle unità richiesta o costo elevato. Inoltre richiede molto tempo per eseguire la query, ovvero non crittografata con la proprietà tempo totale trascorso:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Vale a dire che la query ha richiesto 4.5 secondi per l'esecuzione (e questo è solo una continuazione).

Per ottimizzare la query di esempio, evitare l'uso di superiore nel filtro. Al contrario, quando i documenti creati o aggiornati, il `c.description` i valori devono essere inseriti in tutti i caratteri maiuscoli. La query diventa quindi: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Questa query è ora in grado di essere servite dall'indice.

Per altre informazioni sull'ottimizzazione delle prestazioni delle query, vedere la [ottimizzare le prestazioni delle Query](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) articolo.

## <a id="References"></a>Riferimenti

- [Specifica SQL di Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Passaggi successivi

- [Ottimizzare le prestazioni delle query](sql-api-query-metrics.md)
- [Panoramica dell'indicizzazione](index-overview.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
