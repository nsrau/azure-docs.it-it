<properties linkid="develop-mobile-tutorials-get-started-with-push-js" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (JavaScript)" metaKeywords="" description="Learn how to use push notifications in your Windows Store app with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Introduzione alle notifiche push in Servizi mobili tramite Visual Studio 2012
=============================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012 "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012 "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app di Windows Store. In questa esercitazione si utilizzerà Servizi notifica Push Windows per aggiungere notifiche push al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

**Nota**

In questa esercitazione verranno aggiunte le notifiche push a un'app di Windows Store creata in Visual Studio 2012. In Visual Studio 2013 sono disponibili nuove funzionalità che semplificano la configurazione delle notifiche push nell'app di Windows Store tramite Servizi mobili. Per la versione di Visual Studio 2013, vedere [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-js).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push e configurazione di Servizi mobili](#register)
2.  [Creazione della tabella Registrations](#create-table)
3.  [Aggiunta di notifiche push all'app](#add-push)
4.  [Aggiornamento degli script per l'invio di notifiche push](#update-scripts)
5.  [Inserimento di dati per la ricezione di notifiche](#test)

Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2012 Express per Windows 8
-   Account di Windows Store attivo

Questa esercitazione si basa sull'esercitazione [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-js). Prima di iniziare questa esercitazione, è necessario completare [l'esercitazione seguente](/en-us/develop/mobile/tutorials/get-started-with-data-js).

Registrazione dell'appRegistrazione dell'app di Windows Store
-------------------------------------------------------------

Per poter inviare notifiche push ad app di Windows Store da Servizi mobili, è necessario inviare l'app a Windows Store e quindi configurare il servizio mobile per l'integrazione con Servizi notifica Push Windows.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Il servizio mobile e l'app sono ora configurati per funzionare con Servizi notifica Push Windows. Ora si creerà una nuova tabella in cui archiviare le registrazioni.

Creazione di una nuova tabella
------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

Aggiunta di notifiche pushAggiunta di notifiche push all'app
------------------------------------------------------------

1.  Aprire il file default.js e inserire il frammento di codice seguente nell'overload del metodo **app.OnActivated**, subito dopo il metodo **args.setPromise**:

         // Get the channel for the application.
         var channel;
         var channelOperation = Windows.Networking.PushNotifications
             .PushNotificationChannelManager
             .createPushNotificationChannelForApplicationAsync()
             .then(function (newChannel) {
                 channel = newChannel;
             });

    Questo codice acquisisce e archivia un canale di notifica push ogni volta che l'applicazione viene avviata.

2.  Inserire il codice seguente dopo il codice che crea l'istanza **MobileServiceClient**:

         // Insert the new channel URI into the Registrations table.
         var registrationsTable = client.getTable('Registrations');
         registrationsTable.insert({ handle: channel.uri });         

    Questo codice inserisce il canale corrente nella tabella Registrations.

3.  Aprire il file Package.appxmanifest e verificare che nella scheda **Application UI** l'opzione **Toast capable** sia impostata su **Yes**.

    ![][15]

    In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup. 

Aggiornamento dello script insertAggiornamento dello script insert registrato nel portale di gestione
-----------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Fare clic su **TodoItem**, quindi su **Script** e selezionare **Insert**.

    ![][5]

2.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

		function insert(item, user, request) {
    		request.execute({
                 success: function() {
                    request.respond();
                    sendNotifications();
                }
			});

            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            push.wns.sendToastText04(registration.handle, {
                                text1: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
			}
         }

    Questo script insert invia una notifica push (con il testo dell'elemento inserito) a tutti i canali archiviati nella tabella **Registrations**.

Test dell'appEsecuzione del test delle notifiche push nell'app
--------------------------------------------------------------

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][13]

    Si noti che al termine dell'inserimento l'app riceve una notifica push da WNS.

    ![][14]

Passaggi successivi
-------------------

In questa esercitazione viene illustrata la funzionalità di notifica push di base fornita da Servizi mobili. Se per l'app sono richieste funzionalità più avanzate, ad esempio l'invio di notifiche tra piattaforme diverse, il routing basato sulla sottoscrizione o la gestione di volumi molto elevati, con il servizio mobile è consigliabile utilizzare Hub di notifica di Azure. Per ulteriori informazioni, vedere uno degli argomenti seguenti relativi a Hub di notifica:

-   [Introduzione ad Hub di notifica](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

-   [Invio di notifiche ai sottoscrittori](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

-   [Invio di notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>Informazioni su come inviare notifiche push da un servizio mobile a utenti specifici su qualsiasi dispositivo.

-   [Invio di notifiche su piattaforme diverse agli utenti](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>Informazioni su come utilizzare i modelli per inviare notifiche push da un servizio mobile, senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-js)
    <br/>Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    <br/>Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)
    <br/>Ulteriori informazioni su come utilizzare Servizi mobili con HTML e JavaScript.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Create the Registrations table]: #create-table
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->





[5]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-insert-script-push2.png







[13]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push2.png
[15]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-js
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012

[Azure Management Portal]: https://manage.windowsazure.com/
[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
