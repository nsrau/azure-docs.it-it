<properties 
	pageTitle="Introduzione alle notifiche push (Android JavaScript) | Microsoft Azure" 
	description="Informazioni su come usare Servizi mobili di Azure per inviare notifiche push all'app JavaScript per Android." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="ricksal"/>

# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push-ec.md)]

Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per Android tramite Google Cloud Messaging (GCM). In questa esercitazione si userà Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

Questa esercitazione descrive le operazioni di base per abilitare le notifiche push:

1. [Abilitare Google Cloud Messaging](#register)
2. [Configurare Servizi mobili](#configure)
3. [Aggiungere notifiche push all'app](#add-push)
4. [Aggiornare gli script per l'invio di notifiche push](#update-scripts)
5. [Inserire dati per la ricezione di notifiche](#test)


>[AZURE.NOTE]Per visualizzare il codice sorgente dell'app completata, fare clic <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">qui</a>.

##Prerequisiti

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites-ec.md)]

##<a id="register"></a>Abilitare Google Cloud Messaging

[AZURE.INCLUDE [Abilitare GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>Configurare Servizi mobili per l'invio di richieste push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="add-push"></a>Aggiungere notifiche push all'app

###Verificare la versione di Android SDK

[AZURE.INCLUDE [Verificare l'SDK](../../includes/mobile-services-verify-android-sdk-version-EC.md)]

Il passaggio successivo comporta l'installazione di Google Play Services. Google Cloud Messaging prevede alcuni requisiti minimi a livello di API per lo sviluppo e il testing. È necessario che la proprietà **minSdkVersion** nel file manifesto sia conforme a tali requisiti.

Se il test verrà eseguito con un dispositivo meno recente, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK] per determinare il livello minimo su cui è possibile impostare tale valore.

###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Aggiungere Play Services](../../includes/mobile-services-add-google-play-services-EC.md)]

###Aggiungere codice

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push-ec.md)]


##<a id="update-scripts"></a>Aggiornare lo script insert registrato nel portale di gestione

1. Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**. 

   	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-portal-data-tables.png)

2. In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.
   
  	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-insert-script-push2.png)

   	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = {
		    "data": {
		        "message": item.text 
		    }
		};		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	Verrà registrato un nuovo script insert, che usa l'[oggetto gcm] per inviare una notifica push a tutti i dispositivi registrati dopo la corretta esecuzione dell'inserimento.

##<a id="test"></a>Testare le notifiche push nell'app

È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure usando un dispositivo virtuale nell'emulatore.

###Configurazione dell'emulatore per il test

Quando si esegue l'app nell'emulatore, assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

1. Riavviare Eclipse, quindi in Package Explorer fare clic con il pulsante destro del mouse sul progetto, scegliere **Properties**, fare clic su **Android**, selezionare **Google APIs** e infine fare clic su **OK**.

	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-services-import-android-properties.png)

  	Il progetto farà quindi riferimento a Google APIs.

2. Da **Window** selezionare **Android Virtual Device Manager**, quindi selezionare il dispositivo e fare clic su **Edit**.

	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager.png)

3. Selezionare **Google APIs** in **Target**, quindi fare clic su OK.

   	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager-edit.png)

	AVD sarà quindi configurato per l'utilizzo di Google APIs.

###Esecuzione del test

1. Dal menu **Run** di Eclipse, fare clic su **Run** per avviare l'app.

2. Digitare testo significativo nell'app, ad esempio _A new Mobile Services task_, quindi fare clic sul pulsante **Add**.

  	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-quickstart-push1-android.png)

3. Scorrere verso il basso dalla parte superiore della schermata per aprire il Notification Center del dispositivo e visualizzare la notifica.


L'esercitazione è stata completata.


## <a name="next-steps"> </a>Passaggi successivi

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione ai dati] <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite servizi mobili.

* [Aggiungere l'autenticazione all'app][Get started with authentication] <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

* [Informazioni su Hub di notifica] <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Informazioni aggiuntive sulla risoluzione dei problemi e sul debug di soluzioni di Hub di notifica.

* [Come usare la libreria client di Android per Servizi mobili] <br/>Altre informazioni su come usare Servizi mobili con Android.

* [Informazioni di riferimento sugli script del server di Servizi mobili] <br/>Altre informazioni su come implementare la logica di business nel servizio mobile.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: mobile-services-android-get-started.md
[Introduzione ai dati]: mobile-services-android-get-started-data.md
[Get started with authentication]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[configurazione di Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure Management Portal]: https://manage.windowsazure.com/
[Come usare la libreria client di Android per Servizi mobili]: mobile-services-android-how-to-use-client-library.md

[oggetto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-android-push-notifications-app-users.md

[Informazioni su Hub di notifica]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md
 

<!---HONumber=August15_HO8-->