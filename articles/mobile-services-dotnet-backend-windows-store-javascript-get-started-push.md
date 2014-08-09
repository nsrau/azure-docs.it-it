<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

Introduzione alle notifiche push in Servizi mobili
==================================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push "Windows Store JavaScript")
[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/ "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure con un back-end .NET per inviare notifiche push a un'app di Windows Store. In questa esercitazione si utilizzerà Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push dal back-end .NET utilizzando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

> [WACOM.NOTE]L'integrazione di Servizi mobili con Hub di notifica è attualmente in versione di anteprima ed è disponibile solo per le piattaforme Windows. Nel frattempo, è comunque possibile inviare notifiche push a dispositivi iOS e Android dal servizio back-end .NET tramite l'hub di notifica connesso, come illustrato in **Introduzione ad Hub di notifica** ([iOS](/it-it/documentation/articles/notification-hubs-ios-get-started)/[Android](/it-it/documentation/articles/notification-hubs-android-get-started)).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili](#register)
2.  [Aggiornamento dell'app per la registrazione per le notifiche](#update-app)
3.  [Aggiornamento del server per l'invio di notifiche push](#update-server)
4.  [Inserimento di dati per la ricezione di notifiche push](#test)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-windows-store-get-started) o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data) per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione.

Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili
-------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app](../includes/mobile-services-javascript-backend-register-windows-store-app.md)]

Il servizio mobile e l'app sono ora configurati per funzionare con Servizi notifica Push Windows e Hub di notifica. A questo punto, si aggiornerà l'app di Windows Store per la registrazione per le notifiche.

Aggiornamento dell'app per la registrazione per le notifiche
------------------------------------------------------------

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1.  Aprire il file default.js e inserire il codice seguente dopo il codice che crea l'istanza **MobileServiceClient**. Questo codice crea un canale di notifica push ed effettua la registrazione per le notifiche push:

         // Request a push notification channel.
         Windows.Networking.PushNotifications
             .PushNotificationChannelManager
             .createPushNotificationChannelForApplicationAsync()
             .then(function (channel) {
                 // Register for notifications using the new channel
                 client.push.registerNative(channel.uri);
             }, function (error) {
                 var message = "Registration failed: " + error.message;
                 var dialog = new Windows.UI.Popups.MessageDialog(message);
                 dialog.showAsync();
             });

    Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo per le notifiche push. Se la registrazione ha esito negativo, il messaggio di errore verrà visualizzato in una finestra di dialogo con messaggio.

2.  In Visual Studio aprire il file Package.appxmanifest e verificare che nella scheda **Application UI** l'opzione **Toast capable** sia impostata su **Yes**.

    ![][1]

    In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup. Tali notifiche sono già abilitate nel progetto di guida introduttiva scaricato.

Aggiornamento del server per l'invio di notifiche push
------------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

Esecuzione del test delle notifiche push nell'app
-------------------------------------------------

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][2]

    Si noti che al termine dell'inserimento l'app riceve una notifica push da WNS.

    ![][3]

Passaggi successivi
-------------------

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Introduzione ad Hub di notifica](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/) <br/>
    Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

-   [Invio di notifiche ai sottoscrittori](/en-us/manage/services/notification-hubs/breaking-news-dotnet/) <br/>
    Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

-   [Invio di notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users/) <br/>
    Informazioni su come inviare notifiche push da un servizio mobile a utenti specifici su qualsiasi dispositivo.

-   [Invio di notifiche su piattaforme diverse agli utenti](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/) <br/>
    Informazioni su come utilizzare i modelli per inviare notifiche push da un servizio mobile, senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data) <br/>
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante i servizi mobili di runtime .NET.

-   [Introduzione all'autenticazione](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users) <br/>
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili di runtime .NET.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293) <br/>
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET](/it-it/documentation/articles/mobile-services-html-how-to-use-client-library) <br/>
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.


<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /it-it/documentation/articles/mobile-services-windows-store-get-started
[Get started with data]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Get started with authentication]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[Get started with push notifications]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push

[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library