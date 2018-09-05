---
title: Associazioni di Hub eventi di Azure per Funzioni di Azure
description: Informazioni su come usare le associazioni di Hub eventi di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: 3ff4c23c0538adcc3a064503431cb18016db04cd
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747045"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Associazioni di Hub eventi di Azure per Funzioni di Azure

Questo articolo illustra come usare le associazioni di [Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) in Funzioni di Azure. Funzioni di Azure supporta il trigger e le associazioni di output per Hub eventi.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Per Funzioni di Azure versione 1.x, le associazioni di Hub eventi sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) versione 2.x.
Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

Per Funzioni 2.x, usare il pacchetto [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) versione 3.x.
Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

È possibile usare il trigger di Hub eventi per rispondere a un evento inviato a un flusso di eventi di Hub eventi. Per configurare il trigger è necessario avere accesso in lettura ad Hub eventi.

Quando viene attivata una funzione trigger di Hub eventi, il messaggio che la attiva viene passato alla funzione sotto forma di stringa.

## <a name="trigger---scaling"></a>Trigger - ridimensionamento

Ogni istanza di una funzione attivata di Hub eventi è supportata da una sola istanza di [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Hub eventi garantisce che solo un'istanza [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) può ottenere un lease in una determinata partizione.

Si consideri ad esempio un Hub eventi come quello indicato di seguito:

* 10 partizioni.
* 1000 eventi distribuiti in modo uniforme tra tutte le partizioni con 100 messaggi in ogni partizione.

Quando la funzione viene abilitata per la prima volta, è presente solo un'istanza della funzione. Denominiamo questa istanza della funzione `Function_0`. `Function_0` ha un'unica istanza [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) che presenta un lease in tutte le dieci partizioni. Questa istanza legge gli eventi dalle partizioni da 0 a 9. A partire da questo punto potranno verificarsi una delle condizioni seguenti:

* **Non sono necessarie nuove istanze della funzione**: `Function_0` è in grado di elaborare tutti i 1000 eventi prima che si attivi la logica di ridimensionamento delle funzioni. In questo caso, tutti i 1000 messaggi vengono elaborati da `Function_0`.

* **Viene aggiunta un'altra istanza di funzione**: la logica di ridimensionamento delle funzioni determina che `Function_0` ha più messaggi di quanti ne possa elaborare. In questo caso, viene creata una nuova istanza dell'app per le funzioni (`Function_1`), insieme a una nuova istanza [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Hub eventi rileva che una nuova istanza host sta tentando di leggere i messaggi. Hub eventi bilancia il carico delle partizioni tra le relative istanze dell'host. Ad esempio, le partizioni da 0 a 4 possono essere assegnate a `Function_0` e le partizioni a 5 a 9 a `Function_1`. 

* **Aggiunta di altre N istanze della funzione**: la logica di ridimensionamento delle Funzioni di Azure determina che sia `Function_0` che `Function_1` hanno più messaggi di quanti ne possano elaborare. Vengono create nuove istanze di app per le funzioni `Function_2`... `Functions_N` in cui `N` è maggiore del numero delle partizioni dell'hub eventi. In questo esempio l'Hub eventi bilancia nuovamente il carico delle partizioni, in questo caso tra le istanze `Function_0`...`Functions_9`. 

Si noti quando la funzione scala a `N` istanze, ovvero un numero maggiore del numero di partizioni dell'hub eventi. Questa operazione viene eseguita per assicurare che vi siano sempre delle istanze [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) disponibili per ottenere blocchi sulle partizioni appena diventano disponibili da altre istanze. Vengono addebitati solo i costi delle risorse usate quando viene eseguita l'istanza della funzione e non vengono addebitati i costi dell'overprovisioning.

Al termine dell'esecuzione di tutte le funzioni (con o senza errori), i checkpoint vengono aggiunti all'account di archiviazione associato. Quando il checkpoint ha esito positivo, non verranno più recuperati nuovamente tutti i 1000 messaggi.

## <a name="trigger---example"></a>Trigger - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#trigger---c-example)
* [Script C# (file con estensione csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Trigger - esempio in C#

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che registra il corpo del messaggio del trigger per Hub eventi.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Per ottenere l'accesso ai [metadati dell'evento](#trigger---event-metadata) nel codice funzione, associare un oggetto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (richiede un'istruzione using per `Microsoft.ServiceBus.Messaging`). È possibile anche accedere alle stesse proprietà usando le espressioni di associazione nella firma del metodo.  Nell'esempio seguente vengono illustrati entrambi i modi per ottenere gli stessi dati:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Per ricevere gli eventi in un batch, impostare `string` o `EventData` come matrice:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Trigger - esempio di script C#

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione registra il corpo del messaggio del trigger per Hub eventi.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*. Il primo esempio è per Funzioni 1.x e il secondo è per Funzioni 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
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

Per ottenere l'accesso ai [metadati dell'evento](#trigger---event-metadata) nel codice funzione, associare un oggetto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (richiede un'istruzione using per `Microsoft.ServiceBus.Messaging`). È possibile anche accedere alle stesse proprietà usando le espressioni di associazione nella firma del metodo.  Nell'esempio seguente vengono illustrati entrambi i modi per ottenere gli stessi dati:

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
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

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*. Il primo esempio è per Funzioni 1.x e il secondo è per Funzioni 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice F#:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Trigger - esempio JavaScript

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione legge i [metadati dell'evento](#trigger---event-metadata) e registra il messaggio.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*. Il primo esempio è per Funzioni 1.x e il secondo è per Funzioni 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

Per ricevere gli eventi in un batch, impostare `cardinality` su `many` nel file *function.json*, come illustrato negli esempi seguenti. Il primo esempio è per Funzioni 1.x e il secondo è per Funzioni 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---java-example"></a>Trigger - Esempio Java

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione Java](functions-reference-java.md) che usa l'associazione. La funzione registra il corpo del messaggio del trigger per Hub eventi.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context ) 
       {
          context.getLogger().info(message);
 }
 ```

 Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare l'annotazione `EventHubTrigger` per i parametri il cui valore deriva da Hub eventi. I parametri con queste annotazioni attivano l'esecuzione della funzione quando viene ricevuto un evento.  Questa annotazione può essere usata con i tipi Java nativi, con oggetti POJO o con valori di tipo nullable tramite Facoltativo<T>. 

## <a name="trigger---attributes"></a>Trigger - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs).

Il costruttore dell'attributo accetta il nome di Hub eventi, il nome del gruppo di consumer e il nome di un'impostazione di app che contiene la stringa di connessione. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dei trigger](#trigger---configuration). Di seguito è riportato un esempio di attributo `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Per un esempio completo, vedere [Trigger - esempio in C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `EventHubTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `eventHubTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta l'elemento evento nel codice della funzione. | 
|**path** |**EventHubName** | Solo Funzioni 1.x. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. | 
|**eventHubName** |**EventHubName** | Solo Funzioni 2.x. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. |
|**consumerGroup** |**ConsumerGroup** | Proprietà facoltativa usata per impostare il [gruppo di consumer](../event-hubs/event-hubs-features.md#event-consumers) usato per effettuare la sottoscrizione agli eventi nell'hub. Se omessa, al suo posto viene usato il gruppo di consumer `$Default`. | 
|**cardinality** | n/d | Per JavaScript. Impostare su `many` per abilitare l'invio in batch.  Se omesso o impostato su `one`, singolo messaggio passato alla funzione. | 
|**connessione** |**Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione per lo spazio dei nomi di Hub eventi. Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo [spazio dei nomi](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), non per lo stesso Hub eventi. Per attivare il trigger, questa stringa di connessione deve disporre almeno delle autorizzazioni Read.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Trigger: metadati dell'evento

Il trigger di Hub eventi fornisce diverse [proprietà di metadati](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Queste sono proprietà della classe [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata).

|Proprietà|type|DESCRIZIONE|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|L'istanza di `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Il tempo di accodamento in formato UTC.|
|`Offset`|`string`|L'offset dei dati rispetto al flusso di partizione di Hub eventi. L'offset è un indicatore o un identificatore per un evento all'interno del flusso di Hub eventi. L'identificatore è univoco all'interno di una partizione del flusso di Hub eventi.|
|`PartitionKey`|`string`|La partizione a cui devono essere inviati i dati dell'evento.|
|`Properties`|`IDictionary<String,Object>`|Le proprietà dell'utente dei dati dell'evento.|
|`SequenceNumber`|`Int64`|Il numero di sequenza logica dell'evento.|
|`SystemProperties`|`IDictionary<String,Object>`|Le proprietà di sistema, inclusi di dati dell'evento.|

Vedere gli [esempi di codice](#trigger---example) che usano queste proprietà in precedenza in questo articolo.

## <a name="trigger---hostjson-properties"></a>Trigger - proprietà di host.json

Il file [host.json](functions-host-json.md#eventhub) contiene le impostazioni che controllano il comportamento del trigger per Hub eventi.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Output

È possibile usare l'associazione di output di Hub eventi per scrivere eventi in un flusso di eventi. Per scrivervi eventi, è necessario disporre dell'autorizzazione Send verso un Hub eventi.

Verificare che i riferimenti ai pacchetti necessari siano disponibili: [Funzioni 1.x](#packages---functions-1.x) o [Funzioni 2.x](#packages---functions-2.x) 

## <a name="output---example"></a>Output - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#output---c-example)
* [Script C# (file con estensione csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Output - esempio in C#

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che scrive un messaggio in un Hub eventi usando il valore restituito del metodo come output:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Output - esempio di script C#

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione scrive un messaggio in un Hub eventi.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*. Il primo esempio è per Funzioni 1.x e il secondo è per Funzioni 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
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

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*. Il primo esempio è per Funzioni 1.x e il secondo è per Funzioni 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
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

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*. Il primo esempio è per Funzioni 1.x e il secondo è per Funzioni 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
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

### <a name="output---java-example"></a>Output - Esempio Java

Nell'esempio seguente viene illustrata una funzione Java che scrive un messaggio contenente l'ora corrente in un Hub eventi.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
 ```

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare l'annotazione `@EventHubOutput` per i parametri il cui valore potrebbe essere pubblicato da Hub eventi.  Il di parametro deve essere di tipo `OutputBinding<T>`, dove T corrisponde a un POJO o a qualsiasi tipo Java nativo. 

## <a name="output---attributes"></a>Output - attributi

Per le [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs).

Il costruttore dell'attributo accetta il nome di Hub eventi e il nome di un'impostazione di app che contiene la stringa di connessione. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dell'output](#output---configuration). Di seguito è riportato un esempio di attributo `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Per un esempio completo, vedere [Output - esempio in C#](#output---c-example).

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `EventHub`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "eventHub". |
|**direction** | n/d | Deve essere impostato su "out". Questo parametro viene impostato automaticamente quando si crea l'associazione nel portale di Azure. |
|**nome** | n/d | Nome della variabile usato nel codice della funzione che rappresenta l'evento. | 
|**path** |**EventHubName** | Solo Funzioni 1.x. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. | 
|**eventHubName** |**EventHubName** | Solo Funzioni 2.x. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. |
|**connessione** |**Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione per lo spazio dei nomi di Hub eventi. Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo *spazio dei nomi*, non per lo stesso Hub eventi. Per inviare il messaggio al flusso di eventi, questa stringa di connessione deve disporre di autorizzazioni Send.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Output - uso

In C# e negli script C# è possibile inviare messaggi con un parametro del metodo, ad esempio `out string paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. Per scrivere più messaggi, è possibile usare `ICollector<string>` o `IAsyncCollector<string>` al posto di `out string`.

In JavaScript accedere all'evento di output usando `context.bindings.<name>`. `<name>` è il valore specificato nella proprietà `name` di *function.json*.

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione | riferimento |
|---|---|
| Hub eventi | [Operations Guide](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) (Guida operativa) |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
