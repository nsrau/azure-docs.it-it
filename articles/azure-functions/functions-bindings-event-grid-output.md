---
title: Associazioni di output di Griglia di eventi di Azure per Funzioni di Azure
description: Informazioni su come inviare un evento di Griglia di eventi in Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 6bd4d5d82af213063b2000693e46d22744604480
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214125"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Associazioni di output di Griglia di eventi di Azure per Funzioni di Azure

È possibile usare l'associazione di output di Griglia di eventi per scrivere eventi in un argomento personalizzato. È necessario avere una [chiave di accesso per l'argomento personalizzato](../event-grid/security-authenticate-publishing-clients.md) valida.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](./functions-bindings-event-grid.md).

> [!NOTE]
> L'associazione di output di Griglia di eventi non supporta le firme di accesso condiviso (token SAS). È necessario usare la chiave di accesso dell'argomento.

> [!IMPORTANT]
> L'associazione di output di Griglia di eventi è disponibile solo per Funzioni di Azure 2.x e successive.

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che scrive un messaggio in un argomento personalizzato di Griglia di eventi usando il valore restituito del metodo come output:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

L'esempio seguente mostra come usare l'interfaccia `IAsyncCollector` per inviare un batch di messaggi.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli esempi seguenti illustrano i dati di associazione di output di Griglia di eventi nel file *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Ecco il codice script C# che crea un evento:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Ecco il codice script C# che crea più eventi:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli esempi seguenti illustrano i dati di associazione di output di Griglia di eventi nel file *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Ecco il codice JavaScript che crea un singolo evento:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Ecco il codice JavaScript che crea più eventi:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

L'esempio seguente mostra un'associazione di trigger in un file *function.json* e una [funzione Python](functions-reference-python.md) che usa l'associazione. Invia quindi un evento all'argomento personalizzato, come specificato da `topicEndpointUri` .

Ecco i dati di associazione nel file *function.json*:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Di seguito è riportato l'esempio Python per inviare un evento a un argomento personalizzato impostando `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime


def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

# <a name="java"></a>[Java](#tab/java)

L'associazione di output di Griglia di eventi non è disponibile per Java.

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Per le [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs).

Il costruttore dell'attributo accetta il nome di un'impostazione di app che contiene il nome dell'argomento personalizzato e il nome di un'impostazione di app che contiene la chiave dell'argomento. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dell'output](#configuration). Di seguito è riportato un esempio di attributo `EventGrid`:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere l'[esempio](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

L'associazione di output di Griglia di eventi non è disponibile per Python.

# <a name="java"></a>[Java](#tab/java)

L'associazione di output di Griglia di eventi non è disponibile per Java.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `EventGrid`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "eventGrid". |
|**direction** | n/d | Deve essere impostato su "out". Questo parametro viene impostato automaticamente quando si crea l'associazione nel portale di Azure. |
|**nome** | n/d | Nome della variabile usato nel codice della funzione che rappresenta l'evento. |
|**topicEndpointUri** |**TopicEndpointUri** | Nome di un'impostazione di app che contiene l'URI per l'argomento personalizzato, ad esempio `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | Nome di un'impostazione di app che contiene una chiave di accesso per l'argomento personalizzato. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Assicurarsi di impostare il valore della proprietà di configurazione `TopicEndpointUri` sul nome di un'impostazione di app che contiene l'URI dell'argomento personalizzato. Non specificare l'URI dell'argomento personalizzato direttamente in questa proprietà.

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Inviare messaggi con un parametro del metodo, ad esempio `out EventGridEvent paramName`. Per scrivere più messaggi, è possibile usare `ICollector<EventGridEvent>` o `IAsyncCollector<EventGridEvent>` al posto di `out EventGridEvent`.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Inviare messaggi con un parametro del metodo, ad esempio `out EventGridEvent paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. Per scrivere più messaggi, è possibile usare `ICollector<EventGridEvent>` o `IAsyncCollector<EventGridEvent>` al posto di `out EventGridEvent`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Accedere all'evento di output usando `context.bindings.<name>` dove `<name>` è il valore specificato nella proprietà `name` di *function.json*.

# <a name="python"></a>[Python](#tab/python)

L'associazione di output di Griglia di eventi non è disponibile per Python.

# <a name="java"></a>[Java](#tab/java)

L'associazione di output di Griglia di eventi non è disponibile per Java.

---

## <a name="next-steps"></a>Passaggi successivi

* [Inviare un evento di Griglia di eventi](./functions-bindings-event-grid-trigger.md)
