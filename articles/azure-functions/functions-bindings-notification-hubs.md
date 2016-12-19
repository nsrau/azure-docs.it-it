---
title: Associazione di Hub di notifica in Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare l&quot;associazione di Hub di notifica di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 7e9534afa8ecd224b4e3c1df2f4465b70d961d2c
ms.openlocfilehash: 1e03a54956a9fd035cf40aa953a2c368d91b766b


---
# <a name="azure-functions-notification-hub-output-binding"></a>Associazione di output di Hub di notifica in Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice di associazioni di Hub di notifica in Funzioni di Azure. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Le funzioni possono inviare notifiche push usando un hub di notifica di Azure configurato con poche righe di codice. L'hub di notifica di Azure tuttavia deve essere configurato per i servizi di notifiche della piattaforma (PNS) che si intende usare. Per altre informazioni sulla configurazione di un hub di notifica di Azure e sullo sviluppo di applicazioni client che eseguono la registrazione per ricevere notifiche, vedere [Introduzione ad Hub di notifica per le app della piattaforma UWP (Universal Windows Platform)](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e fare clic sulla piattaforma client di destinazione nella parte superiore.

Le notifiche inviate possono essere native o modello. Le notifiche native sono indirizzate a una piattaforma di notifica specifica, come configurato nella `platform` proprietà dell'associazione di uscita. È possibile utilizzare una notifica modello per indirizzare più piattaforme.   

## <a name="notification-hub-output-binding-properties"></a>Associazione delle proprietà di uscita dell'hub di notifica
Il file function.json specifica le proprietà seguenti:

* `name` : nome della variabile usato nel codice della funzione per il messaggio dell'hub di notifica.
* `type` : deve essere impostato su *"notificationHub"*.
* `tagExpression` : le espressioni tag consentono di specificare che le notifiche devono essere recapitate a un set di dispositivi che hanno eseguito la registrazione per ricevere le notifiche corrispondenti all'espressione tag.  Per altre informazioni, vedere [Routing ed espressioni tag](../notification-hubs/notification-hubs-tags-segment-push-message.md).
* `hubName` : nome della risorsa dell'hub di notifica nel portale di Azure.
* `connection` : questa stringa di connessione deve essere una stringa di connessione dell' **impostazione dell'applicazione** configurata sul valore *DefaultFullSharedAccessSignature* per l'hub di notifica.
* `direction` : deve essere impostato su *"out"*. 
* `platform` : la piattaforma delle proprietà indica la piattaforma di notifica a cui è indirizzata la notifica. Deve essere uno dei valori seguenti: 
  * `template` : piattaforma predefinita in caso di omissione della proprietà della piattaforma dall'associazione di uscita. È possibile utilizzare le notifiche modello per indirizzare qualsiasi piattaforma configurata sull'hub di notifica di Azure. Per ulteriori informazioni sull'utilizzo dei modelli in generale per inviare notifiche tra piattaforme con un hub di notifica di Azure, vedere [Modelli](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
  * `apns` : Apple Push Notification Service. Per ulteriori informazioni su come configurare l'hub di notifica per APN e ricevere la notifica in un'applicazione client, vedere [Invio di notifiche push a iOS con hub di notifica di Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
  * `adm` : [Amazon Device Messaging](https://developer.amazon.com/device-messaging). Per ulteriori informazioni su come configurare l'hub di notifica per ADM e ricevere la notifica in un'applicazione Kindle, vedere [Introduzione agli hub di notifica per le applicazioni Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
  * `gcm` : [Google Cloud Messaging](https://developers.google.com/cloud-messaging/). È supportato anche Firebase Cloud Messaging, la nuova versione di GCM. Per ulteriori informazioni su come configurare l'hub di notifica per GCM/FCM e ricevere la notifica in un'applicazione client Android, vedere [Invio di notifiche push ad Android con hub di notifica di Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
  * `wns` : [Servizio notifica Push Windows (WPNS)](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) indirizzato a piattaforme Windows. Anche Windows Phone 8.1 e versioni successive sono supportati da WNS. Per ulteriori informazioni su come configurare l'hub di notifica per WNS e ricevere la notifica in un'applicazione Universal Windows Platform (UWP), vedere [Introduzione agli hub di notifica per le applicazioni Universal Windows Platform](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
  * `mpns` : [Servizio di notifica push Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Questa piattaforma supporta piattaforme Windows Phone 8 e precedenti di Windows Phone. Per ulteriori informazioni su come configurare l'hub di notifica per APN e ricevere la notifica in un'applicazione Windows Phone, vedere [Invio di notifiche push con hub di notifica di Azure su Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)

Function.json di esempio:

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>Configurazione della stringa di connessione dell'hub di notifica
Per usare un'associazione di output dell'hub di notifica, è necessario configurare la stringa di connessione per l'hub. Nella scheda *Integra* è possibile selezionare quindi l'hub di notifica o crearne uno nuovo. 

È anche possibile aggiungere manualmente una stringa di connessione per un hub esistente aggiungendo una stringa di connessione per *DefaultFullSharedAccessSignature* all'hub di notifica. Questa stringa di connessione fornisce l'autorizzazione di accesso alla funzione per inviare messaggi di notifica. È possibile accedere al valore della stringa di connessione *DefaultFullSharedAccessSignature* dal pulsante **chiavi** nel pannello principale della risorsa dell'hub di notifica nel portale di Azure. Per aggiungere manualmente una stringa di connessione per l'hub, usare la procedura seguente: 

1. Nel pannello **App per le funzioni** del portale di Azure fare clic su **Impostazioni dell'app per le funzioni > Passa a Impostazioni del servizio app**.
2. Nel pannello **Impostazioni** fare clic su **Impostazioni applicazione**.
3. Scorrere verso il basso fino alla sezione **Stringhe di connessione** e aggiungere una voce chiamata per il valore *DefaultFullSharedAccessSignature* per l'hub di notifica. Impostare il tipo su **Personalizzato**.
4. Fare riferimento al nome della stringa di connessione nelle associazioni di output. È simile a **MyHubConnectionString** usato nell'esempio precedente.

## <a name="apns-native-notifications-with-c-queue-triggers"></a>Notifiche native APNS con trigger in coda C#
Questo esempio illustra come usare i tipi definiti nella [libreria di Hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) per inviare una notifica APNS nativa. 

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

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notifiche native GCM con trigger in coda C#
Questo esempio illustra come usare i tipi definiti nella [libreria di Hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) per inviare una notifica GCM nativa. 

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

## <a name="wns-native-notifications-with-c-queue-triggers"></a>Notifiche native WNS con trigger in coda C#
Questo esempio illustra come usare i tipi definiti nella [libreria di Hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) per inviare una notifica WNS popup nativa. 

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

## <a name="template-example-for-nodejs-timer-triggers"></a>Esempio di modello per i trigger timer Node.js
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

## <a name="template-example-for-f-timer-triggers"></a>Esempio di modello per i trigger timer F#
Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente `location` e `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>Esempio di modello che utilizza un parametro di uscita
Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente `message` un segnaposto nel modello.

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

## <a name="template-example-with-asynchronous-function"></a>Esempio di modello con funzione asincrona
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

## <a name="template-example-using-json"></a>Esempio di modello che utilizza JSON
Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente `message` un segnaposto nel modello tramite una stringa JSON valida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>Esempio di modello utilizzando i tipi di librerie degli hub di notifica
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

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


