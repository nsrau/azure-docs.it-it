<properties 
	pageTitle="Introduzione alle notifiche push (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come usare Hub di notifica e Servizi mobili di Azure per inviare notifiche push all'app di Windows Store." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="glenga"/>


# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app di Windows universale. 
In questa esercitazione si userà Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push usando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere usato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere usato da altri servizi e applicazioni.

>[AZURE.NOTE]Questo argomento descrive come configurare manualmente le notifiche push usando Servizi mobili di Azure in un progetto di Windows Store. È possibile usare Visual Studio 2013 per aggiungere le stesse notifiche push in un progetto di app di Windows Store. Per altre informazioni, vedere la [versione per l'app di Windows universale](/it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push/) di questa esercitazione. 

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app con Servizi notifica Push Windows e configurare Servizi mobili](#register)
2. [Aggiornare l'app per la registrazione per le notifiche](#update-app)
3. [Aggiornare gli script del server per l'invio di notifiche push](#update-scripts)
3. [Inserire dati per la ricezione di notifiche push](#test)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili] o [Introduzione ai dati] per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione. 

>[AZURE.NOTE]Questo argomento riguarda le app di Windows Phone Store 8.1. Per aggiungere le notifiche push a un'app per Windows Phone 8 o Windows Phone Silverlight 8.1, vedere questa versione della guida [Introduzione alle notifiche push in Servizi mobili](/it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push).

##<a id="register"></a> Registrare l'app con Servizi notifica Push Windows e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Il servizio mobile e l'app sono ora configurati per funzionare con Servizi notifica Push Windows e Hub di notifica. A questo punto, si aggiornerà l'app di Windows Store universale per la registrazione per le notifiche.

##<a id="update-app"></a> Aggiornare l'app per la registrazione per le notifiche

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1. In Visual Studio aprire il file App.xaml.cs e aggiungere le istruzioni `using` seguenti:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Aggiungere il metodo seguente alla classe **App**: 
	
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
    
4. All'inizio del gestore eventi **OnLaunched** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **InitNotificationsAsync**:

        InitNotificationsAsync();

	In questo modo la registrazione verrà richiesta ogni volta che viene caricata la pagina. Nell'app dell'utente può essere preferibile eseguire la registrazione solo su base periodica, per verificare che sia aggiornata. 

5. Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.
  
6. (Facoltativo) Se non si usa il progetto di guida introduttiva generato nel portale di gestione, aprire il file Package.appxmanifest e verificare che nella scheda **Interfaccia utente dell'applicazione** l'opzione **Popup supportati** sia impostata su **Sì**.

   	![][2]

   	In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup. Tali notifiche sono già abilitate nel progetto di guida introduttiva scaricato.

##<a id="update-scripts"></a> Aggiornare gli script del server per l'invio di notifiche push

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Testare le notifiche push nell'app

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app di Windows Store di usare Servizi mobili e Hub di notifica per l'invio di notifiche push. In seguito, è consigliabile eseguire una delle esercitazioni seguenti:

+ [Inviare notifiche push agli utenti autenticati]
	<br/>Informazioni su come usare i tag per inviare notifiche push da un servizio mobile a un solo utente autenticato.

+ [Inviare notifiche di trasmissione ai sottoscrittori]
	<br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

+ [Inviare notifiche basate su modelli ai sottoscrittori]
	<br/>Informazioni su come usare i modelli per inviare notifiche push da un servizio mobile senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante servizi mobili.

* [Informazioni su Hub di notifica]
  <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Informazioni aggiuntive sulla risoluzione dei problemi e sul debug di soluzioni Hub di notifica. 

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con .NET.

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni su come implementare la logica di business nel servizio mobile.

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users

[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/

[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
[Inviare notifiche di trasmissione ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Inviare notifiche basate su modelli ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/


<!--HONumber=42-->
