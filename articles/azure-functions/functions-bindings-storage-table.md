---
title: Associazioni di Archiviazione tabelle di Azure per Funzioni di Azure
description: Informazioni su come usare le associazioni di archiviazione tabelle di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: e5bee65677b85b729a38ce3b902687cfbdb989da
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345906"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Associazioni di Archiviazione tabelle di Azure per Funzioni di Azure

Questo articolo illustra come operare con le associazioni dell'archiviazione tabelle di Azure in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per l'archiviazione tabelle di Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Le associazioni di archiviazione tabelle sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

Le associazioni di archiviazione tabelle sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Storage/Table).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="input"></a>Input

Usare l'associazione di input dell'archiviazione tabelle di Azure per leggere una tabella in un account di archiviazione di Azure.

## <a name="input---example"></a>Input - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C# - lettura di un'entità](#input---c-example---one-entity)
* [C# - associazione a IQueryable](#input---c-example---iqueryable)
* [C# - associazione a CloudTable](#input---c-example---cloudtable)
* [Script C# - lettura di un'entità](#input---c-script-example---one-entity)
* [Script C# - associazione a IQueryable](#input---c-script-example---iqueryable)
* [Script C# - associazione a CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example---one-entity"></a>Input - Esempio C# - Un'entità

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che legge una singola riga della tabella. 

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
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example---iqueryable"></a>Input - Esempio C# - IQueryable

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che legge più righe della tabella. Si noti che la classe `MyPoco` deriva da `TableEntity`.

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
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="input---c-example---cloudtable"></a>Input - Esempio C# - CloudTable

Il metodo `IQueryable` non è supportato nel [runtime di Funzioni v2](functions-versions.md). In alternativa, è possibile usare un parametro del metodo `CloudTable` per leggere la tabella tramite Azure Storage SDK. Di seguito è riportato un esempio di una funzione 2.x che esegue una query su una tabella di log di Funzioni di Azure:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
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
            TraceWriter log)
        {
            log.Info($"C# Timer trigger function executed at: {DateTime.Now}");

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
                log.Info(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Per altre informazioni su come usare CloudTable, vedere [Introduzione all'archiviazione tabelle di Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se si prova a eseguire l'associazione a `CloudTable` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Input - Esempio di script C# - Un'entità

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
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---iqueryable"></a>Input - Esempio di script C# - IQueryable

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

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---cloudtable"></a>Input - Esempio di script C# - CloudTable

Il metodo `IQueryable` non è supportato nel [runtime di Funzioni v2](functions-versions.md). In alternativa, è possibile usare un parametro del metodo `CloudTable` per leggere la tabella tramite Azure Storage SDK. Di seguito è riportato un esempio di una funzione 2.x che esegue una query su una tabella di log di Funzioni di Azure:

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

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");

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
        log.Info(
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

### <a name="input---f-example"></a>Input - esempio in F#

L'esempio seguente illustra un'associazione di input della tabella in un file *function.json* e codice [script F#](functions-reference-fsharp.md) che usa l'associazione. La funzione usa un trigger della coda per leggere una riga della tabella. 

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

Ecco il codice F#:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Input - esempio JavaScript

L'esempio seguente illustra un'associazione di input della tabella in un file *function.json* e [codice JavaScript] (functions-reference-node.md) che usa l'associazione. La funzione usa un trigger della coda per leggere una riga della tabella. 

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

## <a name="input---attributes"></a>Input - attributi
 
Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare gli attributi seguenti per configurare un'associazione di input per la tabella:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)

  Il costruttore dell'attributo accetta il nome della tabella, una chiave di partizione e una chiave di riga. Può essere usato su un parametro out o sul valore restituito della funzione, come illustrato nell'esempio seguente:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
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
      TraceWriter log)
  {
      ...
  }
  ```

  Per un esempio completo, vedere [Input - esempio in C#](#input---c-example).

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

## <a name="input---configuration"></a>Input - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Table`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `table`. Questa proprietà viene impostata automaticamente quando si crea l'associazione nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea l'associazione nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta la tabella o l'entità nel codice della funzione. | 
|**tableName** | **TableName** | Nome della tabella.| 
|**partitionKey** | **PartitionKey** |facoltativo. Chiave di partizione dell'entità della tabella da leggere. Vedere la sezione [usage](#input---usage) per indicazioni sull'uso di questa proprietà.| 
|**rowKey** |**RowKey** | facoltativo. Chiave di riga dell'entità della tabella da leggere. Vedere la sezione [usage](#input---usage) per indicazioni sull'uso di questa proprietà.| 
|**take** |**Take** | facoltativo. Numero massimo di entità da leggere in JavaScript. Vedere la sezione [usage](#input---usage) per indicazioni sull'uso di questa proprietà.| 
|**filter** |**Filter** | facoltativo. Espressione di filtro OData per l'input della tabella in JavaScript. Vedere la sezione [usage](#input---usage) per indicazioni sull'uso di questa proprietà.| 
|**connessione** |**Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Input - uso

L'associazione di input dell'archiviazione tabelle supporta gli scenari seguenti:

* **Leggere una riga in C# o script C#**

  Impostare `partitionKey` e `rowKey`. Accedere ai dati della tabella con un parametro di metodo `T <paramName>`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. `T` in genere è un tipo che implementa `ITableEntity` o deriva da `TableEntity`. Le proprietà `filter` e `take` non vengono usate in questo scenario. 

* **Leggere una o più righe in C# o script C#**

  Accedere ai dati della tabella con un parametro di metodo `IQueryable<T> <paramName>`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. `T` deve essere un tipo che implementa `ITableEntity` o deriva da `TableEntity`. È possibile usare metodi `IQueryable` per eseguire eventuali filtri richiesti. Le proprietà `partitionKey`, `rowKey`, `filter` e `take` non vengono usate in questo scenario.  

  > [!NOTE]
  > Il metodo `IQueryable` non è supportato nel [runtime di Funzioni v2](functions-versions.md). In alternativa, è possibile [usare un parametro del metodo paramName di CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) per leggere la tabella tramite Azure Storage SDK. Se si prova a eseguire l'associazione a `CloudTable` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

* **Leggere una o più righe in JavaScript**

  Impostare le proprietà `filter` e `take`. Non impostare `partitionKey` o `rowKey`. È possibile accedere all'entità (o alle entità) della tabella di input usando `context.bindings.<name>`. Gli oggetti deserializzati hanno le proprietà `RowKey` e `PartitionKey`.

## <a name="output"></a>Output

Usare un'associazione di output dell'archiviazione tabelle di Azure per scrivere entità in una tabella in un account di archiviazione di Azure.

> [!NOTE]
> L'associazione di output non supporta l'aggiornamento di entità esistenti. Per aggiornare un'entità esistente, usare l'operazione `TableOperation.Replace` [da Azure Storage SDK](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity).   

## <a name="output---example"></a>Output - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#output---c-example)
* [Script C# (file con estensione csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Output - esempio in C#

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
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Output - esempio di script C#

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
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
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

### <a name="output---f-example"></a>Output - esempio in F#

L'esempio seguente illustra un'associazione di output della tabella in un file *function.json* e codice [script F#](functions-reference-fsharp.md) che usa l'associazione. La funzione scrive più entità della tabella.

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

Ecco il codice F#:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Output - esempio JavaScript

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

## <a name="output---attributes"></a>Output - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs).

Il costruttore dell'attributo accetta il nome della tabella. Può essere usato su un parametro `out` o sul valore restituito della funzione, come illustrato nell'esempio seguente:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
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
    TraceWriter log)
{
    ...
}
```

Per un esempio completo, vedere [Output - esempio in C#](#output---c-example).

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere [Input - attributi](#input---attributes).

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Table`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `table`. Questa proprietà viene impostata automaticamente quando si crea l'associazione nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `out`. Questa proprietà viene impostata automaticamente quando si crea l'associazione nel portale di Azure. |
|**nome** | n/d | Nome della variabile usato nel codice della funzione che rappresenta la tabella o l'entità. Impostare su `$return` per fare riferimento al valore restituito della funzione.| 
|**tableName** |**TableName** | Nome della tabella.| 
|**partitionKey** |**PartitionKey** | Chiave di partizione dell'entità della tabella da scrivere. Vedere la sezione [usage](#output---usage) per indicazioni sull'uso di questa proprietà.| 
|**rowKey** |**RowKey** | Chiave di riga dell'entità della tabella da scrivere. Vedere la sezione [usage](#output---usage) per indicazioni sull'uso di questa proprietà.| 
|**connessione** |**Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Output - uso

L'associazione di output dell'archiviazione tabelle supporta gli scenari seguenti:

* **Scrivere una riga in qualsiasi linguaggio**

  In C# e negli script C# è possibile accedere all'entità della tabella di output con un parametro di metodo, ad esempio `out T paramName`, o il valore restituito della funzione. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. `T` può essere qualsiasi tipo serializzabile, se la chiave di partizione e la chiave di riga vengono fornite dal file *function.json* o dall'attributo `Table`. In caso contrario, `T` deve essere un tipo che include le proprietà `PartitionKey` e `RowKey`. In questo scenario, `T` in genere, ma non necessariamente, implementa `ITableEntity` o deriva da `TableEntity`.

* **Scrivere una o più righe in C# o script C#**

  In C# e negli script C# è possibile accedere all'entità della tabella di output con un parametro di metodo `ICollector<T> paramName` o `IAsyncCollector<T> paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. `T` specifica lo schema delle entità da aggiungere. In genere, ma non necessariamente, `T` deriva da `TableEntity` o implementa `ITableEntity`. I valori della chiave di partizione e della chiave di riga in *function.json* o nel costruttore dell'attributo `Table` non vengono usati in questo scenario.

  In alternativa, è possibile usare un parametro del metodo `CloudTable` per scrivere nella tabella tramite Azure Storage SDK. Se si prova a eseguire l'associazione a `CloudTable` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x). Per un esempio di codice associato a `CloudTable`, vedere gli esempi di associazione di input per [C#](#input---c-example---cloudtable) o [script C#](#input---c-script-example---cloudtable) descritti in precedenza in questo articolo.

* **Scrivere una o più righe in JavaScript**

  Nelle funzioni JavaScript è possibile accedere all'output della tabella usando `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione | riferimento |
|---|---|
| Tabella | [Codici di errore del servizio tabelle](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Table, Queue | [Codici di errore di archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue | [Risoluzione dei problemi](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
