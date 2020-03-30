---
title: Uso del valore restituito da una funzione di AzureUsing return value from an Azure Function
description: Informazioni su come gestire i valori restituiti per Funzioni di AzureLearn to manage return values for Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 7ba104e288204dfbf3d24f5783bf69682a286553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74480573"
---
# <a name="using-the-azure-function-return-value"></a>Uso del valore restituito della funzione di AzureUsing the Azure Function return value

Questo articolo spiega come funzionano i valori restituiti all'interno di una funzione.

Nei linguaggi che hanno un valore restituito, è possibile associare [un'associazione](./functions-triggers-bindings.md#binding-direction) di output della funzione al valore restituito:In languages that have a return value, you can bind a function output binding to the return value:

* In una libreria di classi C# applicare l'attributo dell'associazione di output al valore restituito del metodo.
* In Java applicare l'annotazione di associazione di output al metodo della funzione.
* In altri linguaggi impostare la proprietà `name` in *function.json* su `$return`.

Se sono presenti più associazioni di output, usare il valore restituito per una sola di tali associazioni.

Negli script C# e C# è in alternativa possibile inviare dati alle associazioni di output dati tramite i parametri `out` e gli [oggetti dell'agente di raccolta](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C #](#tab/csharp)

Di seguito è riportato il codice C# che usa il valore restituito per un'associazione di output, seguito da un esempio asincrono:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Di seguito è riportata l'associazione di output nel file *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Di seguito è riportato il codice di script C#, seguito da un esempio asincrono:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F #](#tab/fsharp)

Di seguito è riportata l'associazione di output nel file *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Ecco il codice F#:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Di seguito è riportata l'associazione di output nel file *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

In JavaScript il valore restituito viene inserito nel secondo parametro per `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="python"></a>[Python](#tab/python)

Di seguito è riportata l'associazione di output nel file *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Ecco il codice Python:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Ecco il codice Java che usa il valore restituito per un'associazione di output:Here's Java code that uses the return value for an output binding:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire gli errori di associazione di Funzioni di AzureHandle Azure Functions binding errors](./functions-bindings-errors.md)
