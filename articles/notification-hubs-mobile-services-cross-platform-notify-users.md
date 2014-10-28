<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Utilizzo di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/notification-hubs-mobile-services-cross-platform-notify-users/" title="Mobile Services" class="current">Servizi mobili</a>
    <a href="/it-it/documentation/articles/notification-hubs-aspnet-cross-platform-notify-users/" title="ASP.NET">ASP.NET</a>
</div>

Nell'esercitazione precedente, [Utilizzo di Hub di notifica per inviare notifiche agli utenti][Utilizzo di Hub di notifica per inviare notifiche agli utenti], si è appreso come inviare notifiche push a tutti i dispositivi registrati da uno specifico utente autenticato. In tale esercitazione vengono utilizzate più richieste per inviare una notifica a ogni piattaforma client supportata. Hub di notifica supporta i modelli, che consentono di specificare il modo in cui un dispositivo desidera ricevere notifiche. Questa capacità semplifica l'invio di notifiche tra piattaforme diverse. Questo argomento descrive come servirsi dei modelli per inviare, in un'unica richiesta, una notifica indipendente dalla piattaforma destinata a tutte le piattaforme. Per informazioni dettagliate sui modelli, vedere le [informazioni generali sugli hub di notifica di Azure][informazioni generali sugli hub di notifica di Azure].

<div class="dev-callout"><b>Nota</b>
    <p>Hub di notifica consente a un dispositivo di registrare pi&ugrave; modelli con lo stesso tag. In questo caso, un messaggio in arrivo destinato a tale tag ha come esito il recapito al dispositivo di pi&ugrave; notifiche, una per ogni modello. Questo consente di visualizzare lo stesso messaggio in pi&ugrave; notifiche visive, ad esempio sia come notifica badge che come notifica di tipo avviso popup in un'app di Windows Store.</p>
</div>

Per inviare notifiche tra piattaforme diverse usando i modelli, eseguire la procedure seguente:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

    ![][]

2.  Fare clic sulla scheda **API**, quindi sulla voce **register\_notifications** nella tabella dell'API.

    ![][1]

3.  Fare clic sulla scheda **Script**, individuare il blocco **else** che crea una nuova registrazione quando il valore di `existingRegs` è **false**, quindi sostituirlo con il codice seguente:

        else {
            // Create a new registration.
            var template;
            if (platform === 'win8') {                
                template = { text1: '$(message)' };              
                hub.wns.createToastText01Registration(request.body.channelUri, 
                [userId, installationId], template, registrationComplete);
            } else if (platform === 'ios') {
                template = '{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}';
                hub.apns.createTemplateRegistration(request.body.deviceToken, 
                [userId, installationId], template, registrationComplete);
            } else {
                response.send(500, 'Unknown client.');
            }
        }

    In questo codice viene chiamato il metodo specifico della piattaforma per creare una registrazione modello anziché una registrazione nativa. Non è necessario modificare le registrazioni esistenti, in quanto le registrazioni modello derivano da registrazioni native.

4.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][2]

5.  In **todoitem** fare clic sulla scheda **Script**, selezionare **Insert** e quindi sostituire la funzione **insert** esistente con il codice seguente:

    ![][3]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

6.  Sostituire la funzione insert con il codice seguente:

        function insert(item, user, request) {
            var azure = require('azure');
            var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
            '<FULL_SAS_CONNECTION_STRING>');

            // Execute the request and send notifications.
            request.execute({
                success: function() {
                    // Write the default response and send a notification 
                    // to the user on all devices by using the userId tag.
                    request.respond();
                    // Create a template-based payload.
                    var payload = '{ "message" : "New item added: ' + item.text + '" }';            
                    // Send a notification to the current user on all platforms. 
                    hub.send(user.userId, payload,  
                    function(error, outcome){
                        // Do something here with the outcome or error.
                    });     
                }
            });
        }

    Questo codice invia una notifica a tutte le piattaforme nello stesso momento e senza bisogno di specificare un payload nativo. Hub di notifica crea il payload corretto e lo distribuisce a tutti i dispositivi con il valore *tag* fornito, come specificato nei modelli registrati.

7.  Aggiornare lo script in modo da sostituire *`<NOTIFICATION_HUB_NAME>`* e *`<FULL_SAS_CONNECTION_STRING>`* con i valori per l'hub di notifica, quindi fare clic su **Salva**.

8.  Arrestare l'emulatore di dispositivo o disinstallare l'app client esistente dal dispositivo.

    In questo modo si avrà la certezza che il client di Servizi mobili generi un nuovo ID di installazione. In caso contrario, il servizio tenterà di usare la registrazione non modello esistente.

9.  Distribuire ed eseguire nuovamente l'app client e verificare che la registrazione abbia esito positivo e che venga visualizzato un nuovo ID di registrazione.

10. Immettere il testo come in precedenza e aggiungere il nuovo elemento.

    Si noti che al termine dell'inserimento l'app riceve una notifica push da Hub di notifica.

11. (Facoltativo) Distribuire l'app client in un secondo dispositivo, quindi eseguirla e inserire testo.

    Si noti che verrà visualizzata una notifica su ogni dispositivo.

## Passaggi successivi

Dopo avere completato questa esercitazione, è possibile reperire ulteriori informazioni su Hub di notifica e sui modelli negli argomenti seguenti:

-   **Utilizzo di Hub di notifica per inviare le ultime notizie ([Windows Store C#][Windows Store C#] / [iOS][iOS])**
    Questo argomento descrive un altro scenario per l'uso dei modelli

-   **[Informazioni generali sugli hub di notifica di Azure][informazioni generali sugli hub di notifica di Azure]**
    Panoramica con informazioni dettagliate sui modelli.

-   **[Procedure di Hub di notifica per Windows Store][Procedure di Hub di notifica per Windows Store]**
    Include un riferimento al linguaggio di espressione dei modelli.



  [Servizi mobili]: /it-it/documentation/articles/notification-hubs-mobile-services-cross-platform-notify-users/ "Mobile Services"
  [ASP.NET]: /it-it/documentation/articles/notification-hubs-aspnet-cross-platform-notify-users/ "ASP.NET"
  [Utilizzo di Hub di notifica per inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users
  [informazioni generali sugli hub di notifica di Azure]: http://go.microsoft.com/fwlink/p/?LinkId=317339
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
  [1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
  [2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
  [3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
  [Windows Store C#]: /it-it/manage/services/notification-hubs/breaking-news-dotnet
  [iOS]: /it-it/manage/services/notification-hubs/breaking-news-ios
  [Procedure di Hub di notifica per Windows Store]: http://msdn.microsoft.com/it-it/library/windowsazure/jj927172.aspx
