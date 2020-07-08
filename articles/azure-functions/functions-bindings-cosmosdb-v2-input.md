---
title: Binding di input Azure Cosmos DB per le funzioni 2. x e versioni successive
description: Informazioni su come usare l'associazione di input Azure Cosmos DB in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: 5e41f5d2189cce19dab3e0b48943ef0568ddedb8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85807010"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>Binding di input Azure Cosmos DB per funzioni di Azure 2. x e versioni successive

L'associazione di input di Azure Cosmos DB usa l'API SQL per recuperare uno o più documenti di Azure Cosmos DB e li passa al parametro di input della funzione. L'ID documento o i parametri di query possono essere determinati in base al trigger che richiama la funzione.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Se la raccolta è [partizionata](../cosmos-db/partition-data.md#logical-partitions), è necessario che le operazioni di ricerca specifichino anche il valore della chiave di partizione.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Questa sezione contiene gli esempi seguenti:

* [Trigger della coda e ricerca dell'ID da JSON](#queue-trigger-look-up-id-from-json-c)
* [Trigger HTTP e ricerca dell'ID da una stringa di query](#http-trigger-look-up-id-from-query-string-c)
* [Trigger HTTP e ricerca dell'ID dai dati della route](#http-trigger-look-up-id-from-route-data-c)
* [Trigger HTTP e ricerca dell'ID dai dati della route con SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Trigger HTTP e recupero di più documenti con SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Trigger HTTP e recupero di più documenti con DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Gli esempi fanno riferimento a un tipo semplice `ToDoItem`:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Trigger della coda e ricerca dell'ID da JSON 

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che recupera un singolo documento. La funzione viene attivata da un messaggio in coda contenente un oggetto JSON. Il trigger Queue analizza il codice JSON in un oggetto di tipo `ToDoItemLookup` , che contiene l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un `ToDoItem` documento dal database e dalla raccolta specificati.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Trigger HTTP e ricerca dell'ID da una stringa di query

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che utilizza una stringa di query per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un `ToDoItem` documento dal database e dalla raccolta specificati.

>[!NOTE]
>Il parametro di stringa di query HTTP fa distinzione tra maiuscole e minuscole.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Trigger HTTP e ricerca dell'ID dai dati della route

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che usa i dati di route per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un `ToDoItem` documento dal database e dalla raccolta specificati.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Trigger HTTP e ricerca dell'ID dai dati della route con SqlQuery

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che usa i dati della route per specificare l'ID da cercare. Questo ID viene usato per recuperare un documento `ToDoItem` dal database e dalla raccolta specificati.

L'esempio illustra come usare un'espressione di associazione nel parametro `SqlQuery`. Si possono passare i dati della route al parametro `SqlQuery` come illustrato, ma attualmente [non è possibile passare valori di stringhe di query](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Se è necessario eseguire una query solo con l'ID, è consigliabile usare una ricerca, come negli [esempi precedenti](#http-trigger-look-up-id-from-query-string-c), perché utilizzerà meno [unità richiesta](../cosmos-db/request-units.md). Le operazioni di lettura del punto (GET) sono [più efficienti](../cosmos-db/optimize-cost-queries.md) delle query in base all'ID.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Trigger HTTP e recupero di più documenti con SqlQuery

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che recupera un elenco di documenti. La funzione viene attivata da una richiesta HTTP. La query è specificata nella proprietà dell'attributo `SqlQuery`.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Trigger HTTP e recupero di più documenti con DocumentClient

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che recupera un elenco di documenti. La funzione viene attivata da una richiesta HTTP. Il codice usa un'istanza di `DocumentClient` fornita dall'associazione di Azure Cosmos DB per leggere un elenco di documenti. L'istanza di `DocumentClient` potrebbe essere usata anche per operazioni di scrittura.

> [!NOTE]
> È anche possibile usare l'interfaccia [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) per semplificare i test.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Questa sezione contiene gli esempi seguenti:

* [Trigger della coda e ricerca dell'ID dalla stringa](#queue-trigger-look-up-id-from-string-c-script)
* [Trigger della coda e recupero di più documenti con SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Trigger HTTP e ricerca dell'ID da una stringa di query](#http-trigger-look-up-id-from-query-string-c-script)
* [Trigger HTTP e ricerca dell'ID dai dati della route](#http-trigger-look-up-id-from-route-data-c-script)
* [Trigger HTTP e recupero di più documenti con SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Trigger HTTP e recupero di più documenti con DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Gli esempi di trigger HTTP fanno riferimento a un tipo `ToDoItem` semplice:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Trigger della coda e ricerca dell'ID dalla stringa

L'esempio seguente mostra un'associazione di input di Cosmos DB in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione legge un singolo documento e aggiorna il relativo valore di testo.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Trigger della coda e recupero di più documenti con SqlQuery

L'esempio seguente mostra un'associazione di input di Azure Cosmos DB in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione recupera più documenti specificati da una query SQL mediante un trigger di coda per personalizzare i parametri di query.

Il trigger di coda fornisce un parametro `departmentId`. Un messaggio nella coda di `{ "departmentId" : "Finance" }` restituirà tutti i record per il reparto finanziario.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C#:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Trigger HTTP e ricerca dell'ID da una stringa di query

L'esempio seguente illustra una [funzione script C#](functions-reference-csharp.md) che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che utilizza una stringa di query per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un `ToDoItem` documento dal database e dalla raccolta specificati.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Ecco il codice script C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Trigger HTTP e ricerca dell'ID dai dati della route

L'esempio seguente illustra una [funzione script C#](functions-reference-csharp.md) che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che usa i dati di route per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un `ToDoItem` documento dal database e dalla raccolta specificati.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Ecco il codice script C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Trigger HTTP e recupero di più documenti con SqlQuery

L'esempio seguente illustra una [funzione script C#](functions-reference-csharp.md) che recupera un elenco di documenti. La funzione viene attivata da una richiesta HTTP. La query è specificata nella proprietà dell'attributo `SqlQuery`.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Ecco il codice script C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Trigger HTTP e recupero di più documenti con DocumentClient

L'esempio seguente illustra una [funzione script C#](functions-reference-csharp.md) che recupera un elenco di documenti. La funzione viene attivata da una richiesta HTTP. Il codice usa un'istanza di `DocumentClient` fornita dall'associazione di Azure Cosmos DB per leggere un elenco di documenti. L'istanza di `DocumentClient` potrebbe essere usata anche per operazioni di scrittura.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Ecco il codice script C#:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Questa sezione contiene gli esempi seguenti per leggere un singolo documento specificando un valore di ID da varie origini:

* [Trigger della coda e ricerca dell'ID da JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Trigger HTTP e ricerca dell'ID da una stringa di query](#http-trigger-look-up-id-from-query-string-javascript)
* [Trigger HTTP e ricerca dell'ID dai dati della route](#http-trigger-look-up-id-from-route-data-javascript)
* [Trigger della coda e recupero di più documenti con SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Trigger della coda e ricerca dell'ID da JSON

L'esempio seguente mostra un'associazione di input di Cosmos DB in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione legge un singolo documento e aggiorna il relativo valore di testo.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Trigger HTTP e ricerca dell'ID da una stringa di query

L'esempio seguente illustra una [funzione JavaScript](functions-reference-node.md) che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che utilizza una stringa di query per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un `ToDoItem` documento dal database e dalla raccolta specificati.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Trigger HTTP e ricerca dell'ID dai dati della route

L'esempio seguente illustra una [funzione JavaScript](functions-reference-node.md) che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che usa i dati di route per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un `ToDoItem` documento dal database e dalla raccolta specificati.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Trigger della coda e recupero di più documenti con SqlQuery

L'esempio seguente mostra un'associazione di input di Azure Cosmos DB in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione recupera più documenti specificati da una query SQL mediante un trigger di coda per personalizzare i parametri di query.

Il trigger di coda fornisce un parametro `departmentId`. Un messaggio nella coda di `{ "departmentId" : "Finance" }` restituirà tutti i record per il reparto finanziario.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice JavaScript:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Questa sezione contiene gli esempi seguenti per leggere un singolo documento specificando un valore di ID da varie origini:

* [Trigger della coda e ricerca dell'ID da JSON](#queue-trigger-look-up-id-from-json-python)
* [Trigger HTTP e ricerca dell'ID da una stringa di query](#http-trigger-look-up-id-from-query-string-python)
* [Trigger HTTP e ricerca dell'ID dai dati della route](#http-trigger-look-up-id-from-route-data-python)
* [Trigger della coda e recupero di più documenti con SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Trigger della coda e ricerca dell'ID da JSON

L'esempio seguente mostra un'associazione di input di Cosmos DB in un file *function.json* e una [funzione Python](functions-reference-python.md) che usa l'associazione. La funzione legge un singolo documento e aggiorna il relativo valore di testo.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice Python:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Trigger HTTP e ricerca dell'ID da una stringa di query

L'esempio seguente illustra una [funzione Python](functions-reference-python.md) che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che utilizza una stringa di query per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un `ToDoItem` documento dal database e dalla raccolta specificati.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Trigger HTTP e ricerca dell'ID dai dati della route

L'esempio seguente illustra una [funzione Python](functions-reference-python.md) che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che usa i dati di route per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un `ToDoItem` documento dal database e dalla raccolta specificati.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Trigger della coda e recupero di più documenti con SqlQuery

L'esempio seguente mostra un'associazione di input di Azure Cosmos DB in un file *function.json* e una [funzione Python](functions-reference-python.md) che usa l'associazione. La funzione recupera più documenti specificati da una query SQL mediante un trigger di coda per personalizzare i parametri di query.

Il trigger di coda fornisce un parametro `departmentId`. Un messaggio nella coda di `{ "departmentId" : "Finance" }` restituirà tutti i record per il reparto finanziario.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Questa sezione contiene gli esempi seguenti:

* [Trigger HTTP e ricerca dell'ID da una stringa di query - Parametro String](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Trigger HTTP e ricerca dell'ID da una stringa di query - Parametro POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Trigger HTTP e ricerca dell'ID dai dati della route](#http-trigger-look-up-id-from-route-data-java)
* [Trigger HTTP e ricerca dell'ID dai dati della route con SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP trigger e recupero di più documenti dai dati della route con SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Gli esempi fanno riferimento a un tipo semplice `ToDoItem`:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>Trigger HTTP e ricerca dell'ID da una stringa di query - Parametro String

L'esempio seguente illustra una funzione Java che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che utilizza una stringa di query per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un documento dal database e dalla raccolta specificati, in formato stringa.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare l'annotazione `@CosmosDBInput` per i parametri di funzione il cui valore deriva da Cosmos DB.  Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Trigger HTTP e ricerca dell'ID da una stringa di query - Parametro POJO

L'esempio seguente illustra una funzione Java che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che utilizza una stringa di query per specificare l'ID e il valore della chiave di partizione da ricercare. ID e valore della chiave di partizione utilizzati per recuperare un documento dal database e dalla raccolta specificati. Il documento viene quindi convertito in un'istanza del ```ToDoItem``` POJO precedentemente creato e passato come argomento alla funzione.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Trigger HTTP e ricerca dell'ID dai dati della route

L'esempio seguente illustra una funzione Java che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che usa un parametro di route per specificare l'ID e il valore della chiave di partizione da ricercare. L'ID e il valore della chiave di partizione vengono utilizzati per recuperare un documento dal database e dalla raccolta specificati, restituendo come ```Optional<String>``` .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Trigger HTTP e ricerca dell'ID dai dati della route con SqlQuery

L'esempio seguente illustra una funzione Java che recupera un singolo documento. La funzione viene attivata da una richiesta HTTP che usa un parametro di route per specificare l'ID da ricercare. Questo ID viene usato per recuperare un documento dal database e dalla raccolta specificati, convertendo il set di risultati in ```ToDoItem[]```, dato che possono essere restituiti molti documenti, a seconda dei criteri di query.

> [!NOTE]
> Se è necessario eseguire una query solo con l'ID, è consigliabile usare una ricerca, come negli [esempi precedenti](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), perché utilizzerà meno [unità richiesta](../cosmos-db/request-units.md). Le operazioni di lettura del punto (GET) sono [più efficienti](../cosmos-db/optimize-cost-queries.md) delle query in base all'ID.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP trigger e recupero di più documenti dai dati della route con SqlQuery

Nell'esempio seguente viene illustrata una funzione Java che recupera più documenti. La funzione viene attivata da una richiesta HTTP che usa un parametro di route ```desc``` per specificare la stringa da cercare nel ```description``` campo. Il termine di ricerca viene usato per recuperare una raccolta di documenti dal database e dalla raccolta specificati, convertendo il set di risultati in ```ToDoItem[]``` e passandolo come argomento alla funzione.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

Il costruttore dell'attributo accetta il nome del database e il nome della raccolta. Per informazioni su tali impostazioni e altre proprietà che è possibile configurare, vedere la [sezione seguente relativa alla configurazione](#configuration).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Dalla [libreria di runtime di funzioni Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime), usare l' `@CosmosDBOutput` annotazione sui parametri che scrivono in Cosmos DB. Il tipo di parametro dell'annotazione deve essere `OutputBinding<T>` , dove `T` è un tipo Java nativo o un POJO.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `CosmosDB`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type**     | n/d | Il valore deve essere impostato su `cosmosDB`.        |
|**direction**     | n/d | Il valore deve essere impostato su `in`.         |
|**nome**     | n/d | Nome del parametro di binding che rappresenta il documento nella funzione.  |
|**databaseName** |**DatabaseName** |Database che contiene il documento.        |
|**collectionName** |**CollectionName** | Nome della raccolta che contiene il documento. |
|**id**    | **Id** | ID del documento da recuperare. Questa proprietà supporta le [espressioni di associazione](./functions-bindings-expressions-patterns.md). Non impostare entrambe le `id` proprietà e **sqlQuery** . Se non si imposta una delle due proprietà, verrà recuperato l'intera raccolta. |
|**sqlQuery**  |**SqlQuery**  | Query SQL di Azure Cosmos DB usata per recuperare più documenti. La proprietà supporta le associazioni del runtime, come nell'esempio seguente: `SELECT * FROM c where c.departmentId = {departmentId}`. Non impostare entrambe le `id` `sqlQuery` proprietà e. Se non si imposta una delle due proprietà, verrà recuperato l'intera raccolta.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Nome dell'impostazione app contenente la stringa di connessione di Azure Cosmos DB. |
|**partitionKey**|**PartitionKey**|Specifica il valore della chiave di partizione per la ricerca. Può includere i parametri di associazione. È necessario per le ricerche nelle raccolte [partizionate](../cosmos-db/partition-data.md#logical-partitions) .|
|**preferredLocations**| **PreferredLocations**| Opzionale Definisce le posizioni preferite (aree) per gli account di database con replica geografica nel servizio Azure Cosmos DB. I valori devono essere separati da virgole. Ad esempio, "Stati Uniti orientali, Stati Uniti centro-meridionali, Europa settentrionale". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Quando la funzione viene chiusa correttamente, tutte le modifiche apportate al documento di input tramite parametri di input denominati vengono rese automaticamente permanente.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Quando la funzione viene chiusa correttamente, tutte le modifiche apportate al documento di input tramite parametri di input denominati vengono rese automaticamente permanente.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli aggiornamenti non vengono eseguiti automaticamente alla chiusura della funzione. Per eseguire gli aggiornamenti usare invece `context.bindings.<documentName>In` e `context.bindings.<documentName>Out`. Vedere l'esempio JavaScript.

# <a name="python"></a>[Python](#tab/python)

I dati vengono resi disponibili per la funzione tramite un `DocumentList` parametro. Le modifiche apportate al documento non vengono rese automaticamente permanente.

# <a name="java"></a>[Java](#tab/java)

Dalla [libreria di runtime di funzioni Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime), l' [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) annotazione espone Cosmos DB dati alla funzione. Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

---

## <a name="next-steps"></a>Passaggi successivi

- [Esegui una funzione quando viene creato o modificato un documento Azure Cosmos DB (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Salvare le modifiche apportate a un documento Azure Cosmos DB (associazione di output)](./functions-bindings-cosmosdb-v2-output.md)
