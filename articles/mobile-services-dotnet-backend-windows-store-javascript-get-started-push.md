<properties 
	pageTitle="Introduzione alle notifiche push (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come usare i servizi mobili di runtime .NET di Azure e Hub di notifica per inviare notifiche push all'app JavaScript per Windows Store." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="wesmc"/>


# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Questo argomento descrive come usare Servizi mobili di Azure con un back-end .NET per inviare notifiche push a un'app di Windows Store. In questa esercitazione si userà Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push dal back-end .NET usando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere usato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere usato da altri servizi e applicazioni.

>[AZURE.NOTE] Questo argomento descrive come configurare manualmente le notifiche push mediante Servizi notifica Push Windows (WNS) per un'app di Windows Store. È possibile usare gli strumenti di Visual Studio 2013 per configurare automaticamente le stesse notifiche push in un progetto di app di Windows. Per altre informazioni, vedere la [versione per l'app di Windows universale](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push) di questa esercitazione.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app con Servizi notifica Push Windows e configurare Servizi mobili](#register)
2. [Aggiornare l'app per la registrazione per le notifiche](#update-app)
3. [Aggiornare il server per l'invio di notifiche push](#update-server)
4. [Abilitare le notifiche push per test locali](#local-testing)
5. [Inserire dati per la ricezione di notifiche push](#test)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili] o [Introduzione ai dati] per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione. 

##<a id="register"></a> Registrare l'app con Servizi notifica Push Windows e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Il servizio mobile e l'app sono ora configurati per funzionare con Servizi notifica Push Windows e Hub di notifica. A questo punto, si aggiornerà l'app di Windows Store per la registrazione per le notifiche.

##<a id="update-app"></a> Aggiornare l'app per la registrazione per le notifiche

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1. Aprire il file default.js e inserire il codice seguente dopo il codice che crea l'istanza **MobileServiceClient**. Questo codice crea un canale di notifica push ed effettua la registrazione per le notifiche push:

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

2. In Visual Studio aprire il file Package.appxmanifest e verificare che nella scheda **Application UI** l'opzione **Toast capable** sia impostata su **Yes**.

   	![][1]

   	In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup. Tali notifiche sono già abilitate nel progetto di guida introduttiva scaricato.

##<a id="update-server"></a> Aggiornare il server per l'invio di notifiche push


[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Abilitare le notifiche push per test locali

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Testare le notifiche push nell'app

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app di Windows Store di usare Servizi mobili e Hub di notifica per l'invio di notifiche push. In seguito, è consigliabile eseguire una delle esercitazioni seguenti:

+ [Inviare notifiche push agli utenti autenticati]
	<br/>Informazioni su come usare i tag per inviare notifiche push da un servizio mobile a un solo utente autenticato.

Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili e Hub di notifica:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante servizi mobili.

* [Informazioni su Hub di notifica]
  <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Informazioni aggiuntive sulla risoluzione dei problemi e sul debug di soluzioni Hub di notifica. 

* [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]
  <br/>Altre informazioni su come usare Servizi mobili con app JavaScript

<!-- Anchors. -->

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users

[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/

[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library


<!--HONumber=42-->
