---
title: Associazioni di Hub eventi in Funzioni di Azure | Microsoft Docs
description: Informazioni su come usare le associazioni di Hub eventi di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/20/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: eaa97e31fbc2ffb8464b5ec2bd1f0eb5c59fdbd2
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017


---
# <a name="azure-functions-event-hubs-bindings"></a>Associazioni di Hub eventi in Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e usare le associazioni di [Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) in Funzioni di Azure.
Funzioni di Azure supporta il trigger e le associazioni di output per Hub eventi.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Se non si ha familiarità con Hub eventi di Azure, vedere la [panoramica di Hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md).

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>Trigger di Hub eventi
È possibile usare il trigger di Hub eventi per rispondere a un evento inviato a un flusso di eventi di Hub eventi. Per configurare il trigger è necessario avere accesso in lettura ad Hub eventi.

Il trigger di funzione di Hub eventi usa l'oggetto JSON seguente nella matrice `bindings` di function.json:

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the event hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup`: proprietà facoltativa usata per impostare il [gruppo di consumer](../event-hubs/event-hubs-features.md#event-consumers) usato per effettuare la sottoscrizione agli eventi nell'hub. Se omessa, al suo posto viene usato il gruppo di consumer `$Default`.  
`connection` deve essere il nome di un'impostazione dell'app che contiene la stringa di connessione per lo spazio dei nomi di Hub eventi.
Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo *spazio dei nomi*, non per lo stesso Hub eventi. Per attivare il trigger, questa stringa di connessione deve disporre almeno delle autorizzazioni Read.

È possibile specificare [Impostazioni aggiuntive](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) in un file host.json per ottimizzare i trigger di Hub eventi.  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utilizzo dei trigger
Quando viene attivata una funzione trigger di Hub eventi, il messaggio che la attiva viene passato alla funzione sotto forma di stringa.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Esempio di trigger
Si supponga che il trigger di Hub eventi seguente sia presente nella matrice `bindings` di function.json:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Vedere l'esempio specifico del linguaggio che registra il corpo del messaggio del trigger di Hub eventi.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Esempio di trigger in C# #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Esempio di trigger in F# #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Esempio di trigger in Node.js

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hubs-output-binding"></a>Associazione di output di Hub eventi
È possibile usare l'associazione di output di Hub eventi per scrivere eventi in un flusso di eventi di Hub eventi. Per scrivervi eventi, è necessario disporre dell'autorizzazione Send verso un Hub eventi.

L'associazione di output usa l'oggetto JSON seguente nella matrice `bindings` di function.json:

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection` deve essere il nome di un'impostazione dell'app che contiene la stringa di connessione per lo spazio dei nomi di Hub eventi.
Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo *spazio dei nomi*, non per lo stesso Hub eventi. Per inviare il messaggio al flusso di eventi, questa stringa di connessione deve disporre di autorizzazioni Send.

## <a name="output-usage"></a>Uso dell'output
Questa sezione illustra come usare l'associazione di output di Hub eventi nel codice della funzione.

È possibile inviare i messaggi all'hub eventi configurato con i tipi di parametro seguenti:

* `out string`
* `ICollector<string>` (per restituire più messaggi)
* `IAsyncCollector<string>` (versione asincrona di `ICollector<T>`)

<a name="outputsample"></a>

## <a name="output-sample"></a>Esempio di output
Si supponga che l'associazione di output di Hub eventi seguente sia presente nella matrice `bindings` di function.json:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Vedere l'esempio specifico del linguaggio che scrive un evento nel flusso di eventi.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Esempio di output in C# #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Oppure per creare più messaggi:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Esempio di output in F# #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>Esempio di output in Node.js

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Oppure, per inviare più messaggi,

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

