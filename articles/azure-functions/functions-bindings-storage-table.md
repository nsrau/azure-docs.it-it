---
title: Associazioni di Archiviazione tabelle di Azure per Funzioni di Azure
description: Informazioni su come usare le associazioni di archiviazione tabelle di Azure in Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 1aa3537679ee37cbc6085344d2f31ae4043d32bb
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520667"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Associazioni di Archiviazione tabelle di Azure per Funzioni di Azure

Questo articolo illustra come operare con le associazioni dell'archiviazione tabelle di Azure in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per l'archiviazione tabelle di Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Le associazioni di archiviazione tabelle sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacchetti - Funzioni 2.x e successive

Le associazioni di archiviazione tabelle sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input

Usare l'associazione di input dell'archiviazione tabelle di Azure per leggere una tabella in un account di archiviazione di Azure.

# <a name="c"></a>[C #](#tab/csharp)

### <a name="one-entity"></a>Un'entità

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che legge una singola riga della tabella. Per ogni record inserito nella tabella, la funzione verrà attivata.

Il valore della chiave della riga "{queueTrigger}" indica che la chiave della riga proviene dalla stringa di messaggio della coda.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

Nell'esempio riportato di seguito viene illustrata `MyPoco` una funzione `TableEntity` [di C,](functions-dotnet-class-library.md) che legge più righe di tabella in cui la classe deriva da .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

Il metodo `IQueryable` non è supportato nel [runtime di Funzioni v2](functions-versions.md). In alternativa, è possibile usare un parametro del metodo `CloudTable` per leggere la tabella tramite Azure Storage SDK. Ecco un esempio di una funzione che esegue una query su una tabella di log di Funzioni di Azure:Here's an example of a function that queries an Azure Functions log table:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Per altre informazioni su come usare CloudTable, vedere [Introduzione all'archiviazione tabelle di Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se si prova a eseguire l'associazione a `CloudTable` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

### <a name="one-entity"></a>Un'entità

L'esempio seguente illustra un'associazione di input della tabella in un file *function.json* e codice [script C#](functions-reference-csharp.md) che usa l'associazione. La funzione usa un trigger della coda per leggere una riga della tabella. 

Il file *function.json* specifica `partitionKey` e `rowKey`. Il valore `rowKey` "{queueTrigger}" indica che la chiave della riga proviene dalla stringa di messaggio della coda.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Queste proprietà sono descritte nella sezione [configuration](#input---configuration).

Ecco il codice script C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

L'esempio seguente illustra un'associazione di input della tabella in un file *function.json* e codice [script C#](functions-reference-csharp.md) che usa l'associazione. La funzione legge le entità per una chiave di partizione specificata in un messaggio della coda.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Queste proprietà sono descritte nella sezione [configuration](#input---configuration).

Il codice script C# aggiunge un riferimento ad Azure Storage SDK in modo che il tipo di entità possa derivare da `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable`non è supportato nel runtime di Funzioni per le [versioni 2.x e successive)](functions-versions.md). In alternativa, è possibile usare un parametro del metodo `CloudTable` per leggere la tabella tramite Azure Storage SDK. Ecco un esempio di una funzione che esegue una query su una tabella di log di Funzioni di Azure:Here's an example of a function that queries an Azure Functions log table:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Per altre informazioni su come usare CloudTable, vedere [Introduzione all'archiviazione tabelle di Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se si prova a eseguire l'associazione a `CloudTable` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente illustra un'associazione di input della tabella in un file *function.json* e codice [JavaScript](functions-reference-node.md) che usa l'associazione. La funzione usa un trigger della coda per leggere una riga della tabella. 

Il file *function.json* specifica `partitionKey` e `rowKey`. Il valore `rowKey` "{queueTrigger}" indica che la chiave della riga proviene dalla stringa di messaggio della coda.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Queste proprietà sono descritte nella sezione [configuration](#input---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Riga di tabella singola 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

Nell'esempio seguente viene illustrata una funzione attivata HTTP che restituisce un elenco di oggetti person che si trovano in una partizione specificata nell'archivio tabelle. Nell'esempio, la chiave di partizione viene estratta dalla route http e tableName e connection provengono dalle impostazioni della funzione. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

L'annotazione TableInput può anche estrarre le associazioni dal corpo json della richiesta, come illustrato nell'esempio seguente.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Gli esempi seguenti usano il filtro per eseguire una query per le persone con un nome specifico in una tabella di Azure e limitano il numero di possibili corrispondenze a 10 risultati.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Input - attributi e annotazioni

# <a name="c"></a>[C #](#tab/csharp)

 Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare gli attributi seguenti per configurare un'associazione di input per la tabella:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Il costruttore dell'attributo accetta il nome della tabella, una chiave di partizione e una chiave di riga. L'attributo può `out` essere utilizzato su un parametro o sul valore restituito della funzione, come illustrato nell'esempio seguente:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  È possibile impostare la proprietà `Connection` per specificare l'account di archiviazione da usare, come illustrato nell'esempio seguente:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Per un esempio completo, vedere Input - esempio in C#.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Offre un altro modo per specificare l'account di archiviazione da usare. Il costruttore accetta il nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. L'attributo può essere applicato a livello di parametro, metodo o classe. L'esempio seguente illustra il livello classe e il livello metodo:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

L'account di archiviazione da usare è determinato nell'ordine seguente:

* La proprietà `Connection` dell'attributo `Table`.
* L'attributo `StorageAccount` applicato allo stesso parametro dell'attributo `Table`.
* L'attributo `StorageAccount` applicato alla funzione.
* L'attributo `StorageAccount` applicato alla classe.
* L'account di archiviazione predefinito per l'app per le funzioni (impostazione dell'app "AzureWebJobsStorage").

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script di C.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare `@TableInput` l'annotazione per i parametri il cui valore deriva dall’archiviazione tabelle.  Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

---

## <a name="input---configuration"></a>Input - configurazione

Nella tabella seguente vengono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Table` .

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `table`. Questa proprietà viene impostata automaticamente quando si crea l'associazione nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea l'associazione nel portale di Azure. |
|**name** | n/d | Nome della variabile che rappresenta la tabella o l'entità nel codice della funzione. | 
|**Tablename** | **Tablename** | Nome della tabella.| 
|**partitionKey (chiave)** | **PartitionKey** |Facoltativa. Chiave di partizione dell'entità della tabella da leggere. Vedere la sezione [relativa all'utilizzo](#input---usage) per istruzioni su come utilizzare questa proprietà.| 
|**rowKey (chiave di riga)** |**RowKey** | Facoltativa. Chiave di riga dell'entità della tabella da leggere. Vedere la sezione [relativa all'utilizzo](#input---usage) per istruzioni su come utilizzare questa proprietà.| 
|**take** |**Prendere** | Facoltativa. Numero massimo di entità da leggere in JavaScript. Vedere la sezione [relativa all'utilizzo](#input---usage) per istruzioni su come utilizzare questa proprietà.| 
|**ﬁltro** |**Filtro** | Facoltativa. Espressione di filtro OData per l'input della tabella in JavaScript. Vedere la sezione [relativa all'utilizzo](#input---usage) per istruzioni su come utilizzare questa proprietà.| 
|**Connessione** |**Connessione** | Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. L'impostazione può essere il nome di un'impostazione dell'app con prefisso "AzureWebJobs" o il nome della stringa di connessione. Ad esempio, se il nome dell'impostazione è "AzureWebJobsMyStorage", è possibile specificare "MyStorage" qui. Il runtime di Funzioni cercherà automaticamente un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Input - uso

# <a name="c"></a>[C #](#tab/csharp)

* **Leggere una riga in**

  Impostare `partitionKey` e `rowKey`. Accedere ai dati della tabella con un parametro di metodo `T <paramName>`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. `T` in genere è un tipo che implementa `ITableEntity` o deriva da `TableEntity`. Le proprietà `filter` e `take` non vengono usate in questo scenario.

* **Leggere una o più righe**

  Accedere ai dati della tabella con un parametro di metodo `IQueryable<T> <paramName>`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. `T` deve essere un tipo che implementa `ITableEntity` o deriva da `TableEntity`. È possibile usare metodi `IQueryable` per eseguire eventuali filtri richiesti. Le proprietà `partitionKey`, `rowKey`, `filter` e `take` non vengono usate in questo scenario.  

  > [!NOTE]
  > Il metodo `IQueryable` non è supportato nel [runtime di Funzioni v2](functions-versions.md). In alternativa, è possibile [usare un parametro del metodo paramName di CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) per leggere la tabella tramite Azure Storage SDK. Se si prova a eseguire l'associazione a `CloudTable` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

* **Leggere una riga in**

  Impostare `partitionKey` e `rowKey`. Accedere ai dati della tabella con un parametro di metodo `T <paramName>`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. `T` in genere è un tipo che implementa `ITableEntity` o deriva da `TableEntity`. Le proprietà `filter` e `take` non vengono usate in questo scenario.

* **Leggere una o più righe**

  Accedere ai dati della tabella con un parametro di metodo `IQueryable<T> <paramName>`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. `T` deve essere un tipo che implementa `ITableEntity` o deriva da `TableEntity`. È possibile usare metodi `IQueryable` per eseguire eventuali filtri richiesti. Le proprietà `partitionKey`, `rowKey`, `filter` e `take` non vengono usate in questo scenario.  

  > [!NOTE]
  > Il metodo `IQueryable` non è supportato nel [runtime di Funzioni v2](functions-versions.md). In alternativa, è possibile [usare un parametro del metodo paramName di CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) per leggere la tabella tramite Azure Storage SDK. Se si prova a eseguire l'associazione a `CloudTable` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Impostare le proprietà `filter` e `take`. Non impostare `partitionKey` o `rowKey`. È possibile accedere all'entità (o alle entità) della tabella di input usando `context.bindings.<BINDING_NAME>`. Gli oggetti deserializzati hanno le proprietà `RowKey` e `PartitionKey`.

# <a name="python"></a>[Python](#tab/python)

I dati della tabella vengono passati alla funzione come stringa JSON. Deserializzare il messaggio `json.loads` chiamando come illustrato nell'esempio di input . [example](#input)

# <a name="java"></a>[Java](#tab/java)

L'attributo [TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) consente di accedere alla riga della tabella che ha attivato la funzione.

---

## <a name="output"></a>Output

Usare un'associazione di output dell'archiviazione tabelle di Azure per scrivere entità in una tabella in un account di archiviazione di Azure.

> [!NOTE]
> L'associazione di output non supporta l'aggiornamento di entità esistenti. Per aggiornare un'entità esistente, usare l'operazione `TableOperation.Replace`[da Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity).

# <a name="c"></a>[C #](#tab/csharp)

L'esempio seguente illustra una [funzione C# ](functions-dotnet-class-library.md) che usa un trigger HTTP per scrivere una singola riga della tabella. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente illustra un'associazione di output della tabella in un file *function.json* e codice [script C#](functions-reference-csharp.md) che usa l'associazione. La funzione scrive più entità della tabella.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Queste proprietà sono descritte nella sezione [configuration](#output---configuration).

Ecco il codice script C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente illustra un'associazione di output della tabella in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione scrive più entità della tabella.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Queste proprietà sono descritte nella sezione [configuration](#output---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrato come utilizzare l'associazione di output di archiviazione Table.The following example demonstrates how to use the Table storage output binding. `table` L'associazione viene configurata in *function.json* `partitionKey`assegnando `connection`valori a `name`, `tableName`, e :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

La funzione seguente genera un'interfaccia utente univoca per il `rowKey` valore e rende persistente il messaggio nell'archivio tabelle.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

Nell'esempio seguente viene illustrata una funzione Java che utilizza un trigger HTTP per scrivere una singola riga di tabella.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

Nell'esempio seguente viene illustrata una funzione Java che utilizza un trigger HTTP per scrivere più righe di tabella.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Output - attributi e annotazioni

# <a name="c"></a>[C #](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Il costruttore dell'attributo accetta il nome della tabella. L'attributo può `out` essere utilizzato su un parametro o sul valore restituito della funzione, come illustrato nell'esempio seguente:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

È possibile impostare la proprietà `Connection` per specificare l'account di archiviazione da usare, come illustrato nell'esempio seguente:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [Output - esempio in C#](#output).

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere [Input - attributi](#input---attributes-and-annotations).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script di C.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Nella [libreria](/java/api/overview/azure/functions/runtime)di runtime delle funzioni Java utilizzare l'annotazione [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) sui parametri per scrivere valori nell'archiviazione tabelle.

Vedere [l'esempio per ulteriori dettagli](#output).

---

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente vengono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Table` .

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `table`. Questa proprietà viene impostata automaticamente quando si crea l'associazione nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `out`. Questa proprietà viene impostata automaticamente quando si crea l'associazione nel portale di Azure. |
|**name** | n/d | Nome della variabile usato nel codice della funzione che rappresenta la tabella o l'entità. Impostare su `$return` per fare riferimento al valore restituito della funzione.| 
|**Tablename** |**Tablename** | Nome della tabella.| 
|**partitionKey (chiave)** |**PartitionKey** | Chiave di partizione dell'entità della tabella da scrivere. Vedere la sezione [usage](#output---usage) per indicazioni sull'uso di questa proprietà.| 
|**rowKey (chiave di riga)** |**RowKey** | Chiave di riga dell'entità della tabella da scrivere. Vedere la sezione [usage](#output---usage) per indicazioni sull'uso di questa proprietà.| 
|**Connessione** |**Connessione** | Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se `connection` si imposta "MyStorage", il runtime di Functions cerca un'impostazione dell'app denominata "MyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Output - uso

# <a name="c"></a>[C #](#tab/csharp)

Accedere all'entità della tabella `ICollector<T> paramName` di `IAsyncCollector<T> paramName` `T` output `PartitionKey` utilizzando `RowKey` un parametro del metodo o se include le proprietà e . Queste proprietà sono spesso `ITableEntity` accompagnate `TableEntity`dall'implementazione o dall'ereditarietà di .

In alternativa, è `CloudTable` possibile usare un parametro del metodo per scrivere nella tabella usando Azure Storage SDK.Alternatively you can use a method parameter to write to the table by using the Azure Storage SDK. Se si prova a eseguire l'associazione a `CloudTable` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Accedere all'entità della tabella `ICollector<T> paramName` di `IAsyncCollector<T> paramName` `T` output `PartitionKey` utilizzando `RowKey` un parametro del metodo o se include le proprietà e . Queste proprietà sono spesso `ITableEntity` accompagnate `TableEntity`dall'implementazione o dall'ereditarietà di . Il `paramName` valore è `name` specificato nella proprietà di *function.json*.

In alternativa, è `CloudTable` possibile usare un parametro del metodo per scrivere nella tabella usando Azure Storage SDK.Alternatively you can use a method parameter to write to the table by using the Azure Storage SDK. Se si prova a eseguire l'associazione a `CloudTable` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Accedere all'evento `context.bindings.<name>` di `<name>` output utilizzando where `name` è il valore specificato nella proprietà *di function.json*.

# <a name="python"></a>[Python](#tab/python)

Sono disponibili due opzioni per l'output di un messaggio di riga di archiviazione Tabella da una funzione:There are two options for outputting a Table storage row message from a function:

- **Valore restituito**: `name` impostare la `$return`proprietà in *function.json su* . Con questa configurazione, il valore restituito della funzione viene mantenuto come riga di archiviazione Table.With this configuration, the function's return value is persisted as a Table storage row.

- **Imperative**: Passare un valore al metodo [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) del parametro dichiarato come tipo [Out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Il valore `set` passato a viene mantenuto come messaggio dell'Hub eventi.

# <a name="java"></a>[Java](#tab/java)

Sono disponibili due opzioni per l'output di una riga di archiviazione Table da una funzione tramite l'annotazione [TableStorageOutput:There](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) are two options for outputting a Table storage row from a function by using the TableStorageOutput annotation:

- **Valore restituito**: applicando l'annotazione alla funzione stessa, il valore restituito della funzione viene mantenuto come riga di archiviazione Table.

- **Imperative**: per impostare in modo esplicito il valore [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)del `T` messaggio, applicare l'annotazione a un parametro specifico del tipo , in cui include le `PartitionKey` proprietà e `RowKey` . Queste proprietà sono spesso `ITableEntity` accompagnate `TableEntity`dall'implementazione o dall'ereditarietà di .

---

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione | Informazioni di riferimento |
|---|---|
| Tabella | [Codici di errore del servizio tabelle](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Table, Queue | [Codici di errore di archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue | [Risoluzione dei problemi](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
