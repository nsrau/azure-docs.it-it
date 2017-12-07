---
title: Associazioni di Hub di notifica per Funzioni di Azure
description: Informazioni su come usare l'associazione di Hub di notifica di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: fd5ff8878e6afe95bd620bd9d1910add6dd92f3f
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Associazioni di output di Hub di notifica per Funzioni di Azure

Questo articolo illustra come inviare le notifiche push tramite le associazioni di [Hub di notifica di Azure](../notification-hubs/notification-hubs-push-notification-overview.md) in Funzioni di Azure. Funzioni di Azure supporta associazioni di output per Hub di notifica.

Hub di notifica di Azure deve essere configurato per il sistema PNS (Platform Notifications Service) che si vuole usare. Per informazioni su come ottenere notifiche push nell'app client da Hub di notifica, vedere [Introduzione a Hub di notifica](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e selezionare la piattaforma client di destinazione dall'elenco a discesa nella parte superiore della pagina.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example---template"></a>Modello di esempio

Le notifiche inviate possono essere native o [modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Le notifiche native sono indirizzate a una piattaforma client specifica, come configurato nella proprietà `platform` dell'associazione di output. È possibile utilizzare una notifica modello per indirizzare più piattaforme.   

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# - parametro out](#c-script-template-example---out-parameter)
* [Script C# - asincrono](#c-script-template-example---asynchronous)
* [Script C# - JSON](#c-script-template-example---json)
* [Script C# - tipi di libreria](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Esempio di modello di script C# - parametro out

Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente un segnaposto `message` nel modello.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>Esempio di modello di script C# - asincrono

I parametri di uscita non sono consentiti se si utilizza codice asincrono. In questo caso utilizzare `IAsyncCollector` per tornare alla notifica modello. Il codice seguente è un esempio asincrono del codice precedente. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>Esempio di modello di script C# - JSON

Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente un segnaposto `message` nel modello tramite una stringa JSON valida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Esempio di modello di script C# - tipi di librerie

Questo esempio illustra come usare i tipi definiti nella [libreria di Hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>Esempio di modello F#

Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente `location` e `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Esempio di modello JavaScript

Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente `location` e `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Esempio - notifica del servizio APN nativa

In questo esempio di script C# viene illustrato come inviare una notifica del servizio APN nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---gcm-native"></a>Esempio - notifica GCM nativa

In questo esempio di script C# viene illustrato come inviare una notifica GCM nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Esempio - notifica WNS nativa

Questo esempio di script C# illustra come usare i tipi definiti nella [libreria di Hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) per inviare una notifica WNS popup nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Attributi

Per funzioni in [C# precompilato](functions-dotnet-class-library.md) usare l'attributo [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs), definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs).

I parametri e le proprietà del costruttore dell'attributo sono descritte nella sezione [Configurazione](#configuration).

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `NotificationHub`:

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** |n/d| Deve essere impostato su "notificationHub". |
|**direction** |n/d| Deve essere impostato su "out". | 
|**nome** |n/d| Nome della variabile usato nel codice della funzione per il messaggio dell'hub di notifica. |
|**tagExpression** |**TagExpression** | Le espressioni tag consentono di specificare che le notifiche devono essere recapitate a un set di dispositivi che hanno eseguito la registrazione per ricevere le notifiche corrispondenti all'espressione tag.  Per altre informazioni, vedere [Routing ed espressioni tag](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nome della risorsa dell'hub di notifica nel portale di Azure. |
|**connessione** | **ConnectionStringSetting** | Nome di un'impostazione dell'app che contiene una stringa di connessione di Hub di notifica.  La stringa di connessione deve essere impostata sul valore di *DefaultFullSharedAccessSignature* per l'hub di notifica. Vedere [Configurazione della stringa di connessione](#connection-string-setup) più avanti in questo articolo.|
|**platform** | **Piattaforma** | La proprietà platform indica la piattaforma client a cui è indirizzata la notifica. Per impostazione predefinita, se la proprietà della piattaforma viene omessa dall'associazione di output, possono essere usate le notifiche del modello per indirizzarsi a qualsiasi piattaforma configurata nell'hub di notifica di Azure. Per ulteriori informazioni sull'utilizzo dei modelli in generale per inviare notifiche tra piattaforme con un hub di notifica di Azure, vedere [Modelli](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Se impostata, la proprietà **platform** deve avere uno dei valori seguenti: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Per altre informazioni su come configurare l'hub di notifica per il servizio APN e ricevere la notifica in un'app client, vedere [Invio di notifiche push a iOS con Hub di notifica di Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Per altre informazioni su come configurare l'hub di notifica per ADM e ricevere la notifica in un'app Kindle, vedere [Introduzione ad Hub di notifica per le app per Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). È supportato anche Firebase Cloud Messaging, la nuova versione di GCM. Per altre informazioni, vedere [Invio di notifiche push ad Android con Hub di notifica di Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Servizi notifica Push Windows](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) per piattaforme Windows. Anche Windows Phone 8.1 e versioni successive sono supportati da WNS. Per altre informazioni, vedere [Introduzione ad Hub di notifica per le app della piattaforma UWP (Universal Windows Platform)](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Servizio notifica push Microsoft](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Questa piattaforma supporta piattaforme Windows Phone 8 e precedenti di Windows Phone. Per altre informazioni, vedere [Invio di notifiche push con Hub di notifica di Azure in Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Esempio di file function.json

Di seguito è riportato un esempio di un'associazione di Hub di notifica in un file *function.json*.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Configurazione della stringa di connessione

Per usare un'associazione di output dell'hub di notifica, è necessario configurare la stringa di connessione per l'hub. È possibile selezionare un hub di notifica esistente o crearne uno nuovo direttamente dalla scheda *Integrazione* nel portale di Azure. È anche possibile configurare manualmente la stringa di connessione. 

Per configurare la stringa di connessione a un hub di notifica esistente:

1. Passare all'hub di notifica nel [portale di Azure](https://portal.azure.com), scegliere **Criteri di accesso** e selezionare il pulsante di copia accanto a **DefaultFullSharedAccessSignature**. In questo modo, la stringa di connessione per il criterio *DefaultFullSharedAccessSignature* viene copiata nell'hub di notifica. Questa stringa di connessione consente alla funzione di inviare i messaggi di notifica all'hub.
    ![Copiare la stringa di connessione dell'hub di notifica](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Passare all'app per le funzioni nel portale di Azure, scegliere **Impostazioni dell'applicazione**, aggiungere una chiave, ad esempio **MyHubConnectionString**, incollare il valore di *DefaultFullSharedAccessSignature* copiato per l'hub di notifica e quindi fare clic su **Salva**.

Il nome di questa impostazione applicazione corrisponde all'impostazione di connessione dell'associazione di output in *function.json* o nell'attributo .NET. Vedere la [sezione Configurazione](#configuration) più indietro in questo articolo.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

