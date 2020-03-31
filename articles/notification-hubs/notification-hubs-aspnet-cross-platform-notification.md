---
title: Inviare notifiche multipiattaforma agli utenti con Hub di notifica di Azure (ASP.NET)
description: Informazioni su come usare i modelli di Hub di notifica per inviare, in un'unica richiesta, una notifica indipendente dalla piattaforma destinata a tutte le piattaforme.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127040"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Inviare notifiche multipiattaforma con Hub di notifica di AzureSend cross-platform notifications with Azure Notification Hubs

Questa esercitazione si basa sull'esercitazione [precedente, inviare notifiche a utenti specifici tramite Hub di notifica]di Azure.This tutorial buildon on the previous tutorial, Send notifications to specific users by using Azure Notification Hubs . In questa esercitazione viene descritto come inviare notifiche a tutti i dispositivi registrati per un utente autenticato specifico. Tale approccio richiedeva più richieste per inviare una notifica a ogni piattaforma client supportata. Hub di notifica di Azure supporta modelli che consentono di specificare il modo in cui un determinato dispositivo vuole ricevere notifiche. Questo metodo semplifica l'invio di notifiche multipiattaforma.

In questo articolo viene illustrato come sfruttare i modelli per inviare una notifica destinata a tutte le piattaforme. Questo articolo usa una singola richiesta per inviare una notifica indipendente dalla piattaforma. Per informazioni più dettagliate sui modelli, vedere [Cenni preliminari][Templates]sugli hub di notifica .

> [!IMPORTANT]
> I progetti Windows Phone 8.1 e versioni precedenti non sono supportati in Visual Studio 2019. Per altre informazioni, vedere [Selezione della piattaforma e compatibilità di Visual Studio 2019](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Con Hub di notifica, un dispositivo può registrare più modelli con lo stesso tag. In questo caso, un messaggio in arrivo destinato a tale tag determina il recapito di più notifiche al dispositivo, una per ogni modello. Questo processo consente di visualizzare lo stesso messaggio in più notifiche visive, ad esempio sia come notifica che come avviso popup in un'app di Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Inviare notifiche multipiattaforma usando i modelli

Questa sezione usa il codice di esempio compilato nell'esercitazione Invia notifiche a utenti specifici usando Hub di notifica di [Azure.This] section uses the sample code you built in the Send notifications to specific users by using Azure Notification Hubs tutorial. È possibile scaricare l'esempio da [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Per inviare notifiche multipiattaforma utilizzando i modelli, eseguire la procedura seguente:To send cross-platform notifications by using templates, do the following steps:

1. In Visual Studio in **Esplora soluzioni**espandere la cartella **Controllers** e quindi aprire il file *di RegisterController.cs.*

1. Trovare il blocco di codice nel metodo `Put` che crea una nuova registrazione e quindi sostituire il contenuto di `switch` con il codice seguente:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    In questo codice viene chiamato il metodo specifico della piattaforma per creare una registrazione modello anziché una registrazione nativa. Dato che le registrazioni dei modelli derivano da registrazioni native, non è necessario modificare le registrazioni esistenti.

1. In **Esplora soluzioni**, nella cartella **Controller** , aprire il *file NotificationsController.cs.* Sostituire il metodo `Post` con il codice seguente:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Questo codice invia una notifica a tutte le piattaforme contemporaneamente. Non si specifica un payload nativo. Gli hub di notifica creano il payload corretto e lo distribuiscono a tutti i dispositivi con il valore tag specificato, come indicato nei modelli registrati.

1. Ripubblicare il progetto API Web.

1. Eseguire nuovamente l'app client per verificare che la registrazione sia stata eseguita correttamente.

1. Facoltativamente, distribuire l'app client in un secondo dispositivo e quindi eseguire l'app. Verrà visualizzata una notifica su ogni dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato questa esercitazione, scopri di più sugli hub di notifica e sui modelli in questi articoli:Now that you've completed this tutorial, find more about Notification Hubs and templates in these articles:

* Per uno scenario diverso per l'utilizzo dei modelli, vedere l'esercitazione [Notifiche push a dispositivi Windows specifici che eseguono applicazioni con piattaforma Windows universale.][Use Notification Hubs to send breaking news]
* Per informazioni più dettagliate sui modelli, vedere [Cenni preliminari][Templates]sugli hub di notifica.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Inviare notifiche a utenti specifici con Hub di notifica di Azure]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
