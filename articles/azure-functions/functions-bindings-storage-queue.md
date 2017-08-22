---
title: Associazioni dell'archiviazione code di Funzioni di Azure | Microsoft Docs
description: Informazioni su come usare trigger e associazioni di Archiviazione di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f39f674bf576a2661a0e03710b9005b0515b3aa5
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="azure-functions-queue-storage-bindings"></a>Associazione dell'archiviazione code di Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice delle associazioni archiviazione code di Azure in Funzioni di Azure. Funzioni di Azure supporta il trigger e le associazioni di output per le code di Azure. Per le funzionalità disponibili in tutte le associazioni, vedere [Concetti di Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Trigger per l'archiviazione code
Il trigger dell'archiviazione code di Azure consente di monitorare l'archiviazione code di nuovi messaggi e di intraprendere le azioni necessarie. 

Definire un trigger di coda usando la scheda **Integrazione** nel portale Funzioni. Il portale crea la definizione seguente nella sezione **associazioni** di *function.json*:

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* La proprietà `connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione alla risorsa di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integrazione** configura questa impostazione app quando si sceglie un account di archiviazione.

È possibile specificare impostazioni aggiuntive in un [file host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) per ottimizzare i trigger dell'archiviazione code. Ad esempio, è possibile modificare l'intervallo di polling della coda in host.json.

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>Uso di un trigger della coda
Nelle funzioni Node.js si accede ai dati della coda usando `context.bindings.<name>`.


Nelle funzioni .NET è possibile accedere al payload della coda con un parametro del metodo, ad esempio `CloudQueueMessage paramName`. In questo caso, `paramName` è il valore specificato nella [configurazione del trigger](#trigger). Il messaggio della coda può essere deserializzato in uno qualsiasi dei seguenti tipi:

* Oggetto POCO. Usare se il payload della coda è un oggetto JSON. Il runtime di Funzioni deserializza il payload nell'oggetto POCO. 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Metadati del trigger della coda
Il trigger della coda contiene diverse proprietà di metadati. Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. I valori hanno la stessa semantica di [`CloudQueueMessage`].

* **QueueTrigger**: payload della coda, se si tratta di una stringa valida
* **DequeueCount**: digitare `int`. Il numero di volte in cui questo messaggio è stato rimosso dalla coda.
* **ExpirationTime**: digitare `DateTimeOffset?`. Ora di scadenza del messaggio.
* **Id**: digitare `string`. ID del messaggio in coda.
* **InsertionTime**: digitare `DateTimeOffset?`. L'ora in cui il messaggio è stato aggiunto alla coda.
* **NextVisibleTime** - Tipo `DateTimeOffset?`. Ora in cui il messaggio sarà visibile.
* **PopReceipt**: digitare `string`. Ricezione del messaggio.

Per informazioni su come usare i metadati della coda, vedere l'[esempio di trigger](#triggersample).

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Esempio di trigger
Si supponga di avere il seguente function.json, che definisce un trigger della coda:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

Vedere l'esempio specifico del linguaggio che recupera e registra i metadati della coda.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Esempio di trigger in C# #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Esempio di trigger in Node.js

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>Gestione di messaggi della coda non elaborabili
Quando una funzione di trigger della coda ha esito negativo, Funzioni di Azure ritenta l'esecuzione fino a cinque volte per un dato messaggio della coda, incluso il primo tentativo. Se tutti i cinque tentativi hanno esito negativo, il runtime di Funzioni aggiunge un messaggio a un'archiviazione code denominata *&lt;originalqueuename>-poison*. È possibile scrivere una funzione per elaborare i messaggi dalla coda non elaborabile archiviandoli o inviando una notifica della necessità di un intervento manuale. 

Per gestire manualmente i messaggi non elaborabili, controllare `dequeueCount` nel messaggio della coda. Vedere [Metadati del trigger della coda](#meta).

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>Associazione di output dell'archiviazione code
L'associazione di output dell'archiviazione code di Azure consente di scrivere i messaggi in una coda. 

Definire un'associazione di output in coda usando la scheda **Integrazione** nel portale Funzioni. Il portale crea la definizione seguente nella sezione **associazioni** di *function.json*:

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* La proprietà `connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione alla risorsa di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integrazione** configura questa impostazione app quando si sceglie un account di archiviazione.

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>Uso dell'associazione di output della coda
Nelle funzioni Node.js si accede alla coda di output usando `context.bindings.<name>`.

Nelle funzioni .NET è anche possibile eseguire l'output in uno dei tipi seguenti. Quando vi è un parametro di tipo `T`, `T` deve essere uno dei tipi di output supportati, ad esempio `string` o un oggetto POCO.

* `out T`, serializzato come JSON
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

È anche possibile usare il tipo restituito del metodo come associazione di output.

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>Esempio di output in coda
*function.json* seguente definisce un trigger HTTP e un'associazione di output della coda:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

Vedere l'esempio specifico del linguaggio che restituisce un messaggio in coda con il payload HTTP in ingresso.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>Esempio di output della coda in C# #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Per inviare più messaggi, usare `ICollector`:

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Esempio di output della coda in Node.js

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Oppure, per inviare più messaggi,

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi

Per un esempio di funzione che usa trigger e associazioni della coda, vedere [Creare una funzione di Azure connessa a un servizio di Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

["CloudQueueMessage"]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage

