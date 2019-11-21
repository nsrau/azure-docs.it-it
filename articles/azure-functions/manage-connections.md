---
title: Manage connections in Azure Functions
description: Informazioni su come evitare i problemi di prestazioni in Funzioni di Azure tramite i client con connessione statica.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226489"
---
# <a name="manage-connections-in-azure-functions"></a>Manage connections in Azure Functions

Functions in a function app share resources. Among those shared resources are connections: HTTP connections, database connections, and connections to services such as Azure Storage. Quando si eseguono molte funzioni contemporaneamente, è possibile esaurire le connessioni disponibili. This article explains how to code your functions to avoid using more connections than they need.

## <a name="connection-limit"></a>Connection limit

The number of available connections is limited partly because a function app runs in a [sandbox environment](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). One of the restrictions that the sandbox imposes on your code is a limit on the number of outbound connections, which is currently 600 active (1,200 total) connections per instance. When you reach this limit, the functions runtime writes the following message to the logs: `Host thresholds exceeded: Connections`. For more information, see the [Functions service limits](functions-scale.md#service-limits).

This limit is per instance. When the [scale controller adds function app instances](functions-scale.md#how-the-consumption-and-premium-plans-work) to handle more requests, each instance has an independent connection limit. That means there's no global connection limit, and you can have much more than 600 active connections across all active instances.

When troubleshooting, make sure that you have enabled Application Insights for your function app. Application Insights lets you view metrics for your function apps like executions. For more information, see [View telemetry in Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Static clients

Per evitare di mantenere più connessioni del necessario, riutilizzare le istanze di client, anziché crearne di nuove con ogni chiamata di funzione. We recommend reusing client connections for any language that you might write your function in. For example, .NET clients like the [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), and Azure Storage clients can manage connections if you use a single, static client.

Here are some guidelines to follow when you're using a service-specific client in an Azure Functions application:

- *Do not* create a new client with every function invocation.
- *Do* create a single, static client that every function invocation can use.
- *Consider* creating a single, static client in a shared helper class if different functions use the same service.

## <a name="client-code-examples"></a>Esempi di codice client

Questa sezione illustra le procedure consigliate per la creazione e l'utilizzo di client dal codice funzione.

### <a name="httpclient-example-c"></a>Esempio di HttpClient (C#)

Here's an example of C# function code that creates a static [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instance:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

A common question about [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) in .NET is "Should I dispose of my client?" In general, you dispose of objects that implement `IDisposable` when you're done using them. But you don't dispose of a static client because you aren't done using it when the function ends. La durata del client statico deve coincidere con quella dell'applicazione.

### <a name="http-agent-examples-javascript"></a>HTTP agent examples (JavaScript)

È consigliabile usare la classe [`http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) che fornisce opzioni di gestione della connessione migliori, anziché i metodi non nativi come il modulo `node-fetch`. Connection parameters are configured through options on the `http.agent` class. For detailed options available with the HTTP agent, see [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

The global `http.globalAgent` class used by `http.request()` has all of these values set to their respective defaults. Il modo consigliato per configurare i limiti di connessione in Funzioni è impostare un numero massimo globale. L'esempio seguente imposta il numero massimo di socket per l'app per la funzione:

```js
http.globalAgent.maxSockets = 200;
```

 The following example creates a new HTTP request with a custom HTTP agent only for that request:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Esempio di codice DocumentClient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) si connette a un'istanza di Azure Cosmos DB. La documentazione di Azure Cosmos DB consiglia di [usare un client di Azure Cosmos DB singleton per l'intera durata dell'applicazione](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). L'esempio seguente illustra un modello per eseguire questa operazione in una funzione:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient code example (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) connects to an Azure Cosmos DB instance. La documentazione di Azure Cosmos DB consiglia di [usare un client di Azure Cosmos DB singleton per l'intera durata dell'applicazione](../cosmos-db/performance-tips.md#sdk-usage). L'esempio seguente illustra un modello per eseguire questa operazione in una funzione:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Connessioni SqlClient

Your function code can use the .NET Framework Data Provider for SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) to make connections to a SQL relational database. This is also the underlying provider for data frameworks that rely on ADO.NET, such as [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). A differenza delle connessioni [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), ADO.NET implementa pool di connessioni per impostazione predefinita. But because you can still run out of connections, you should optimize connections to the database. Per altre informazioni, vedere [Pool di connessioni SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Some data frameworks, such as Entity Framework, typically get connection strings from the **ConnectionStrings** section of a configuration file. In questo caso, è necessario aggiungere esplicitamente le stringhe di connessione di database SQL alla raccolta delle **stringhe di connessione** delle impostazioni dell'app per le funzioni e al file [local.settings.json](functions-run-local.md#local-settings-file) nel progetto locale. If you're creating an instance of [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) in your function code, you should store the connection string value in **Application settings** with your other connections.

## <a name="next-steps"></a>Passaggi successivi

For more information about why we recommend static clients, see [Improper instantiation antipattern](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Per altri suggerimenti per incrementare le prestazioni di Funzioni di Azure, vedere [Ottimizzare le prestazioni e l'affidabilità delle funzioni di Azure](functions-best-practices.md).
