---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: a31dc1c6d1a7f4dce6e7baae5a0e0e8f3d6d3d34
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77179119"
---
## <a name="trigger"></a>Trigger

Usare il trigger di funzione per rispondere a un evento inviato a un flusso di eventi di hub eventi. È necessario avere accesso in lettura all'hub eventi sottostante per configurare il trigger. Quando la funzione viene attivata, il messaggio passato alla funzione viene tipizzato come stringa.

## <a name="trigger---scaling"></a>Trigger - ridimensionamento

Ogni istanza di una funzione attivata da un evento è supportata da una singola istanza di [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) . Il trigger (basato su Hub eventi) garantisce che solo un'istanza di [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) possa ottenere un lease in una determinata partizione.

Si consideri ad esempio un Hub eventi come quello indicato di seguito:

* 10 partizioni
* 1\.000 eventi distribuiti uniformemente tra tutte le partizioni, con 100 messaggi in ogni partizione

Quando la funzione viene abilitata per la prima volta, è presente solo un'istanza della funzione. Chiameremo la prima istanza della funzione `Function_0`. La funzione `Function_0` dispone di una singola istanza di [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) che contiene un lease su tutte e dieci le partizioni. Questa istanza legge gli eventi dalle partizioni da 0 a 9. A partire da questo punto potranno verificarsi una delle condizioni seguenti:

* **Non sono necessarie nuove istanze della funzione**: `Function_0` è in grado di elaborare tutti gli eventi 1.000 prima che la logica di ridimensionamento funzioni abbia effetto. In questo caso, tutti i messaggi 1.000 vengono elaborati dal `Function_0`.

* **Viene aggiunta un'istanza di funzione aggiuntiva**: se la logica di ridimensionamento delle funzioni determina che `Function_0` dispone di più messaggi di quanti ne possa elaborare, viene creata una nuova istanza dell'app per le funzioni (`Function_1`). Alla nuova funzione è associata anche un'istanza di [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Poiché gli hub eventi sottostanti rilevano che una nuova istanza dell'host sta provando a leggere i messaggi, il carico bilancia le partizioni tra le istanze dell'host. Ad esempio, le partizioni da 0 a 4 possono essere assegnate a `Function_0` e le partizioni a 5 a 9 a `Function_1`.

* **Sono state aggiunte altre istanze della funzione**: se la logica di ridimensionamento delle funzioni determina che sia `Function_0` che `Function_1` hanno più messaggi di quanti ne possano elaborare, vengono create nuove istanze di app per le funzioni di `Functions_N`.  Le app vengono create nel punto in cui `N` è maggiore del numero di partizioni dell'hub eventi. In questo esempio l'Hub eventi bilancia nuovamente il carico delle partizioni, in questo caso tra le istanze `Function_0`...`Functions_9`.

Quando si verifica il ridimensionamento, `N` istanze è un numero maggiore del numero di partizioni dell'hub eventi. Questo modello viene usato per garantire che le istanze di [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) siano disponibili per ottenere blocchi sulle partizioni Man mano che diventano disponibili da altre istanze. Vengono addebitate solo le risorse utilizzate quando l'istanza della funzione viene eseguita. In altre parole, non viene addebitato alcun costo per il provisioning eccessivo.

Al termine dell'esecuzione di tutte le funzioni (con o senza errori), i checkpoint vengono aggiunti all'account di archiviazione associato. Quando il segno di spunta ha esito positivo, tutti i messaggi 1.000 non vengono mai recuperati.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L'esempio seguente illustra una [funzione C#](../articles/azure-functions/functions-dotnet-class-library.md) che registra il corpo del messaggio del trigger per Hub eventi.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Per ottenere l'accesso ai [metadati dell'evento](#trigger---event-metadata) nel codice funzione, associare un oggetto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (richiede un'istruzione using per `Microsoft.Azure.EventHubs`). È possibile anche accedere alle stesse proprietà usando le espressioni di associazione nella firma del metodo.  Nell'esempio seguente vengono illustrati entrambi i modi per ottenere gli stessi dati:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Per ricevere gli eventi in un batch, impostare `string` o `EventData` come matrice.  

> [!NOTE]
> Quando vengono ricevuti in un batch non è possibile eseguire l'associazione ai parametri del metodo come nell'esempio precedente con `DateTime enqueuedTimeUtc` ed è necessario riceverli da ciascun oggetto `EventData`  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione script C#](../articles/azure-functions/functions-reference-csharp.md) che usa l'associazione. La funzione registra il corpo del messaggio del trigger per Hub eventi.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*.

#### <a name="version-2x-and-higher"></a>Versione 2. x e successive

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versione 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice script C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Per ottenere l'accesso ai [metadati dell'evento](#trigger---event-metadata) nel codice funzione, associare un oggetto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (richiede un'istruzione using per `Microsoft.Azure.EventHubs`). È possibile anche accedere alle stesse proprietà usando le espressioni di associazione nella firma del metodo.  Nell'esempio seguente vengono illustrati entrambi i modi per ottenere gli stessi dati:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

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
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione JavaScript](../articles/azure-functions/functions-reference-node.md) che usa l'associazione. La funzione legge i [metadati dell'evento](#trigger---event-metadata) e registra il messaggio.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*.

#### <a name="version-2x-and-higher"></a>Versione 2. x e successive

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versione 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Per ricevere gli eventi in un batch, impostare `cardinality` su `many` nel file *function.json*, come illustrato negli esempi seguenti.

#### <a name="version-2x-and-higher"></a>Versione 2. x e successive

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

### <a name="version-1x"></a>Versione 1.x

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

# <a name="pythontabpython"></a>[Python](#tab/python)

L'esempio seguente mostra un'associazione di trigger per hub eventi in un file *function.json* e una [funzione Python](../articles/azure-functions/functions-reference-python.md) che usa l'associazione. La funzione legge i [metadati dell'evento](#trigger---event-metadata) e registra il messaggio.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

# <a name="javatabjava"></a>[Java](#tab/java)

L'esempio seguente illustra un'associazione di trigger di hub eventi che registra il corpo del messaggio del trigger di hub eventi.

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

 Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare l'annotazione `EventHubTrigger` per i parametri il cui valore deriva da Hub eventi. I parametri con queste annotazioni attivano l'esecuzione della funzione quando viene ricevuto un evento.  Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

 ---

## <a name="trigger---attributes-and-annotations"></a>Trigger-attributi e annotazioni

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](../articles/azure-functions/functions-dotnet-class-library.md) usare l'attributo [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs).

Il costruttore dell'attributo accetta il nome di Hub eventi, il nome del gruppo di consumer e il nome di un'impostazione di app che contiene la stringa di connessione. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dei trigger](#trigger---configuration). Di seguito è riportato un esempio di attributo `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [Trigger - esempio in C#](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Dalla libreria di [runtime di funzioni](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)Java, usare l'annotazione [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) nei parametri il cui valore verrebbe dall'hub eventi. I parametri con queste annotazioni attivano l'esecuzione della funzione quando viene ricevuto un evento. Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

---

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `EventHubTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `eventHubTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta l'elemento evento nel codice della funzione. |
|**path** |**EventHubName** | Solo Funzioni 1.x. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. |
|**eventHubName** |**EventHubName** | Funzioni 2. x e versioni successive. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. È possibile farvi riferimento tramite le impostazioni dell'app% eventHubName% |
|**consumerGroup** |**ConsumerGroup** | Proprietà facoltativa usata per impostare il [gruppo di consumer](../articles/event-hubs/event-hubs-features.md#event-consumers) usato per effettuare la sottoscrizione agli eventi nell'hub. Se omessa, al suo posto viene usato il gruppo di consumer `$Default`. |
|**cardinality** | n/d | Per JavaScript. Impostare su `many` per abilitare l'invio in batch.  Se omesso o impostato su `one`, viene passato un singolo messaggio alla funzione. |
|**connection** |**Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione per lo spazio dei nomi di Hub eventi. Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo [spazio dei nomi](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), non per lo stesso Hub eventi. Per attivare il trigger, questa stringa di connessione deve disporre almeno delle autorizzazioni Read.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Trigger: metadati dell'evento

Il trigger di Hub eventi fornisce diverse [proprietà di metadati](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Le proprietà dei metadati possono essere utilizzate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Le proprietà provengono dalla classe [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) .

|Proprietà|Type|Descrizione|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Istanza `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Il tempo di accodamento in formato UTC.|
|`Offset`|`string`|L'offset dei dati rispetto al flusso di partizione di Hub eventi. L'offset è un indicatore o un identificatore per un evento all'interno del flusso di Hub eventi. L'identificatore è univoco all'interno di una partizione del flusso di Hub eventi.|
|`PartitionKey`|`string`|La partizione a cui devono essere inviati i dati dell'evento.|
|`Properties`|`IDictionary<String,Object>`|Le proprietà dell'utente dei dati dell'evento.|
|`SequenceNumber`|`Int64`|Il numero di sequenza logica dell'evento.|
|`SystemProperties`|`IDictionary<String,Object>`|Le proprietà di sistema, inclusi di dati dell'evento.|

Vedere gli [esempi di codice](#trigger) che usano queste proprietà in precedenza in questo articolo.

## <a name="trigger---hostjson-properties"></a>Trigger - proprietà di host.json

Il file [host.json](../articles/azure-functions/functions-host-json.md#eventhub) contiene le impostazioni che controllano il comportamento del trigger per Hub eventi.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Output

È possibile usare l'associazione di output di Hub eventi per scrivere eventi in un flusso di eventi. Per scrivervi eventi, è necessario disporre dell'autorizzazione Send verso un Hub eventi.

Prima di provare a implementare un'associazione di output, assicurarsi che i riferimenti ai pacchetti necessari siano presenti.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L'esempio seguente illustra una [funzione C#](../articles/azure-functions/functions-dotnet-class-library.md) che scrive un messaggio in un Hub eventi usando il valore restituito del metodo come output:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Nell'esempio seguente viene illustrato come utilizzare l'interfaccia `IAsyncCollector` per inviare un batch di messaggi. Questo scenario è comune quando si elaborano i messaggi provenienti da un hub eventi e si invia il risultato a un altro hub eventi.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione script C#](../articles/azure-functions/functions-reference-csharp.md) che usa l'associazione. La funzione scrive un messaggio in un Hub eventi.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*. Il primo esempio è per functions 2. x e versioni successive e il secondo è per functions 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ecco il codice script C# che crea un messaggio:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Ecco il codice script C# che crea più messaggi:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione JavaScript](../articles/azure-functions/functions-reference-node.md) che usa l'associazione. La funzione scrive un messaggio in un Hub eventi.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*. Il primo esempio è per functions 2. x e versioni successive e il secondo è per functions 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ecco il codice JavaScript che invia un messaggio:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Ecco il codice JavaScript che invia più messaggi:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

L'esempio seguente mostra un'associazione di trigger per hub eventi in un file *function.json* e una [funzione Python](../articles/azure-functions/functions-reference-python.md) che usa l'associazione. La funzione scrive un messaggio in un Hub eventi.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ecco il codice Python che invia un messaggio:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Nell'esempio seguente viene illustrata una funzione Java che scrive un messaggio contenente l'ora corrente in un hub eventi.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime) usare l'annotazione `@EventHubOutput` per i parametri il cui valore potrebbe essere pubblicato in Hub eventi.  Il di parametro deve essere di tipo `OutputBinding<T>`, dove T corrisponde a un POJO o a qualsiasi tipo Java nativo.

---

## <a name="output---attributes-and-annotations"></a>Output-attributi e annotazioni

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Per le [librerie di classi C#](../articles/azure-functions/functions-dotnet-class-library.md) usare l'attributo [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs).

Il costruttore dell'attributo accetta il nome di Hub eventi e il nome di un'impostazione di app che contiene la stringa di connessione. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dell'output](#output---configuration). Di seguito è riportato un esempio di attributo `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [Output - esempio in C#](#output).

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Nella [libreria di runtime di funzioni Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)usare l'annotazione [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) nei parametri il cui valore verrebbe pubblicato nell'hub eventi. Il parametro deve essere di tipo `OutputBinding<T>`, dove `T` è un POJO o qualsiasi tipo Java nativo.

---

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `EventHub`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "eventHub". |
|**direction** | n/d | Deve essere impostato su "out". Questo parametro viene impostato automaticamente quando si crea l'associazione nel portale di Azure. |
|**nome** | n/d | Nome della variabile usato nel codice della funzione che rappresenta l'evento. |
|**path** |**EventHubName** | Solo Funzioni 1.x. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. |
|**eventHubName** |**EventHubName** | Funzioni 2. x e versioni successive. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. |
|**connection** |**Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione per lo spazio dei nomi di Hub eventi. Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo *spazio dei nomi*, non per lo stesso Hub eventi. Per inviare il messaggio al flusso di eventi, questa stringa di connessione deve disporre di autorizzazioni Send.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Output - uso

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Inviare messaggi utilizzando un parametro del metodo, ad esempio `out string paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. Per scrivere più messaggi, è possibile usare `ICollector<string>` o `IAsyncCollector<string>` al posto di `out string`.

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Inviare messaggi utilizzando un parametro del metodo, ad esempio `out string paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. Per scrivere più messaggi, è possibile usare `ICollector<string>` o `IAsyncCollector<string>` al posto di `out string`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Accedere all'evento di output usando `context.bindings.<name>` dove `<name>` è il valore specificato nella proprietà `name` di *Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Sono disponibili due opzioni per l'output di un messaggio di hub eventi da una funzione:

- **Valore restituito**: impostare la proprietà `name` in *Function. JSON* su `$return`. Con questa configurazione, il valore restituito della funzione viene reso permanente come messaggio dell'hub eventi.

- **Imperativo**: passare un valore al metodo [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) del parametro dichiarato come tipo [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . Il valore passato a `set` viene reso permanente come messaggio dell'hub eventi.

# <a name="javatabjava"></a>[Java](#tab/java)

Sono disponibili due opzioni per l'output di un messaggio di hub eventi da una funzione tramite l'annotazione [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) :

- **Valore restituito**: applicando l'annotazione alla funzione stessa, il valore restituito della funzione viene reso persistente come messaggio dell'hub eventi.

- **Imperativo**: per impostare in modo esplicito il valore del messaggio, applicare l'annotazione a un parametro specifico del tipo [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), dove `T` è un POJO o qualsiasi tipo Java nativo. Con questa configurazione, il passaggio di un valore al metodo `setValue` rende permanente il valore come messaggio dell'hub eventi.

---

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione | Riferimento |
|---|---|
| Hub eventi | [Operations Guide](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) (Guida operativa) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>impostazioni host.json

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione nelle versioni 2. x e successive. Il file host. JSON di esempio seguente contiene solo le impostazioni della versione 2. x + per questa associazione. Per ulteriori informazioni sulle impostazioni di configurazione globali nelle versioni 2. x e successive, vedere informazioni di [riferimento su host. JSON per funzioni di Azure](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|`maxBatchSize`|10|Il numero massimo degli eventi ricevuto per ogni ciclo di ricezione.|
|`prefetchCount`|300|Conteggio predefinito di pre-recupero utilizzato dal `EventProcessorHost`sottostante.|
|`batchCheckpointFrequency`|1|Il numero di batch di eventi da elaborare prima di creare un checkpoint di cursore EventHub.|
