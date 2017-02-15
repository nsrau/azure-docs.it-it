---
title: Uso di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse
description: Informazioni su come usare i modelli di Hub di notifica per inviare, in un&quot;unica richiesta, una notifica indipendente dalla piattaforma destinata a tutte le piattaforme.
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0cc620324c75ed6ffee26fe442014d61673ba1e6


---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Uso di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse
Nell'esercitazione precedente, [Utilizzo di Hub di notifica per inviare notifiche agli utenti], si è appreso come inviare notifiche push a tutti i dispositivi registrati da uno specifico utente autenticato. In tale esercitazione vengono utilizzate più richieste per inviare una notifica a ogni piattaforma client supportata. Hub di notifica supporta i modelli, che consentono di specificare il modo in cui un dispositivo desidera ricevere notifiche. Questa capacità semplifica l'invio di notifiche tra piattaforme diverse. Questo argomento descrive come servirsi dei modelli per inviare, in un'unica richiesta, una notifica indipendente dalla piattaforma destinata a tutte le piattaforme. Per informazioni dettagliate sui modelli, vedere [Panoramica dell'Hub di notifica][Modelli].

> [!NOTE]
> Hub di notifica consente a un dispositivo di registrare più modelli con lo stesso tag. In questo caso, un messaggio in arrivo destinato a tale tag ha come esito il recapito al dispositivo di più notifiche, una per ogni modello. Questo consente di visualizzare lo stesso messaggio in più notifiche visive, ad esempio sia come notifica badge che come notifica di tipo avviso popup in un'app di Windows Store.
> 
> 

Per inviare notifiche tra piattaforme diverse utilizzando i modelli, eseguire la procedure seguente:

1. In Esplora soluzioni in Visual Studio espandere la cartella **Controllers** e quindi aprire il file RegisterController.cs.
2. Nel metodo **Post** individuare il blocco di codice che crea una nuova registrazione e sostituire il contenuto `switch` con il codice seguente:
   
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
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    In questo codice viene chiamato il metodo specifico della piattaforma per creare una registrazione modello anziché una registrazione nativa. Non è necessario modificare le registrazioni esistenti, in quanto le registrazioni modello derivano da registrazioni native.
3. Nel controller **Notifications** sostituire il metodo **sendNotification** con il codice seguente:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Questo codice invia una notifica a tutte le piattaforme nello stesso momento e senza bisogno di specificare un payload nativo. Gli hub di notifica creano il payload corretto e lo distribuiscono a tutti i dispositivi con il valore *tag* specificato, come indicato nei modelli registrati.
4. Pubblicare di nuovo il progetto back-end WebApi.
5. Eseguire nuovamente l'app client e verificare che la registrazione abbia esito positivo.
6. (Facoltativo) Distribuire l'app client in un secondo dispositivo, quindi eseguirla.
   
    Si noti che verrà visualizzata una notifica su ogni dispositivo.

## <a name="next-steps"></a>Passaggi successivi
Dopo avere completato questa esercitazione, è possibile reperire altre informazioni su Hub di notifica e sui modelli nei seguenti argomenti:

* **[Use Notification Hubs to send breaking news]** (Usare gli hub di notifica per inviare le ultime notizie) <br/>Questo argomento descrive un altro scenario per l'uso dei modelli.
* **[Panoramica dell'Hub di notifica][Modelli]**<br/> Panoramica con informazioni dettagliate sui modelli.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Effettuare il push agli utenti - ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Effettuare il push agli utenti - Servizi mobili]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express per Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Hub di notifica di Azure]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Utilizzo di Hub di notifica per inviare notifiche agli utenti]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Modelli]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Procedure di Hub di notifica per Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx



<!--HONumber=Nov16_HO3-->


