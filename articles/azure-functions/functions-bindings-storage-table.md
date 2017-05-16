---
title: Associazioni della tabella di archiviazione di Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare le associazioni di Archiviazione di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: bb01be3ee044f60376e0c9c2de7b3dd34f3b7aca
ms.lasthandoff: 04/25/2017


---
# <a name="azure-functions-storage-table-bindings"></a>Associazioni della tabella di archiviazione di Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice delle associazioni delle tabelle di Archiviazione di Azure in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per le tabelle di Archiviazione di Azure.

L'associazione delle tabelle di archiviazione supporta gli scenari seguenti:

* **Leggere una singola riga in una funzione C# o Node.js**: impostare `partitionKey` e `rowKey`. Le proprietà `filter` e `take` non vengono usate in questo scenario.
* **Leggere più righe in una funzione C#**: il runtime di Funzioni specifica un oggetto `IQueryable<T>` associato alla tabella. Il tipo `T` deve derivare da `TableEntity` o implementare `ITableEntity`. Le proprietà `partitionKey`, `rowKey`, `filter` e `take` non vengono usate in questo scenario. È possibile usare l'oggetto `IQueryable` per qualsiasi operazione di filtro necessaria. 
* **Leggere più righe in una funzione Node**: impostare le proprietà `filter` e `take`. Non impostare `partitionKey` o `rowKey`.
* **Scrivere una o più righe in una funzione C#**: il runtime di Funzioni specifica un oggetto `ICollector<T>` o `IAsyncCollector<T>` associato alla tabella, dove `T` indica lo schema delle entità da aggiungere. In genere, ma non necessariamente, il tipo `T` deriva da `TableEntity` o implementa `ITableEntity`. Le proprietà `partitionKey`, `rowKey`, `filter` e `take` non vengono usate in questo scenario.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Associazione di input della tabella di archiviazione
L'associazione di input nella tabella di archiviazione di Azure consente di usare una tabella di archiviazione nella funzione. 

L'input della tabella di archiviazione in una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

Tenere presente quanto segue: 

* Usare `partitionKey` e `rowKey` insieme per leggere una singola entità. Queste proprietà sono facoltative. 
* `connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integra** configura automaticamente questa impostazione app quando si crea un account di archiviazione o si seleziona un account già esistente. È anche possibile [configurare questa impostazione dell'app manualmente](functions-how-to-use-azure-function-app-settings.md#settings).  

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso dell'input
Nelle funzioni C# l'associazione all'entità, o alle entità, della tabella di input viene eseguita usando un parametro denominato nella firma funzione, ad esempio `<T> <name>`.
`T` è il tipo di dati in cui si vogliono deserializzare i dati e `paramName` è il nome specificato nell'[associazione di input](#input). Nelle funzioni Node.js si accede all'entità (o alle entità) della tabella di input usando `context.bindings.<name>`.

I dati di input possono essere deserializzati in funzioni Node.js o C#. Gli oggetti deserializzati hanno le proprietà `RowKey` e `PartitionKey`.

Nelle funzioni C# è anche possibile eseguire l'associazione a uno dei seguenti tipi e il runtime di Funzioni tenterà di deserializzare i dati della tabella usando quel tipo:

* Qualsiasi tipo che implementa `ITableEntity`
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>Esempio di input
Si supponga di avere il seguente function.json che usa un trigger della coda per leggere una singola riga della tabella. Il JSON specifica `PartitionKey` 
`RowKey`. `"rowKey": "{queueTrigger}"` indica che la chiave di riga proviene dalla stringa di messaggio della coda.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
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
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Vedere l'esempio specifico del linguaggio che legge una singola entità di tabella.

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.JS](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Esempio di input in C# #
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

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>Esempio di input in F# #
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

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Esempio di input in Node.js
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Associazione di output della tabella di archiviazione
L'associazione di output della tabella di Archiviazione di Azure consente di scrivere entità in una tabella di archiviazione della funzione. 

L'output della tabella di archiviazione per una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

Tenere presente quanto segue: 

* Usare `partitionKey` e `rowKey` insieme per scrivere una singola entità. Queste proprietà sono facoltative. È possibile specificare `PartitionKey` e `RowKey` anche quando si creano gli oggetti entità nel codice della funzione.
* `connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integra** configura automaticamente questa impostazione app quando si crea un account di archiviazione o si seleziona un account già esistente. È anche possibile [configurare questa impostazione dell'app manualmente](functions-how-to-use-azure-function-app-settings.md#settings). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso dell'output
Nelle funzioni C# è possibile eseguire l'associazione all'output della tabella usando il parametro denominato `out` nella firma funzione, ad esempio `out <T> <name>`, dove `T` è il tipo di dati in cui si vuole serializzare i dati e `paramName` è il nome specificato nell'[associazione di output](#output). Nelle funzioni Node.js si accede all'output della tabella usando `context.bindings.<name>`.

È possibile serializzare gli oggetti nelle funzioni Node.js o C#. Nelle funzioni C# è anche possibile definire associazioni con i seguenti tipi:

* Qualsiasi tipo che implementa `ITableEntity`
* `ICollector<T>` (per restituire più entità, vedere l'[esempio](#outcsharp)).
* `IAsyncCollector<T>` (versione asincrona di `ICollector<T>`)
* `CloudTable` (uso di Azure Storage SDK. vedere l'[esempio](#readmulti)).

<a name="outputsample"></a>

## <a name="output-sample"></a>Esempio di output
L'esempio di *function.json* e *run.csx* seguente illustra come scrivere più entità di tabella.

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
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Vedere l'esempio specifico del linguaggio che crea più entità di tabella.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Esempio di output in C# #
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
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Esempio di output in F# #
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

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Esempio di output in Node.js
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

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>Esempio: Leggere più entità di tabella in C#  #
Il codice di esempio di *function.json* e di C# seguente legge le entità per una chiave di partizione specificata nel messaggio della coda.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Il codice C# aggiunge un riferimento ad Azure Storage SDK in modo che il tipo di entità possa derivare da `TableEntity`.

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

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


