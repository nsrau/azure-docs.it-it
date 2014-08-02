<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Notify Users xplat aspnet" pageTitle="Send cross-platform notifications to users with Notification Hubs (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Utilizzo di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse
=====================================================================================

[Servizi mobili](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/ "Servizi mobili")[ASP.NET](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet/ "ASP.NET")

Nell'esercitazione precedente, [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet), si è appreso come inviare notifiche push a tutti i dispositivi registrati da uno specifico utente autenticato. In tale esercitazione vengono utilizzate più richieste per inviare una notifica a ogni piattaforma client supportata. Hub di notifica supporta i modelli, che consentono di specificare il modo in cui un dispositivo desidera ricevere notifiche. Questa capacità semplifica l'invio di notifiche tra piattaforme diverse. In questo argomento viene illustrato come servirsi dei modelli per inviare, in un'unica richiesta, una notifica indipendente dalla piattaforma destinata a tutte le piattaforme. Per informazioni dettagliate sui modelli, vedere le [informazioni generali sugli hub di notifica di Azure](http://go.microsoft.com/fwlink/p/?LinkId=317339).

**Nota**

Hub di notifica consente a un dispositivo di registrare più modelli con lo stesso tag. In questo caso, un messaggio in arrivo destinato a tale tag ha come esito il recapito al dispositivo di più notifiche, una per ogni modello. Questo consente di visualizzare lo stesso messaggio in più notifiche visive, ad esempio sia come notifica badge che come notifica di tipo avviso popup in un'app di Windows Store.

Per inviare notifiche tra piattaforme diverse utilizzando i modelli, eseguire la procedure seguente:

1.  In Esplora soluzioni in Visual Studio espandere la cartella **Controllers** e quindi aprire il file RegisterController.cs.

2.  Nel metodo **Post** individuare il blocco di codice che crea una nuova registrazione quando il valore di `updated` è **false**, quindi sostituirlo con il codice seguente:

		if (!updated)
        {
            switch (platform)
            {
                case "windows":
                    var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                    await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });
                    break;
                case "ios":
                    template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";
                    await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });
                    break;
            } 
        }

    In questo codice viene chiamato il metodo specifico della piattaforma per creare una registrazione modello anziché una registrazione nativa. Non è necessario modificare le registrazioni esistenti, in quanto le registrazioni modello derivano da registrazioni native.

3.  Sostituire il metodo **sendNotification** con il codice seguente:

         // Send a cross-plaform notification by using templates. 
         private async Task sendNotification(string notificationText, string tag)
         {           
                 var notification = new Dictionary<string, string> { { "message", "Hello, " + tag } };
                 await hubClient.SendTemplateNotificationAsync(notification, tag);        
         }

    Questo codice invia una notifica a tutte le piattaforme nello stesso momento e senza bisogno di specificare un payload nativo. Hub di notifica crea il payload corretto e lo distribuisce a tutti i dispositivi con il valore *tag* fornito, come specificato nei modelli registrati.

4.  Eseguire nuovamente l'app client e verificare che la registrazione abbia esito positivo.

5.  (Facoltativo) Distribuire l'app client in un secondo dispositivo, quindi eseguirla.

    Si noti che verrà visualizzata una notifica su ogni dispositivo.

Passaggi successivi
-------------------

Dopo avere completato questa esercitazione, è possibile reperire ulteriori informazioni su Hub di notifica e sui modelli negli argomenti seguenti:

-   **Utilizzo di Hub di notifica per inviare le ultime notizie ([Windows Store C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-dotnet))**
    In questo argomento viene illustrato un altro scenario per l'utilizzo dei modelli

-   **[Informazioni generali sugli hub di notifica di Azure](http://go.microsoft.com/fwlink/p/?LinkId=317339)**
     Panoramica con informazioni dettagliate sui modelli.

-   **[Procedure di Hub di notifica per Windows Store](http://msdn.microsoft.com/en-us/library/windowsazure/jj927172.aspx)**
    Include un riferimento al linguaggio di espressione dei modelli.


