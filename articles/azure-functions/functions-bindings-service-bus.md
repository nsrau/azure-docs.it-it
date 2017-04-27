---
title: Trigger e associazioni del bus di servizio di Azure di Funzioni di Azure | Microsoft Docs
description: Informazioni su come usare trigger e associazioni del bus di servizio di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6644f6b879e48787249111c5e02b75b963f1e1cd
ms.lasthandoff: 04/03/2017


---
# <a name="azure-functions-service-bus-bindings"></a>Associazioni del bus di servizio di Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e operare con il codice per associazioni del bus di servizio di Azure in Funzioni di Azure. Funzioni di Azure supporta il trigger e le associazioni di output per le code e gli argomenti del bus di servizio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>Trigger di bus di servizio
Usare il trigger di bus di servizio per rispondere a messaggi da una coda o da un argomento del bus di servizio. 

I trigger della coda e dell'argomento del bus di servizio sono definiti dagli oggetti JSON seguenti nella matrice `bindings` di function.json:

* trigger della *coda*:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* trigger dell'*argomento*:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

Tenere presente quanto segue:

* Per `connection`, [creare un'impostazione nell'app per le funzioni](functions-how-to-use-azure-function-app-settings.md) che contenga la stringa di connessione allo spazio dei nomi dell'Hub di servizio, quindi specificare il nome dell'impostazione dell'app nella proprietà `connection` del trigger. Ottenere la stringa di connessione seguendo i passaggi illustrati in [Ottenere le credenziali di gestione](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  La stringa di connessione deve essere relativa a uno spazio dei nomi del bus di servizio e non limitata a una coda o un argomento specifico.
  Se si lascia `connection` vuoto, il trigger presuppone che una stringa di connessione del bus di servizio predefinito sia specificata nell'impostazione dell'app denominata `AzureWebJobsServiceBus`.
* Per `accessRights`, i valori disponibili sono `manage` e `listen`. Il valore predefinito è `manage`, che indica che `connection` dispone dell'autorizzazione **Gestisci**. Se si usa una stringa di connessione che non dispone dell'autorizzazione **Gestisci**, impostare `accessRights` su `listen`. In caso contrario, il runtime di Funzioni potrebbe non riuscire a eseguire operazioni che richiedono diritti di gestione.

## <a name="trigger-behavior"></a>Comportamento di trigger
* **Single threading**: per impostazione predefinita il runtime di Funzioni elabora più messaggi contemporaneamente. Per impostare il runtime in modo che elabori un solo messaggio della coda o dell'argomento alla volta, impostare `serviceBus.maxConcurrentCalls` su 1 in *host.json*. 
  Per informazioni su *host.json*, vedere [Struttura delle cartelle](functions-reference.md#folder-structure) e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).
* **Gestione dei messaggi non elaborabili**: il bus di servizio esegue la gestione dei messaggi non elaborabili in modo autonomo, non controllabile o modificabile nella configurazione o nel codice di Funzioni di Azure. 
* **Comportamento di PeekLock**: il runtime di Funzioni riceve un messaggio in modalità [`PeekLock` ](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode) e chiama `Complete` sul messaggio se la funzione viene completata correttamente oppure `Abandon` se la funzione ha esito negativo. 
  Se il tempo di esecuzione della funzione supera il timeout di `PeekLock` , il blocco viene rinnovato automaticamente.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso dei trigger
Questa sezione illustra come usare il trigger di bus di servizio nel codice di funzione. 

In C# ed F# il messaggio di trigger di bus di servizio può essere deserializzato in uno qualsiasi dei seguenti tipi:

* `string`: utile per i messaggi di stringa
* `byte[]`: utile per i dati binari
* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx): utile per i dati serializzati con JSON.
  Se si dichiara un tipo di input personalizzato, ad esempio `CustomType`, Funzioni di Azure tenta di deserializzare i dati JSON nel tipo specificato.
* `BrokeredMessage`: visualizza il messaggio deserializzato con il metodo [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx).

In Node. js, il messaggio di trigger di bus di servizio viene passato alla funzione come stringa o come oggetto JSON.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Esempio di trigger
Si supponga di disporre del file function.json seguente:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Vedere l'esempio specifico per la lingua che elabora un messaggio di coda del bus di servizio.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Esempio di trigger in C# #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Esempio di trigger in F# #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Esempio di trigger in Node.js

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>Associazione di output di bus di servizio
Gli output della coda e dell'argomento del bus di servizio per una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

* output di *coda*:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* output di *argomento*:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>"
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

Tenere presente quanto segue:

* Per `connection`, [creare un'impostazione nell'app per le funzioni](functions-how-to-use-azure-function-app-settings.md) che contenga la stringa di connessione allo spazio dei nomi dell'Hub di servizio, quindi specificare il nome dell'impostazione dell'app nella proprietà `connection` dell'associazione di output. Ottenere la stringa di connessione seguendo i passaggi illustrati in [Ottenere le credenziali di gestione](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  La stringa di connessione deve essere relativa a uno spazio dei nomi del bus di servizio e non limitata a una coda o un argomento specifico.
  Se si lascia `connection` vuoto, l'associazione di output presuppone che una stringa di connessione del bus di servizio predefinito sia specificata nell'impostazione dell'app denominata `AzureWebJobsServiceBus`.
* Per `accessRights`, i valori disponibili sono `manage` e `listen`. Il valore predefinito è `manage`, che indica che `connection` dispone dell'autorizzazione **Gestisci**. Se si usa una stringa di connessione che non dispone dell'autorizzazione **Gestisci**, impostare `accessRights` su `listen`. In caso contrario, il runtime di Funzioni potrebbe non riuscire a eseguire operazioni che richiedono diritti di gestione.

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso dell'output
In C# e F#, Funzioni di Azure può creare un messaggio di coda del bus di servizio da uno qualsiasi dei tipi seguenti:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx): la definizione dei parametri è simile a `out T paramName` (C#).
  Funzioni deserializza l'oggetto in un messaggio JSON. Se il valore di output è null quando la funzione viene chiusa, Funzioni crea il messaggio con un oggetto null.
* `string`: la definizione dei parametri è simile a `out string paraName` (C#). Se il valore del parametro è diverso da null quando termina la funzione, Funzioni crea un messaggio.
* `byte[]`: la definizione dei parametri è simile a `out byte[] paraName` (C#). Se il valore del parametro è diverso da null quando termina la funzione, Funzioni crea un messaggio.
* `BrokeredMessage`: la definizione dei parametri è simile a `out BrokeredMessage paraName` (C#). Se il valore del parametro è diverso da null quando termina la funzione, Funzioni crea un messaggio.

Per la creazione di più messaggi in una funzione C# è possibile usare `ICollector<T>` o `IAsyncCollector<T>`. Quando si chiama il metodo `Add` viene creato un messaggio.

In Node. js, è possibile assegnare una stringa, una matrice di byte o un oggetto Javascript (deserializzato in JSON) a `context.binding.<paramName>`.

<a name="outputsample"></a>

## <a name="output-sample"></a>Esempio di output
Si supponga di avere il seguente function.json, che definisce un output della coda di bus di servizio:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Vedere l'esempio specifico per la lingua che invia un messaggio alla coda del bus di servizio.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Esempio di output in C# #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Oppure per creare più messaggi:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Esempio di output in F# #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Esempio di output in Node.js

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Oppure per creare più messaggi:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


