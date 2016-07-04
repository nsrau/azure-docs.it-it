<properties
	pageTitle="Uso di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse"
	description="Informazioni su come usare i modelli di Hub di notifica per inviare, in un'unica richiesta, una notifica indipendente dalla piattaforma destinata a tutte le piattaforme."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/28/2016" 
	ms.author="wesmc"/>

# Uso di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse


Nell'esercitazione precedente, [Utilizzo di Hub di notifica per inviare notifiche agli utenti], si è appreso come inviare notifiche push a tutti i dispositivi registrati da uno specifico utente autenticato. In tale esercitazione vengono utilizzate più richieste per inviare una notifica a ogni piattaforma client supportata. Hub di notifica supporta i modelli, che consentono di specificare il modo in cui un dispositivo desidera ricevere notifiche. Questa capacità semplifica l'invio di notifiche tra piattaforme diverse. Questo argomento descrive come servirsi dei modelli per inviare, in un'unica richiesta, una notifica indipendente dalla piattaforma destinata a tutte le piattaforme. Per informazioni dettagliate sui modelli, vedere le [informazioni generali sugli hub di notifica di Azure][Templates].

> [AZURE.NOTE] Hub di notifica consente a un dispositivo di registrare più modelli con lo stesso tag. In questo caso, un messaggio in arrivo destinato a tale tag ha come esito il recapito al dispositivo di più notifiche, una per ogni modello. Questo consente di visualizzare lo stesso messaggio in più notifiche visive, ad esempio sia come notifica badge che come notifica di tipo avviso popup in un'app di Windows Store.

Per inviare notifiche tra piattaforme diverse utilizzando i modelli, eseguire la procedure seguente:

1. In Esplora soluzioni in Visual Studio espandere la cartella **Controllers** e quindi aprire il file RegisterController.cs.

2. Nel metodo **Post** individuare il blocco di codice che crea una nuova registrazione e sostituire il contenuto `switch` con il codice seguente:

		switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version="1.0" encoding="utf-8"?>" +
                    "<wp:Notification xmlns:wp="WPNotification">" +
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
                var alertTemplate = "{"aps":{"alert":"$(message)"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{"data":{"msg":"$(message)"}}";
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

	Questo codice invia una notifica a tutte le piattaforme nello stesso momento e senza bisogno di specificare un payload nativo. Hub di notifica crea il payload corretto e lo distribuisce a tutti i dispositivi con il valore _tag_ fornito, come specificato nei modelli registrati.

4. Pubblicare di nuovo il progetto back-end WebApi.

5. Eseguire nuovamente l'app client e verificare che la registrazione abbia esito positivo.

6. (Facoltativo) Distribuire l'app client in un secondo dispositivo, quindi eseguirla.

	Si noti che verrà visualizzata una notifica su ogni dispositivo.

## Passaggi successivi

Dopo avere completato questa esercitazione, è possibile reperire altre informazioni su Hub di notifica e sui modelli nei seguenti argomenti:

+ **[Usare Hub di notifica per inviare le ultime notizie]** <br/>Questo argomento descrive un altro scenario per l'uso dei modelli.

+  **[Panoramica di hub di Hub di notifica di Azure][Templates]**<br/> Panoramica con informazioni dettagliate sui modelli.


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Usare Hub di notifica per inviare le ultime notizie]: notification-hubs-windows-store-dotnet-send-breaking-news.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Utilizzo di Hub di notifica per inviare notifiche agli utenti]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx

<!---HONumber=AcomDC_0622_2016-->