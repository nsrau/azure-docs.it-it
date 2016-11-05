---
title: Associazione di Hub di notifica in Funzioni di Azure | Microsoft Docs
description: Informazioni su come usare l'associazione di Hub di notifica di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: ''
tags: ''
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/19/2016
ms.author: wesmc

---
# Associazione di output di Hub di notifica in Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice di associazioni di Hub di notifica in Funzioni di Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Le funzioni possono inviare notifiche push usando un hub di notifica di Azure configurato con poche righe di codice. L'hub di notifica tuttavia deve essere configurato per i servizi di notifiche della piattaforma che si vuole usare. Per altre informazioni sulla configurazione di un hub di notifica di Azure e sullo sviluppo di applicazioni client che eseguono la registrazione per ricevere notifiche, vedere [Introduzione ad Hub di notifica per le app della piattaforma UWP (Universal Windows Platform)](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e fare clic sulla piattaforma client di destinazione nella parte superiore.

## function.json per associazione di output di Hub di notifica di Azure
Il file function.json specifica le proprietà seguenti:

* `name`: nome della variabile usato nel codice della funzione per il messaggio dell'hub di notifica.
* `type`: deve essere impostato su *"notificationHub"*.
* `tagExpression`: le espressioni tag consentono di specificare che le notifiche devono essere recapitate a un set di dispositivi che hanno eseguito la registrazione per ricevere le notifiche corrispondenti all'espressione tag. Per altre informazioni, vedere [Routing ed espressioni tag](../notification-hubs/notification-hubs-tags-segment-push-message.md).
* `hubName`: nome della risorsa dell'hub di notifica nel portale di Azure.
* `connection`: questa stringa di connessione deve essere una stringa di connessione dell'**impostazione dell'applicazione** configurata sul valore *DefaultFullSharedAccessSignature* per l'hub di notifica.
* `direction`: deve essere impostato su *"out"*.

Function.json di esempio:

    {
      "bindings": [
        {
          "name": "notification",
          "type": "notificationHub",
          "tagExpression": "",
          "hubName": "my-notification-hub",
          "connection": "MyHubConnectionString",
          "direction": "out"
        }
      ],
      "disabled": false
    }

## Configurazione della stringa di connessione dell'hub di notifica di Azure
Per usare un'associazione di output dell'hub di notifica, è necessario configurare la stringa di connessione per l'hub. Nella scheda *Integra* selezionare quindi l'hub di notifica o crearne uno nuovo.

È anche possibile aggiungere manualmente una stringa di connessione per un hub esistente aggiungendo una stringa di connessione per *DefaultFullSharedAccessSignature* all'hub di notifica. Questa stringa di connessione fornisce l'autorizzazione di accesso alla funzione per inviare messaggi di notifica. È possibile accedere al valore della stringa di connessione *DefaultFullSharedAccessSignature* dal pulsante **chiavi** nel pannello principale della risorsa dell'hub di notifica nel portale di Azure. Per aggiungere manualmente una stringa di connessione per l'hub, usare la procedura seguente:

1. Nel pannello **App per le funzioni** del portale di Azure fare clic su **Impostazioni dell'app per le funzioni > Passa a Impostazioni del servizio app**.
2. Nel pannello **Impostazioni** fare clic su **Impostazioni applicazione**.
3. Scorrere verso il basso fino alla sezione **Stringhe di connessione** e aggiungere una voce chiamata per il valore *DefaultFullSharedAccessSignature* per l'hub di notifica. Impostare il tipo su **Personalizzato**.
4. Fare riferimento al nome della stringa di connessione nelle associazioni di output. È simile a **MyHubConnectionString** usato nell'esempio precedente.

## Esempio di codice di hub di notifica di Azure per un trigger timer Node.js
Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente `location` e `message`.

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

## Esempio di codice di Hub di notifica di Azure per un trigger timer F
Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente `location` e `message`.

    let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
        notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]

## Esempio di codice di hub di notifica di Azure per un trigger della coda C
Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente `message`.

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

Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenente `message` usando una stringa JSON valida.

    using System;

    public static void Run(string myQueueItem,  out string notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = "{"message":"Hello from C#. Processed a queue item!"}";
    }

## Esempio di codice C# del trigger della coda dell'hub di notifica di Azure con il tipo Notification
Questo esempio illustra come usare il tipo `Notification` definito nella [libreria di Hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Per usare questo tipo e la libreria è necessario caricare un file *project.json* per l'app per le funzioni. Il file project.json è un file di testo JSON simile al seguente:

    {
      "frameworks": {
        ".NETFramework,Version=v4.6": {
          "dependencies": {
            "Microsoft.Azure.NotificationHubs": "1.0.4"
          }
        }
      }
    }

Per altre informazioni sul caricamento del file, vedere la sezione relativa al [caricamento di un file project.json](functions-reference.md#fileupdate).

Codice di esempio:

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

## Passaggi successivi
[!INCLUDE [Passaggi successivi](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->