<properties 
	pageTitle="Aggiungere notifiche push all'app di Servizi mobili (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come usare Hub di notifica e Servizi mobili di Azure per inviare notifiche push all'app di Windows Store." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="glenga"/>


# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../../includes/mobile-services-selector-get-started-push-legacy.md)]

Questo argomento illustra come usare Servizi mobili di Azure per inviare notifiche push a un'app di Windows Store. In questa esercitazione si usa Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push utilizzando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

>[AZURE.NOTE]Questo argomento descrive come configurare manualmente le notifiche push mediante Servizi notifica Push Windows (WNS) per un'app di Windows Store. È possibile usare gli strumenti di Visual Studio 2013 per configurare automaticamente le stesse notifiche push in un progetto di app di Windows. Per altre informazioni, vedere la [versione di app Windows universale](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md) di questa esercitazione.

Questa esercitazione descrive le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app con Servizi notifica Push Windows e configurare Servizi mobili](#register)
2. [Aggiornare l'app per la registrazione per le notifiche](#update-app)
3. [Aggiornare gli script del server per l'invio di notifiche push](#update-scripts)
3. [Inserire dati per la ricezione di notifiche push](#test)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili] o [Introduzione ai dati] per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione.

##<a id="register"></a>Registrare l'app con Servizi notifica Push Windows e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Il servizio mobile e l'app sono ora configurati per funzionare con Servizi notifica Push Windows e Hub di notifica. A questo punto, si aggiornerà l'app di Windows Store per la registrazione per le notifiche.

##<a id="update-app"></a> Aggiornare l'app per la registrazione per le notifiche

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1. Aprire il file default.js e inserire il codice seguente dopo il codice che crea l'istanza **MobileServiceClient**:

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);                    
            });      

	Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo per le notifiche push.

2. Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.

6. Aprire il file Package.appxmanifest e verificare che nella scheda **Application UI** l'opzione **Toast capable** sia impostata su **Yes**.

   	![][2]

   	In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup.

##<a id="update-scripts"></a> Aggiornare gli script del server per l'invio di notifiche push

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Testare le notifiche push nell'app

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare Servizi mobili e Hub di notifica per l'invio delle notifiche push. In seguito si consiglia di passare all'esercitazione seguente, [Invio di notifiche push agli utenti autenticati], che spiega come utilizzare i tag per inviare notifiche push da un servizio mobile unicamente agli utenti autenticati.

<!---+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione ai dati] <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite servizi mobili.

* [Introduzione all'autenticazione] <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi tramite Servizi mobili.

* [Informazioni su Hub di notifica] <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Informazioni aggiuntive sulla risoluzione dei problemi e sul debug di soluzioni di Hub di notifica.

* [Riferimento per i concetti e le procedure di Servizi mobili con .NET] <br/>Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

* [Informazioni di riferimento sugli script del server di Servizi mobili] <br/>Altre informazioni su come implementare la logica di business nel servizio mobile.

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: ../mobile-services-windows-store-get-started.md
[Introduzione ai dati]: mobile-services-windows-store-javascript-get-started-data.md
[Introduzione all'autenticazione]: mobile-services-windows-store-javascript-get-started-users.md

[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Riferimento per i concetti e le procedure di Servizi mobili con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md


[Invio di notifiche push agli utenti autenticati]: mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users.md

[Informazioni su Hub di notifica]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-windows-store-javascript-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-windows-store-javascript-send-localized-breaking-news.md
 

<!---HONumber=July15_HO4-->