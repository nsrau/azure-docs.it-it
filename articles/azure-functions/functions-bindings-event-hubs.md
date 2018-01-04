---
title: Associazioni di Hub eventi di Azure per Funzioni di Azure
description: Informazioni su come usare le associazioni di Hub eventi di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: wesmc
ms.openlocfilehash: 0d48d0b008d76cfb2d7d7815a69774976e184467
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Associazioni di Hub eventi di Azure per Funzioni di Azure

Questo articolo illustra come usare le associazioni di [Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) in Funzioni di Azure. Funzioni di Azure supporta il trigger e le associazioni di output per Hub eventi.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Trigger

È possibile usare il trigger di Hub eventi per rispondere a un evento inviato a un flusso di eventi di Hub eventi. Per configurare il trigger è necessario avere accesso in lettura ad Hub eventi.

Quando viene attivata una funzione trigger di Hub eventi, il messaggio che la attiva viene passato alla funzione sotto forma di stringa.

## <a name="trigger---scaling"></a>Trigger - ridimensionamento

Ogni istanza di una funzione attivata di Hub eventi è supportata da una sola istanza di EventProcessorHost (EPH). Hub eventi garantisce che solo un EPH può ottenere un lease in una determinata partizione.

Si supponga, ad esempio, di iniziare con la configurazione e i presupposti seguenti per un Hub eventi:

1. 10 partizioni.
1. 1000 eventi distribuiti in modo uniforme tra tutte le partizioni = > 100 messaggi in ogni partizione.

Quando la funzione è stata abilitata, è solo 1 istanza della funzione. Denominiamo questa istanza della funzione Function_0. Function_0 avrà un EPH che gestisce per ottenere un lease in tutte le 10 partizioni. Inizierà la lettura degli eventi dalle partizioni da 0 a 9. A partire da questo punto potranno verificarsi una delle condizioni seguenti:

* **È necessaria una sola istanza della funzione**  - Function_0 è in grado di elaborare tutti i 1000 eventi prima che si attivi la logica di ridimensionamento delle Funzioni di Azure. Di conseguenza, tutti i 1000 messaggi vengono elaborati da Function_0.

* **Aggiunta di un'altra istanza della funzione** - La logica di ridimensionamento delle Funzioni di Azure determina che Function_0 presenta più messaggi di quanti ne possa elaborare e quindi viene creata una nuova istanza, Function_1. Hub eventi rileva che una nuova istanza EPH sta tentando di leggere i messaggi. Hub eventi avvierà il bilanciamento del carico delle partizioni tra le istanze EPH, ad esempio, le partizioni da 0 a 4 vengono assegnate a Function_0 e le partizioni da 5 a 9 a Function_1. 

* **Aggiunta di N altre istanze della funzione** - La logica di ridimensionamento delle Funzioni di Azure determina che sia Function_0 sia Function_1 hanno più messaggi di quanti ne possano elaborare. Verrà eseguito il ridimensionamento per Function_2...N, dove N è maggiore delle partizioni di Hub eventi. Hub eventi eseguirà il bilanciamento del carico delle partizioni nelle istanze Function_0... 9.

Un fattore univoco della logica di ridimensionamento corrente delle Funzioni di Azure è che N è maggiore del numero di partizioni. Questa operazione viene eseguita per assicurare che ci siano sempre istanze di EPH immediatamente disponibili per ottenere rapidamente un blocco delle partizioni appena diventano disponibili da altre istanze. Agli utenti vengono addebitati solo i costi delle risorse usate quando viene eseguita l'istanza della funzione e non vengono addebitati i costi dell'overprovisioning.

Se tutte le esecuzioni delle funzioni riescono senza errori, i checkpoint vengono aggiunti all'account di archiviazione associato. Quando il checkpoint ha esito positivo, non sarà più necessario recuperare nuovamente tutti i 1000 messaggi.

## <a name="trigger---example"></a>Trigger - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#trigger---c-example)
* [Script c# (con estensione csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Trigger - esempio in C#

Nell'esempio seguente un [funzione c#](functions-dotnet-class-library.md) che registra il corpo del messaggio del trigger di hub eventi.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Per ottenere l'accesso ai metadati dell'evento, associare un oggetto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (richiede un'istruzione `using` per `Microsoft.ServiceBus.Messaging`).

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```
Per ricevere gli eventi in un batch, impostare `string` o `EventData` come matrice:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Trigger - esempio di script C#

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione registra il corpo del messaggio del trigger per Hub eventi.

Ecco i dati di associazione nel file *function.json*:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```
Ecco il codice script C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Per ottenere l'accesso ai metadati dell'evento, associare un oggetto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (richiede un'istruzione 'using' per `Microsoft.ServiceBus.Messaging`).

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Per ricevere gli eventi in un batch, impostare `string` o `EventData` come matrice:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Trigger - Esempio in F#

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione F#](functions-reference-fsharp.md) che usa l'associazione. La funzione registra il corpo del messaggio del trigger per Hub eventi.

Ecco i dati di associazione nel file *function.json*:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Ecco il codice F#:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Trigger - esempio JavaScript

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione registra il corpo del messaggio del trigger per Hub eventi.

Ecco i dati di associazione nel file *function.json*:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

## <a name="trigger---attributes"></a>Trigger - attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

Il costruttore dell'attributo accetta il nome di Hub eventi, il nome del gruppo di consumer e il nome di un'impostazione di app che contiene la stringa di connessione. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dei trigger](#trigger---configuration). Di seguito è riportato un esempio di attributo `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Per un esempio completo, vedere [Trigger - esempio c#](#trigger---c-example).

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `EventHubTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `eventHubTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta l'elemento evento nel codice della funzione. | 
|**path** |**EventHubName** | Nome di Hub eventi. | 
|**consumerGroup** |**ConsumerGroup** | Proprietà facoltativa usata per impostare il [gruppo di consumer](../event-hubs/event-hubs-features.md#event-consumers) usato per effettuare la sottoscrizione agli eventi nell'hub. Se omessa, al suo posto viene usato il gruppo di consumer `$Default`. | 
|**connessione** |**Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione per lo spazio dei nomi di Hub eventi. Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo *spazio dei nomi*, non per lo stesso Hub eventi. Per attivare il trigger, questa stringa di connessione deve disporre almeno delle autorizzazioni Read.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---hostjson-properties"></a>Trigger - proprietà di host.json

Il file [host.json](functions-host-json.md#eventhub) contiene le impostazioni che controllano il comportamento del trigger per Hub eventi.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Output

È possibile usare l'associazione di output di Hub eventi per scrivere eventi in un flusso di eventi. Per scrivervi eventi, è necessario disporre dell'autorizzazione Send verso un Hub eventi.

## <a name="output---example"></a>Output - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#output---c-example)
* [Script c# (con estensione csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Output - esempio in C#

Nell'esempio seguente un [funzione c#](functions-dotnet-class-library.md) che scrive un messaggio a un hub eventi, utilizzando il valore restituito del metodo come output:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Output - esempio di script C#

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione scrive un messaggio in un Hub eventi.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Ecco il codice script C# che crea un messaggio:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Ecco il codice script C# che crea più messaggi:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Output - esempio in F#

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione F#](functions-reference-fsharp.md) che usa l'associazione. La funzione scrive un messaggio in un Hub eventi.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Ecco il codice F#:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Output - esempio JavaScript

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione scrive un messaggio in un Hub eventi.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Ecco il codice JavaScript che invia un messaggio:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Ecco il codice JavaScript che invia più messaggi:

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

## <a name="output---attributes"></a>Output - attributi

Per [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

Il costruttore dell'attributo accetta il nome di Hub eventi e il nome di un'impostazione di app che contiene la stringa di connessione. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dell'output](#output---configuration). Di seguito è riportato un esempio di attributo `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Per un esempio completo, vedere [Output - esempio c#](#output---c-example).

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `EventHub`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "eventHub". |
|**direction** | n/d | Deve essere impostato su "out". Questo parametro viene impostato automaticamente quando si crea l'associazione nel portale di Azure. |
|**nome** | n/d | Nome della variabile usato nel codice della funzione che rappresenta l'evento. | 
|**path** |**EventHubName** | Nome di Hub eventi. | 
|**connessione** |**Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione per lo spazio dei nomi di Hub eventi. Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo *spazio dei nomi*, non per lo stesso Hub eventi. Per inviare il messaggio al flusso di eventi, questa stringa di connessione deve disporre di autorizzazioni Send.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Output - uso

In C# e negli script C# è possibile inviare messaggi con un parametro del metodo, ad esempio `out string paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. Per scrivere più messaggi, è possibile usare `ICollector<string>` o `IAsyncCollector<string>` al posto di `out string`.

In JavaScript accedere all'evento di output usando `context.bindings.<name>`. `<name>` è il valore specificato nella proprietà `name` di *function.json*.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
