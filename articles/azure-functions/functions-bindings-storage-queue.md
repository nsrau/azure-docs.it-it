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
ms.date: 01/18/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 770cac8809ab9f3d6261140333ec789ee1390daf
ms.openlocfilehash: bf9bd2a1b5acdf5a4a4f862bef693f8c60c63a33


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

`connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione di archiviazione. Nel portale di Azure è possibile configurare questa impostazione dell'app nella scheda **Integrazione**, quando si crea un account di archiviazione o si seleziona un account esistente. Per creare manualmente questa impostazione app, vedere la sezione relativa alla [gestione delle impostazioni del servizio app](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings).

È possibile specificare [impostazioni aggiuntive](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) in un file host.json per ottimizzare i trigger della coda di archiviazione.  

### <a name="handling-poison-queue-messages"></a>Gestione di messaggi della coda non elaborabili
Quando una funzione di trigger della coda ha esito negativo, Funzioni di Azure ritenta l'esecuzione fino a cinque volte per un dato messaggio della coda, incluso il primo tentativo. Se tutti i cinque tentativi non hanno esito negativo, Funzioni aggiunge un messaggio a una coda di archiviazione denominata *&lt;nomecodaoriginale >-poison*. È possibile scrivere una funzione per elaborare i messaggi dalla coda non elaborabile archiviandoli o inviando una notifica della necessità di un intervento manuale. 

Per gestire manualmente i messaggi non elaborabili, è possibile verificare in `dequeueCount` quante volte un messaggio è stato prelevato per l'elaborazione. Vedere in proposito la sezione relativa ai [metadati dei trigger della coda](#meta).

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso dei trigger
Nelle funzioni C# l'associazione al messaggio di input viene eseguita usando un parametro denominato nella firma funzione, ad esempio `<T> <name>`.
`T` è il tipo di dati in cui si vogliono deserializzare i dati e `paramName` è il nome specificato nell'[associazione del trigger](#trigger). Nelle funzioni Node.js si accede a dati del BLOB di input usando `context.bindings.<name>`.

Il messaggio della coda può essere deserializzato in uno qualsiasi dei seguenti tipi:

* [Oggetto](https://msdn.microsoft.com/library/system.object.aspx): usato per i messaggi serializzati JSON. Quando si dichiara un tipo di input personalizzato, il runtime prova a deserializzare l'oggetto JSON. 
* string
* Matrice di byte
* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx) (solo C#)

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

`connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integra** configura automaticamente questa impostazione app quando si crea un account di archiviazione o si seleziona un account già esistente. Per creare manualmente questa impostazione app, vedere la sezione relativa alla [gestione delle impostazioni del servizio app](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings).

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso dell'output
Nelle funzioni C# la scrittura di un messaggio della coda viene eseguita usando il parametro `out` denominato nella firma della funzione, ad esempio `out <T> <name>`. In questo caso `T` è il tipo di dati in cui si vuole serializzare il messaggio e `paramName` è il nome specificato nell'[associazione di output](#output). Nelle funzioni Node.js si accede all'output usando `context.bindings.<name>`.

È possibile restituire un messaggio della coda usando uno dei tipi di dati nel codice:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx): `out MyCustomType paramName`  
Usato per la serializzazione JSON.  Quando si dichiara un tipo di output personalizzato, il runtime prova a serializzare l'oggetto in JSON. Se il parametro di output è Null quando la funzione viene chiusa, il runtime crea un messaggio della coda come un oggetto Null.
* Stringa: `out string paramName`  
Usato per i messaggi di test. Il runtime crea il messaggio solo se il parametro di stringa è diverso da Null quando la funzione viene chiusa.
* Matrice di byte: `out byte[]` 

Questi tipi di output aggiuntivi sono supportati da una funzione C#:

* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx): `out CloudQueueMessage` 
* `ICollector<T>` o `IAsyncCollector<T>`, dove `T` è uno dei tipi supportati.

<a name="outputsample"></a>

## <a name="output-sample"></a>Esempio di output
Si supponga di avere il seguente function.json, che definisce un [trigger della coda di archiviazione](functions-bindings-storage-queue.md), un input del BLOB di archiviazione e un output del BLOB di archiviazione:

Esempio di *function.json* per un'associazione di output della coda di archiviazione che usa un trigger manuale e scrive l'input in un messaggio della coda:

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "queue",
      "name": "myQueueItem",
      "queueName": "myqueue",
      "connection": "my_storage_connection",
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
public static void Run(string input, out string myQueueItem, TraceWriter log)
{
    myQueueItem = "New message: " + input;
}
```

Oppure, per inviare più messaggi,

```cs
public static void Run(string input, ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Message 1: " + input);
    myQueueItem.Add("Message 2: " + "Some other message.");
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
    // Define a new message for the myQueueItem output binding.
    context.bindings.myQueueItem = "new message";
    context.done();
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

Per un esempio di funzione che usa trigger e associazioni della coda di archiviazione, vedere [Creare una funzione di Azure connessa a un servizio di Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Jan17_HO3-->


