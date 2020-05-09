---
title: Associazione di output di griglia di eventi di Azure per funzioni di Azure
description: Informazioni su come inviare un evento di griglia di eventi in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 0237bcbf98578d9f83f3c9652661c786df54e73a
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627688"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Associazione di output di griglia di eventi di Azure per funzioni di Azure

Usare l'associazione di output di griglia di eventi per scrivere eventi in un argomento personalizzato. È necessario disporre di una [chiave di accesso valida per l'argomento personalizzato](../event-grid/security-authentication.md#authenticate-publishing-clients-using-sas-or-key).

Per informazioni sui dettagli di configurazione e configurazione, vedere la [Panoramica](./functions-bindings-event-grid.md).

> [!NOTE]
> L'associazione di output di griglia di eventi non supporta le firme di accesso condiviso (token SAS). È necessario usare la chiave di accesso dell'argomento.

> [!IMPORTANT]
> L'associazione di output di griglia di eventi è disponibile solo per le funzioni 2. x e successive.

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che scrive un messaggio in un argomento personalizzato di griglia di eventi, usando il valore restituito del metodo come output:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Nell'esempio seguente viene illustrato come utilizzare l' `IAsyncCollector` interfaccia per inviare un batch di messaggi.

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

L'esempio seguente mostra i dati dell'associazione di output di griglia di eventi nel file *Function. JSON* .

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

L'esempio seguente mostra i dati dell'associazione di output di griglia di eventi nel file *Function. JSON* .

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

L'associazione di output di griglia di eventi non è disponibile per Python.

# <a name="java"></a>[Java](#tab/java)

L'associazione di output di griglia di eventi non è disponibile per Java.

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Per le [librerie di classi C#](functions-dotnet-class-library.md), usare l'attributo [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

Il costruttore dell'attributo accetta il nome di un'impostazione dell'app che contiene il nome dell'argomento personalizzato e il nome di un'impostazione dell'app che contiene la chiave dell'argomento. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dell'output](#configuration). Di seguito è riportato un esempio di attributo `EventGrid`:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere l' [esempio](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

L'associazione di output di griglia di eventi non è disponibile per Python.

# <a name="java"></a>[Java](#tab/java)

L'associazione di output di griglia di eventi non è disponibile per Java.

---

## <a name="configuration"></a>Configurazione

La tabella seguente illustra le proprietà di configurazione dell'associazione impostate nel file *Function. JSON* e nell' `EventGrid` attributo.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Deve essere impostato su "eventGrid". |
|**direzione** | n/d | Deve essere impostato su "out". Questo parametro viene impostato automaticamente quando si crea l'associazione nel portale di Azure. |
|**name** | n/d | Nome della variabile usato nel codice della funzione che rappresenta l'evento. |
|**topicEndpointUri** |**TopicEndpointUri** | Nome di un'impostazione dell'app che contiene l'URI per l'argomento personalizzato, ad esempio `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | Nome di un'impostazione dell'app che contiene una chiave di accesso per l'argomento personalizzato. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Assicurarsi di impostare il valore della proprietà di `TopicEndpointUri` configurazione sul nome di un'impostazione dell'app che contiene l'URI dell'argomento personalizzato. Non specificare l'URI dell'argomento personalizzato direttamente in questa proprietà.

## <a name="usage"></a>Utilizzo

# <a name="c"></a>[C#](#tab/csharp)

Inviare messaggi usando un parametro di metodo, ad `out EventGridEvent paramName`esempio. Per scrivere più messaggi, è possibile usare `ICollector<EventGridEvent>` o `IAsyncCollector<EventGridEvent>` al posto di `out EventGridEvent`.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Inviare messaggi usando un parametro di metodo, ad `out EventGridEvent paramName`esempio. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. Per scrivere più messaggi, è possibile usare `ICollector<EventGridEvent>` o `IAsyncCollector<EventGridEvent>` al posto di `out EventGridEvent`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Accedere all'evento di output usando `context.bindings.<name>` Where `<name>` è il valore specificato nella `name` proprietà di *Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

L'associazione di output di griglia di eventi non è disponibile per Python.

# <a name="java"></a>[Java](#tab/java)

L'associazione di output di griglia di eventi non è disponibile per Java.

---

## <a name="next-steps"></a>Passaggi successivi

* [Inviare un evento di griglia di eventi](./functions-bindings-event-grid-trigger.md)
