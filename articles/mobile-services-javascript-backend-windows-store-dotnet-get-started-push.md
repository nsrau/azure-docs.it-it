<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Introduzione alle notifiche push in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Questo argomento illustra come usare Servizi mobili di Azure per inviare notifiche push a un'app Windows universale.
Questa esercitazione abilita le notifiche push mediante Hub di notifica di Azure per l'invio al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push utilizzando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

> [WACOM.NOTE]Questo argomento illustra come configurare manualmente le notifiche push usando Servizi mobili di Azure in un progetto di Windows Store. È possibile usare Visual Studio 2013 per aggiungere le stesse notifiche push in un progetto di app di Windows Store. Per altre informazioni, vedere la [versione di app Windows universale][versione di app Windows universale] di questa esercitazione.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili][Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili]
2.  [Aggiornamento dell'app per la registrazione per le notifiche][Aggiornamento dell'app per la registrazione per le notifiche]
3.  [Aggiornamento degli script del server per l'invio di notifiche push][Aggiornamento degli script del server per l'invio di notifiche push]
4.  [Inserimento di dati per la ricezione di notifiche push][Inserimento di dati per la ricezione di notifiche push]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati] per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione.

> [WACOM.NOTE]Questo argomento riguarda le app di Windows Phone Store 8.1. Per aggiungere notifiche push a un'app per Windows Phone 8 o Windows Phone Silverlight 8.1, vedere questa versione di [Introduzione alle notifiche push in Servizi mobili][Introduzione alle notifiche push in Servizi mobili].

## <span id="register"></span></a> Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Il servizio mobile e l'app sono ora configurati per funzionare con Servizi notifica Push Windows e Hub di notifica. A questo punto, si aggiornerà l'app di Windows Store universale per la registrazione per le notifiche.

## <span id="update-app"></span></a> Aggiornamento dell'app per la registrazione per le notifiche

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1.  In Visual Studio aprire il file App.xaml.cs e aggiungere le istruzioni `using` seguenti:

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Aggiungere il metodo seguente alla classe **App**:

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel =
                await PushNotificationChannelManager
                    .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
        }

    Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo per le notifiche push.

3.  All'inizio del gestore dell'evento **OnLaunched** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **InitNotificationsAsync**:

        InitNotificationsAsync();

    In questo modo la registrazione verrà richiesta ogni volta che viene caricata la pagina. Nell'app dell'utente può essere preferibile eseguire la registrazione solo su base periodica, per verificare che sia aggiornata.

4.  Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.

5.  (Facoltativo) Se non si utilizza il progetto di guida introduttiva generato nel portale di gestione, aprire il file Package.appxmanifest e verificare che nella scheda **Application UI** il valore **Toast capable** sia impostato su **Yes**.

    ![][0]

    In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup. Tali notifiche sono già abilitate nel progetto di guida introduttiva scaricato.

## <span id="update-scripts"></span></a> Aggiornamento degli script del server per l'invio di notifiche push

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>Esecuzione del test delle notifiche push nell'app

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app di Windows Store di usare Servizi mobili e Hub di notifica per l'invio di notifiche push. In seguito, è consigliabile eseguire una delle esercitazioni seguenti:

-   [Invio di notifiche push agli utenti autenticati][Invio di notifiche push agli utenti autenticati]
    Informazioni sull'uso dei tag per inviare notifiche push da un servizio mobile solo agli utenti autenticati.

-   [Invio di notifiche di trasmissione ai sottoscrittori][Invio di notifiche di trasmissione ai sottoscrittori]
    Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

-   [Invio di notifiche basate su modello ad altri sottoscrittori][Invio di notifiche basate su modello ad altri sottoscrittori]
    Informazioni su come usare i modelli per inviare notifiche push da un servizio mobile senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

-   [Informazioni su Hub di notifica][Informazioni su Hub di notifica]
    Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

-   [Riferimento per gli script server di Servizi mobili][Riferimento per gli script server di Servizi mobili]
    Ulteriori informazioni su come implementare la logica di business nel servizio mobile.



  [versione di app Windows universale]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili]: #register
  [Aggiornamento dell'app per la registrazione per le notifiche]: #update-app
  [Aggiornamento degli script del server per l'invio di notifiche push]: #update-scripts
  [Inserimento di dati per la ricezione di notifiche push]: #test
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Introduzione alle notifiche push in Servizi mobili]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [Invio di notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [Invio di notifiche di trasmissione ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
  [Invio di notifiche basate su modello ad altri sottoscrittori]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
  [Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Riferimento per gli script server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
