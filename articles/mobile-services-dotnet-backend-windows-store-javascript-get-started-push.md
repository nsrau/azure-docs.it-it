<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Introduzione alle notifiche push in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][]]

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure con un back-end .NET per inviare notifiche push a un'app di Windows Store. In questa esercitazione si utilizzerà Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push dal back-end .NET utilizzando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

> [WACOM.NOTE]Questo argomento illustra come configurare manualmente le notifiche push mediante Servizi notifica Push Windows (WNS) per un'app di Windows Store. È possibile usare gli strumenti di Visual Studio 2013 per configurare automaticamente le stesse notifiche push in un progetto di app Windows. Per altre informazioni, vedere la [versione di app Windows universale][] di questa esercitazione.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili][]
2.  [Aggiornamento dell'app per la registrazione per le notifiche][]
3.  [Aggiornare il server per l'invio di notifiche push][]
4.  [Abilitare le notifiche push per test locali][]
5.  [Inserimento di dati per la ricezione di notifiche push][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili][] o [Introduzione ai dati][] per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione.

## <span id="register"></span></a> Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app][]]

Il servizio mobile e l'app sono ora configurati per funzionare con Servizi notifica Push Windows e Hub di notifica. A questo punto, si aggiornerà l'app di Windows Store per la registrazione per le notifiche.

## <span id="update-app"></span></a> Aggiornamento dell'app per la registrazione per le notifiche

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

    ![][]

    In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup. Tali notifiche sono già abilitate nel progetto di guida introduttiva scaricato.

## <span id="update-server"></span></a>Aggiornare il server per l'invio di notifiche push

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push][]]

## <span id="local-testing"></span></a> Abilitare le notifiche push per test locali

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][]]

## <span id="test"></span></a>Esecuzione del test delle notifiche push nell'app

[WACOM.INCLUDE [mobile-services-windows-store-test-push][]]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app di Windows Store di usare Servizi mobili e Hub di notifica per l'invio di notifiche push. In seguito, è consigliabile eseguire una delle esercitazioni seguenti:

-   [Invio di notifiche push agli utenti autenticati][]
    Informazioni sull'uso dei tag per inviare notifiche push da un servizio mobile solo agli utenti autenticati.

Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili e Hub di notifica:

-   [Introduzione ai dati][]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

-   [Informazioni su Hub di notifica][]
    Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

-   [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript][]
    Ulteriori informazioni su come usare Servizi mobili con app JavaScript.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [versione di app Windows universale]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push
  [Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili]: #register
  [Aggiornamento dell'app per la registrazione per le notifiche]: #update-app
  [Aggiornare il server per l'invio di notifiche push]: #update-server
  [Abilitare le notifiche push per test locali]: #local-testing
  [Inserimento di dati per la ricezione di notifiche push]: #test
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  []: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Invio di notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
  [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library
