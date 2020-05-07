---
title: Binding di output Azure Cosmos DB per functions 2. x
description: Informazioni su come usare l'associazione di output Azure Cosmos DB in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 743bd21a4fd974654760402a639c661fe086d2d5
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735012"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Binding di output Azure Cosmos DB per funzioni di Azure 2. x

L'associazione di output di Azure Cosmos DB consente di scrivere un nuovo documento in un database di Azure Cosmos DB tramite l'API SQL.

Per informazioni sui dettagli di configurazione e configurazione, vedere la [Panoramica](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Questa sezione contiene gli esempi seguenti:

* [Trigger della coda e scrittura di un documento](#queue-trigger-write-one-doc-c)
* [Trigger della coda e scrittura di documenti con IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Gli esempi fanno riferimento a un tipo semplice `ToDoItem`:

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Trigger della coda e scrittura di un documento

L'esempio seguente mostra una [funzione in C#](functions-dotnet-class-library.md) che aggiunge un documento a un database usando i dati forniti nel messaggio dall'archiviazione code.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Trigger della coda e scrittura di documenti con IAsyncCollector

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che aggiunge una raccolta di documenti a un database usando i dati forniti nel codice JSON di un messaggio in coda.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Questa sezione contiene gli esempi seguenti:

* [Trigger della coda e scrittura di un documento](#queue-trigger-write-one-doc-c-script)
* [Trigger della coda e scrittura di documenti con IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Trigger della coda e scrittura di un documento

L'esempio seguente mostra un'associazione di output di Azure Cosmos DB in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione usa un'associazione di input di coda per una coda che riceve JSON nel formato seguente:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La funzione crea documenti di Azure Cosmos DB nel formato seguente per ogni record:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Trigger della coda e scrittura di documenti con IAsyncCollector

Per creare più documenti, è possibile definire l'associazione a `ICollector<T>` o `IAsyncCollector<T>` dove `T` è uno dei tipi supportati.

Questo esempio si riferisce a un tipo `ToDoItem` semplice:

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

Ecco il file function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Ecco il codice script C#:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente mostra un'associazione di output di Azure Cosmos DB in un file *function.json* e una [funzione script JavaScript](functions-reference-node.md) che usa l'associazione. La funzione usa un'associazione di input di coda per una coda che riceve JSON nel formato seguente:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La funzione crea documenti di Azure Cosmos DB nel formato seguente per ogni record:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice JavaScript:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrato come scrivere un documento in un database CosmosDB di Azure come output di una funzione.

La definizione di associazione è definita in *Function. JSON,* dove *Type* è `cosmosDB`impostato su.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Per scrivere nel database, passare un oggetto Document al `set` metodo del parametro database.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Trigger della coda e salvataggio di un messaggio nel database tramite il valore restituito](#queue-trigger-save-message-to-database-via-return-value-java)
* [Trigger HTTP e salvataggio di un documento nel database tramite il valore restituito](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Trigger HTTP e salvataggio di un documento nel database tramite OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Trigger HTTP e salvataggio di più documenti nel database tramite OutputBinding ](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Trigger della coda e salvataggio di un messaggio nel database tramite il valore restituito

L'esempio seguente mostra una funzione Java che aggiunge un documento a un database con i dati da un messaggio dall'archiviazione code.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Trigger HTTP e salvataggio di un documento nel database tramite il valore restituito

L'esempio seguente mostra una funzione Java la cui firma viene annotata con ```@CosmosDBOutput``` e con valore restituito di tipo ```String```. Il documento JSON restituito dalla funzione verrà scritto automaticamente nella raccolta di Cosmos DB corrispondente.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Trigger HTTP e salvataggio di un documento nel database tramite OutputBinding

L'esempio seguente mostra una funzione Java che scrive un documento in Cosmos DB tramite un parametro di output ```OutputBinding<T>```. In questo esempio, il ```outputItem``` parametro deve essere annotato con ```@CosmosDBOutput```, non con la firma della funzione. Usando ```OutputBinding<T>```, la funzione può sfruttare i vantaggi dell'associazione per scrivere il documento in Cosmos DB, consentendo al tempo stesso la restituzione di un valore diverso al chiamante della funzione, ad esempio un documento JSON o XML.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Trigger HTTP e salvataggio di più documenti nel database tramite OutputBinding 

L'esempio seguente mostra una funzione Java che scrive più documenti in Cosmos DB tramite un parametro di output ```OutputBinding<T>```. In questo esempio, il ```outputItem``` parametro viene annotato ```@CosmosDBOutput```con, non con la firma della funzione. Il parametro di output ```outputItem``` include un elenco di oggetti ```ToDoItem``` come tipo di parametro di modello. Usando ```OutputBinding<T>```, la funzione può sfruttare i vantaggi dell'associazione per scrivere i documenti in Cosmos DB, consentendo al tempo stesso la restituzione di un valore diverso al chiamante della funzione, ad esempio un documento JSON o XML.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare l'annotazione `@CosmosDBOutput` per i parametri che saranno scritti su Cosmos DB.  Il tipo di parametro di annotazione deve essere ```OutputBinding<T>```, dove T è un tipo nativo di Java o un oggetto POJO.

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests).

Il costruttore dell'attributo accetta il nome del database e il nome della raccolta. Per informazioni su tali impostazioni e altre proprietà che è possibile configurare, vedere [Output - configurazione](#configuration). Di seguito è riportato un esempio dell'attributo `CosmosDB` in una firma del metodo:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L' `CosmosDBOutput` annotazione è disponibile per la scrittura dei dati Cosmos DB. È possibile applicare l'annotazione alla funzione o a un singolo parametro di funzione. Quando viene usato nel metodo Function, il valore restituito della funzione è quello che viene scritto in Cosmos DB. Se si utilizza l'annotazione con un parametro, il tipo del parametro deve essere dichiarato `OutputBinding<T>` come `T` dove un tipo Java nativo o un POJO.

---

## <a name="configuration"></a>Configurazione

La tabella seguente illustra le proprietà di configurazione dell'associazione impostate nel file *Function. JSON* e nell' `CosmosDB` attributo.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type**     | n/d | Il valore deve essere impostato su `cosmosDB`.        |
|**direzione**     | n/d | Il valore deve essere impostato su `out`.         |
|**name**     | n/d | Nome del parametro di binding che rappresenta il documento nella funzione.  |
|**databaseName** | **DatabaseName**|Database contenente la raccolta in cui viene creato il documento.     |
|**collectionName** |**CollectionName**  | Nome della raccolta in cui viene creato il documento. |
|**createIfNotExists**  |**CreateIfNotExists**    | Valore booleano che indica se la raccolta viene creata quando non esiste. Il valore predefinito è *false* perché le nuove raccolte vengono create con una velocità effettiva riservata, che ha implicazioni in termini di costi. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Quando `CreateIfNotExists` è true, definisce il percorso della chiave di partizione per la raccolta creata.|
|**collectionThroughput**|**CollectionThroughput**| Quando `CreateIfNotExists` è true, definisce la [velocità effettiva](../cosmos-db/set-throughput.md) della raccolta creata.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Nome dell'impostazione app contenente la stringa di connessione di Azure Cosmos DB.        |
|**preferredLocations**| **PreferredLocations**| Opzionale Definisce le posizioni preferite (aree) per gli account di database con replica geografica nel servizio Azure Cosmos DB. I valori devono essere separati da virgole. Ad esempio, "Stati Uniti orientali, Stati Uniti centro-meridionali, Europa settentrionale". |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| Opzionale Quando è impostato `true` su `PreferredLocations`, può sfruttare le [Scritture](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) in più aree nel servizio Azure Cosmos DB. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilizzo

Per impostazione predefinita, quando si scrive nel parametro di output della funzione, viene creato un documento nel database. L'ID di questo documento è un GUID generato automaticamente. È possibile specificare l'ID del documento di output specificando la proprietà `id` nell'oggetto JSON passato al parametro di output.

> [!Note]
> Quando si specifica l'ID di un documento esistente, questo viene sovrascritto dal nuovo documento di output.

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione | Riferimento |
|---|---|
| Cosmos DB | [Codici di errore di CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>impostazioni host.json

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione nella versione 2.x. Per altre informazioni sulle impostazioni di configurazione globali nella versione 2.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure versione 2.x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|GatewayMode|Gateway|La modalità di connessione usata dalla funzione durante la connessione al servizio di Azure Cosmos DB. Le opzioni sono `Direct` e `Gateway`|
|Protocollo|Https|Il protocollo di connessione usato dalla funzione durante la connessione al servizio di Azure Cosmos DB.  Leggere [qui per una spiegazione di entrambe le modalità](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/d|Prefisso di lease da usare in tutte le funzioni in un'app.|

## <a name="next-steps"></a>Passaggi successivi

- [Esegui una funzione quando viene creato o modificato un documento Azure Cosmos DB (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Leggere un documento di Azure Cosmos DB (associazione di input)](./functions-bindings-cosmosdb-v2-input.md)