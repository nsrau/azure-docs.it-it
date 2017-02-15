---
title: Associazioni della coda dell&quot;archiviazione di Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare trigger e associazioni di Archiviazione di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: christopheranderson
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
ms.date: 11/02/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 36cf563a8318acb9371c48ba7d29e24694446e45


---
# <a name="azure-functions-storage-queue-bindings"></a>Associazioni della coda dell'archiviazione di Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice delle associazioni della coda di Archiviazione di Azure in Funzioni di Azure. Funzioni di Azure supporta il trigger e le associazioni di output per le code di Archiviazione di Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="storage-queue-trigger"></a>Trigger della coda di archiviazione
Il trigger della coda di Archiviazione di Azure consente di verificare la presenza di nuovi messaggi e di intraprendere le azioni necessarie. 

Il trigger della coda di archiviazione per una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>",
    "type": "queueTrigger",
    "direction": "in"
}
```

`connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integra** configura automaticamente questa impostazione app quando si crea un account di archiviazione o si seleziona un account già esistente. Per creare manualmente questa impostazione app, vedere la sezione relativa alla [configurazione manuale dell'impostazione app]().

È possibile specificare [impostazioni aggiuntive](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) in un file host.json per ottimizzare i trigger della coda di archiviazione.  

### <a name="handling-poison-queue-messages"></a>Gestione di messaggi della coda non elaborabili
Quando una funzione di trigger della coda ha esito negativo, per impostazione predefinita Funzioni di Azure ritenta l'esecuzione fino a 5 volte (incluso il primo tentativo) per un dato messaggio della coda. Se tutti i 5 tentativi non riescono, Funzioni aggiunge un messaggio a una coda di archiviazione denominata *&lt;nomecodaoriginale >-poison*. È possibile scrivere una funzione per elaborare i messaggi dalla coda non elaborabile archiviandoli o inviando una notifica della necessità di un intervento manuale. 

Per gestire manualmente i messaggi non elaborabili, è possibile verificare in `dequeueCount`quante volte un messaggio è stato prelevato per l'elaborazione (vedere la sezione relativa ai [metadati dei trigger della coda](#meta)).

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utilizzo dei trigger
Nelle funzioni C# l'associazione al messaggio di input viene eseguita usando un parametro denominato nella firma funzione, ad esempio `<T> <name>`.
`T` è il tipo di dati in cui si vogliono deserializzare i dati e `paramName` è il nome specificato nell'[associazione del trigger](#trigger). Nelle funzioni Node.js si accede a dati del BLOB di input usando `context.bindings.<name>`.

Il messaggio della coda può essere deserializzato in uno qualsiasi dei seguenti tipi:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx), utile per i messaggi serializzati con JSON.
  Se si dichiara un tipo di input personalizzato, ad esempio `FooType`, Funzioni di Azure tenta di deserializzare i dati JSON nel tipo specificato.
* string
* Matrice di byte 
* `CloudQueueMessage` (C#) 

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Metadati del trigger della coda
È possibile ottenere i metadati della coda nella funzione usando questi nomi di variabili:

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (un altro modo per recuperare il testo del messaggio della coda come stringa)

Per informazioni su come usare i metadati della coda, vedere l'[esempio di trigger](#triggersample)

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Esempio di trigger
Si supponga di avere il seguente function.json, che definisce un trigger della coda di archiviazione:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

Vedere l'esempio specifico del linguaggio che recupera e registra i metadati della coda.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Esempio di trigger in C# #
```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
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
    context.log('Node.js queue trigger function processed work item' context.bindings.myQueueItem);
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

<a name="output"></a>

## <a name="storage-queue-output-binding"></a>Associazione di output della coda di archiviazione
L'associazione di output della coda di Archiviazione di Azure consente di scrivere i messaggi in una coda di archiviazione nella funzione. 

L'output della coda di archiviazione per una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
    "queueName": "<Name of queue to write to>",
    "connection":"<Name of app setting - see below>",
  "type": "queue",
  "direction": "out"
}
```

`connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integra** configura automaticamente questa impostazione app quando si crea un account di archiviazione o si seleziona un account già esistente. Per creare manualmente questa impostazione app, vedere la sezione relativa alla [configurazione manuale dell'impostazione app]().

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso dell'output
Nelle funzioni C# è possibile scrivere un messaggio della coda usando il parametro denominato `out` nella firma funzione, ad esempio `out <T> <name>`, dove `T` è il tipo di dati in cui si vuole serializzare il messaggio e `paramName` è il nome specificato nell'[associazione di output](#output). Nelle funzioni Node.js si accede all'output usando `context.bindings.<name>`.

È possibile restituire un messaggio della coda usando uno dei tipi di dati nel codice:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx), utile per la serializzazione con JSON.
  Se si dichiara un tipo di output personalizzato (ad esempio `out FooType paramName`), Funzioni di Azure tenta di serializzare l'oggetto in JSON. Se il parametro di output è null quando la funzione viene chiusa, il runtime di Funzioni crea un messaggio della coda come un oggetto null.
* Stringa (`out string paramName`), utile per i messaggi di prova. Il runtime di Funzioni crea il messaggio solo se il parametro di stringa è diverso da null quando la funzione viene chiusa.
* Matrice di byte (`out byte[]`) 
* `out CloudQueueMessage`, solo C# 

In C# è anche possibile definire l'associazione a `ICollector<T>` o `IAsyncCollector<T>` dove `T` è uno dei tipi supportati.

<a name="outputsample"></a>

## <a name="output-sample"></a>Esempio di output
Si supponga di avere il seguente function.json, che definisce un [trigger della coda di archiviazione](functions-bindings-storage-queue.md), un input del BLOB di archiviazione e un output del BLOB di archiviazione:

Esempio di *function.json* per un'associazione di output della coda di archiviazione che usa il trigger della coda e scrive un messaggio della coda:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Vedere l'esempio specifico del linguaggio che scrive un messaggio della coda di output per ogni messaggio della coda di input.

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Esempio di output in C# #

```cs
public static void Run(string myQueueItem, out string myQueue, TraceWriter log)
{
    myQueue = myQueueItem + "(next step)";
}
```

Oppure, per inviare più messaggi,

```cs
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Esempio di output in Node.js

```javascript
module.exports = function(context) {
    context.bindings.myQueue = context.bindings.myQueueItem + "(next step)";
    context.done();
};
```

Oppure, per inviare più messaggi,

```javascript
module.exports = function(context) {
    context.bindings.myQueue = [];

    context.bindings.myQueueItem.push("(step 1)");
    context.bindings.myQueueItem.push("(step 2)");
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


