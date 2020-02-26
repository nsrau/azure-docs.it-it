---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589740"
---
È possibile usare l'associazione di output di Hub eventi per scrivere eventi in un flusso di eventi. Per scrivervi eventi, è necessario disporre dell'autorizzazione Send verso un Hub eventi.

Prima di provare a implementare un'associazione di output, assicurarsi che i riferimenti ai pacchetti necessari siano presenti.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Script](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Per le [librerie di classi C#](../articles/azure-functions/functions-dotnet-class-library.md) usare l'attributo [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs).

Il costruttore dell'attributo accetta il nome di Hub eventi e il nome di un'impostazione di app che contiene la stringa di connessione. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dell'output](#configuration). Di seguito è riportato un esempio di attributo `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [Output - esempio in C#](#example).

# <a name="c-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Nella [libreria di runtime di funzioni Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)usare l'annotazione [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) nei parametri il cui valore verrebbe pubblicato nell'hub eventi. Il parametro deve essere di tipo `OutputBinding<T>`, dove `T` è un POJO o qualsiasi tipo Java nativo.

---

## <a name="configuration"></a>Configurazione

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

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Inviare messaggi utilizzando un parametro del metodo, ad esempio `out string paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. Per scrivere più messaggi, è possibile usare `ICollector<string>` o `IAsyncCollector<string>` al posto di `out string`.

# <a name="c-script"></a>[C#Script](#tab/csharp-script)

Inviare messaggi utilizzando un parametro del metodo, ad esempio `out string paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. Per scrivere più messaggi, è possibile usare `ICollector<string>` o `IAsyncCollector<string>` al posto di `out string`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Accedere all'evento di output usando `context.bindings.<name>` dove `<name>` è il valore specificato nella proprietà `name` di *Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

Sono disponibili due opzioni per l'output di un messaggio di hub eventi da una funzione:

- **Valore restituito**: impostare la proprietà `name` in *Function. JSON* su `$return`. Con questa configurazione, il valore restituito della funzione viene reso permanente come messaggio dell'hub eventi.

- **Imperativo**: passare un valore al metodo [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) del parametro dichiarato come tipo [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . Il valore passato a `set` viene reso permanente come messaggio dell'hub eventi.

# <a name="java"></a>[Java](#tab/java)

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
