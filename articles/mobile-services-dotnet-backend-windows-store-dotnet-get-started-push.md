<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal" />

# Introduzione alle notifiche push in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Questo argomento illustra come usare Servizi mobili di Azure con un back-end .NET per inviare notifiche push a un'app Windows universale. Questa esercitazione usa Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva di Windows universale. Al termine dell'esercitazione, il servizio mobile invierà una notifica push dal back-end .NET utilizzando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

> [WACOM.NOTE]Questo argomento illustra come configurare manualmente le notifiche push mediante Servizi notifica Push Windows (WNS) per un'app di Windows Store. È possibile usare gli strumenti di Visual Studio 2013 per configurare automaticamente le stesse notifiche push in un progetto di app Windows. Per altre informazioni, vedere la [versione di app Windows universale][versione di app Windows universale] di questa esercitazione.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili][Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili]
2.  [Aggiornamento dell'app per la registrazione per le notifiche][Aggiornamento dell'app per la registrazione per le notifiche]
3.  [Aggiornare il server per l'invio di notifiche push][Aggiornare il server per l'invio di notifiche push]
4.  [Abilitare le notifiche push per test locali][Abilitare le notifiche push per test locali]
5.  [Inserimento di dati per la ricezione di notifiche push][Inserimento di dati per la ricezione di notifiche push]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati] per collegare il progetto al servizio mobile.

> [WACOM.NOTE]Questo argomento riguarda le app di Windows Phone Store 8.1. Per aggiungere notifiche push a un'app per Windows Phone 8 o Windows Phone Silverlight 8.1, vedere questa versione di [Introduzione alle notifiche push in Servizi mobili][Introduzione alle notifiche push in Servizi mobili].

## <span id="register"></span></a> Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Il servizio mobile e le app sono ora configurati per il funzionamento con Servizi di notifica Push Windows e Hub di notifica. A questo punto, si aggiornerà l'app Windows universale per la registrazione per le notifiche.

## <span id="update-app"></span></a> Aggiornamento dell'app per la registrazione per le notifiche

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1.  In Visual Studio aprire il file App.xaml.cs e aggiungere le istruzioni `using` seguenti:

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Aggiungere il metodo `InitNotificationAsync` seguente alla classe **App** per creare un canale per notifica Push ed effettuare la registrazione per le notifiche Push:

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo per le notifiche push.

3.  All'inizio del gestore dell'evento **OnLaunched** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **InitNotificationsAsync**:

        InitNotificationsAsync();

    In questo modo la registrazione verrà richiesta ogni volta che viene caricata la pagina. Nell'app dell'utente può essere preferibile eseguire la registrazione solo su base periodica, per verificare che sia aggiornata.

4.  In Visual Studio aprire il file Package.appxmanifest e verificare che nella scheda **Application UI** l'opzione **Toast capable** sia impostata su **Yes**. Salvare il file.

    ![][0]

    In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup.

## <span id="update-server"></span></a>Aggiornare il server per l'invio di notifiche push

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a> Abilitare le notifiche push per test locali

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a>Esecuzione del test delle notifiche push nell'app

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app di Windows Store di usare Servizi mobili e Hub di notifica per l'invio di notifiche push. In seguito, è consigliabile eseguire l'esercitazione seguente, [Invio di notifiche push agli utenti autenticati][Invio di notifiche push agli utenti autenticati], che mostra come usare i tag per inviare notifiche push da un servizio mobile solo agli utenti autenticati.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

-   [Informazioni su Hub di notifica][Informazioni su Hub di notifica]
    Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.



  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [versione di app Windows universale]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Registrazione dell'app con Servizi notifica Push Windows e configurazione di Servizi mobili]: #register
  [Aggiornamento dell'app per la registrazione per le notifiche]: #update-app
  [Aggiornare il server per l'invio di notifiche push]: #update-server
  [Abilitare le notifiche push per test locali]: #local-testing
  [Inserimento di dati per la ricezione di notifiche push]: #test
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Introduzione alle notifiche push in Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Invio di notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library
