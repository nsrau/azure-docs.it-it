---
title: Uso del valore restituito da una funzione di Azure
description: Informazioni su come gestire i valori restituiti per funzioni di Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 1ea7ec0444ba80d3494afba77ad9d7fdabd5f982
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086415"
---
# <a name="using-the-azure-function-return-value"></a>Uso del valore restituito della funzione di Azure

Questo articolo illustra in che modo i valori restituiti funzionano all'interno di una funzione.

Nei linguaggi che hanno un valore restituito, è possibile associare un' [associazione di output](./functions-triggers-bindings.md#binding-direction) di funzione al valore restituito:

* In una libreria di classi C# applicare l'attributo dell'associazione di output al valore restituito del metodo.
* In altri linguaggi impostare la proprietà `name` in *function.json* su `$return`.

Se sono presenti più associazioni di output, usare il valore restituito per una sola di tali associazioni.

Negli script C# e C# è in alternativa possibile inviare dati alle associazioni di output dati tramite i parametri `out` e gli [oggetti dell'agente di raccolta](functions-reference-csharp.md#writing-multiple-output-values).

Vedere l'esempio specifico del linguaggio che mostra l'utilizzo del valore restituito:

* [C#](#c-example)
* [Script C# (file con estensione csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>Esempio in C#

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

## <a name="c-script-example"></a>Esempio di script C#

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

## <a name="f-example"></a>Esempio F#

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

## <a name="javascript-example"></a>Esempio JavaScript

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

## <a name="python-example"></a>Esempio in Python

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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire gli errori di associazione di funzioni di Azure](./functions-bindings-errors.md)
