---
title: Azure Event Grid output binding for Azure Functions
description: Informazioni su come inviare un evento Griglia di eventi in Funzioni di Azure.Learn to send an Event Grid event in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368948"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid output binding for Azure Functions

Usare l'associazione di output Griglia di eventi per scrivere eventi in un argomento personalizzato. È necessario disporre di un tasto di scelta valido [per l'argomento personalizzato.](../event-grid/security-authentication.md#custom-topic-publishing)

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](./functions-bindings-event-grid.md).

> [!NOTE]
> L'associazione di output della griglia di eventi non supporta le firme di accesso condiviso. È necessario utilizzare la chiave di accesso dell'argomento.

> [!IMPORTANT]
> L'associazione di output Griglia di eventi è disponibile solo per funzioni 2.x e versioni successive.

## <a name="example"></a>Esempio

# <a name="c"></a>[C #](#tab/csharp)

Nell'esempio seguente viene illustrata una [funzione di C,](functions-dotnet-class-library.md) che scrive un messaggio in un argomento personalizzato della griglia di eventi, usando il valore restituito del metodo come output:The following example shows a C' function that writes a message to an Event Grid custom topic, using the method return value as the output:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Nell'esempio seguente viene `IAsyncCollector` illustrato come utilizzare l'interfaccia per inviare un batch di messaggi.

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

Nell'esempio seguente vengono illustrati i dati di associazione dell'output della griglia di eventi nel file *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Di seguito è riportato il codice di script di C'è che crea un evento:Here's C's script code that creates one event:

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

Di seguito è riportato il codice di script di C'è che crea più eventi:Here's C's script code that creates multiple events:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Nell'esempio seguente vengono illustrati i dati di associazione dell'output della griglia di eventi nel file *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Ecco il codice JavaScript che crea un singolo evento:Here's JavaScript code that creates a single event:

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

Ecco il codice JavaScript che crea più eventi:Here's JavaScript code that creates multiple events:

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

L'associazione di output della griglia di eventi non è disponibile per Python.The Event Grid output binding is not available for Python.

# <a name="java"></a>[Java](#tab/java)

L'associazione di output Griglia di eventi non è disponibile per Java.The Event Grid output binding is not available for Java.

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C #](#tab/csharp)

Per [le librerie di classi](functions-dotnet-class-library.md)di C , utilizzare l'attributo [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

Il costruttore dell'attributo accetta il nome di un'impostazione dell'app che contiene il nome dell'argomento personalizzato e il nome di un'impostazione dell'app che contiene la chiave dell'argomento. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dell'output](#configuration). Di seguito è riportato un esempio di attributo `EventGrid`:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [esempio](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script di C.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

L'associazione di output della griglia di eventi non è disponibile per Python.The Event Grid output binding is not available for Python.

# <a name="java"></a>[Java](#tab/java)

L'associazione di output Griglia di eventi non è disponibile per Java.The Event Grid output binding is not available for Java.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente vengono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `EventGrid` .

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Deve essere impostato su "eventGrid". |
|**direction** | n/d | Deve essere impostato su "out". Questo parametro viene impostato automaticamente quando si crea l'associazione nel portale di Azure. |
|**name** | n/d | Nome della variabile usato nel codice della funzione che rappresenta l'evento. |
|**topicEndpointUri (argomentoEndpointUri)** |**TopicEndpointUri** | Nome di un'impostazione dell'app che contiene `MyTopicEndpointUri`l'URI per l'argomento personalizzato, ad esempio . |
|**topicKeySetting (Impostazione di topicKey)** |**TopicKeySetting (Impostazione di TopicKey)** | Nome di un'impostazione dell'app che contiene un tasto di scelta per l'argomento personalizzato. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Assicurarsi di impostare `TopicEndpointUri` il valore della proprietà di configurazione sul nome di un'impostazione dell'app che contiene l'URI dell'argomento personalizzato. Non specificare l'URI dell'argomento personalizzato direttamente in questa proprietà.

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Inviare messaggi utilizzando un `out EventGridEvent paramName`parametro del metodo, ad esempio . Per scrivere più messaggi, è possibile usare `ICollector<EventGridEvent>` o `IAsyncCollector<EventGridEvent>` al posto di `out EventGridEvent`.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Inviare messaggi utilizzando un `out EventGridEvent paramName`parametro del metodo, ad esempio . Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. Per scrivere più messaggi, è possibile usare `ICollector<EventGridEvent>` o `IAsyncCollector<EventGridEvent>` al posto di `out EventGridEvent`.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Accedere all'evento `context.bindings.<name>` di `<name>` output utilizzando where `name` è il valore specificato nella proprietà *di function.json*.

# <a name="python"></a>[Python](#tab/python)

L'associazione di output della griglia di eventi non è disponibile per Python.The Event Grid output binding is not available for Python.

# <a name="java"></a>[Java](#tab/java)

L'associazione di output Griglia di eventi non è disponibile per Java.The Event Grid output binding is not available for Java.

---

## <a name="next-steps"></a>Passaggi successivi

* [Inviare un evento Griglia di eventi](./functions-bindings-event-grid-trigger.md)
