<properties pageTitle="Introduzione alle notifiche push (Android JavaScript) | Mobile Dev Center" description="Informazioni su come usare Servizi mobili di Azure per inviare notifiche push all'app JavaScript per Android." services="mobile-services, notification-hubs" documentationCenter="android" authors="RickSaling" writer="ricksal" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal"/>

# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per Android tramite Google Cloud Messaging (GCM). In questa esercitazione si userà Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Abilitare Google Cloud Messaging](#register)
2. [Configurare Servizi mobili](#configure)
3. [Aggiungere notifiche push all'app](#add-push)
4. [Aggiornare gli script per l'invio di notifiche push](#update-scripts)
5. [Inserire dati per la ricezione di notifiche](#test)


>[AZURE.NOTE] Per visualizzare il codice sorgente dell'app completata, fare clic <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">qui</a>.

##Prerequisiti

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Abilitare Google Cloud Messaging

>[AZURE.NOTE]Per completare questa procedura, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, passare alla pagina all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Questo valore della chiave API verrà quindi usato per abilitare Servizi mobili per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

##<a id="configure"></a>Configurare Servizi mobili per l'invio di richieste push

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Fare clic sulla scheda **Push**, immettere il valore di **Chiave API** ottenuto da GCM nella procedura precedente e quindi fare clic su **Salva**.

	>[AZURE.NOTE]Se si completa questa esercitazione con un servizio mobile meno recente, nella parte inferiore della scheda **Push** potrebbe essere visualizzato un collegamento con l'indicazione **Abilita push avanzato**. Fare clic sul collegamento per aggiornare il servizio mobile per l'integrazione con Hub di notifica. Questa modifica non può essere annullata. Per informazioni dettagliate su come abilitare le notifiche push avanzate in un servizio mobile di produzione, vedere <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">queste linee guida</a>.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

	> [AZURE.IMPORTANT] Le credenziali GCM per le notifiche push avanzate configurate nella scheda Push del portale vengono condivise con Hub di notifica per configurare l'hub di notifica con l'app.


Il servizio mobile e l'app sono ora configurati per funzionare con GCM e Hub di notifica.

##<a id="add-push"></a>Aggiungere notifiche push all'app

###Verificare la versione di Android SDK

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

Il passaggio successivo comporta l'installazione di Google Play Services. Google Cloud Messaging impone alcuni requisiti minimi a livello di API per lo sviluppo e il testing. La proprietà **minSdkVersion** nel file manifesto deve essere conforme a tali requisiti. 

Se i test verranno eseguiti con un dispositivo meno recente, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK] per determinare il livello minimo su cui è possibile impostare tale valore.

###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Aggiungere codice

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>Aggiornare gli script insert registrati nel portale di gestione

1. Nel portale di gestione fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**. 

   	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2. In **todoitem** fare clic sulla scheda **Script** e selezionare **Inserisci**.
   
  	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

   	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Salva**:

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = {
		    data: {
		        message: item.text 
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

È possibile testare l'app collegando direttamente un telefono Android con un cavo USB oppure usando un dispositivo virtuale nell'emulatore.

###Configurazione dell'emulatore per il test

Quando si esegue l'app nell'emulatore, assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

1. Riavviare Eclipse, quindi in Package Explorer fare clic con il pulsante destro del mouse sul progetto, scegliere **Properties**, fare clic su **Android**, selezionare **Google APIs** e fare clic su **OK**.

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	Il progetto farà quindi riferimento a Google APIs.

2. Da **Window** selezionare **Android Virtual Device Manager**, selezionare il dispositivo e fare clic su **Edit**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. Selezionare **Google APIs** in **Target**, quindi fare clic su OK.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	AVD sarà quindi configurato per l'uso di Google APIs.

###Esecuzione del test

1. Dal menu **Run** di Eclipse scegliere **Run** per avviare l'app.

2. Digitare testo significativo nell'app, ad esempio _Nuova attività Servizi mobili_ e quindi fare clic sul pulsante **Add**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Scorrere verso il basso dalla parte superiore della schermata per aprire Notification Center per il dispositivo e visualizzare la notifica.


L'esercitazione è stata completata.


## <a name="next-steps"> </a>Passaggi successivi

<!---In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per Android di usare Servizi mobili e Hub di notifica per inviare notifiche push. In seguito, provare a eseguire l'esercitazione [Inviare notifiche push agli utenti autenticati], che illustra come usare i tag per inviare notifiche push da un servizio mobile a un solo utente autenticato.

+ [Inviare notifiche push agli utenti autenticati]
	<br/>Informazioni su come usare i tag per inviare notifiche push da un servizio mobile a un solo utente autenticato.

+ [Inviare notifiche di trasmissione ai sottoscrittori]
	<br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

+ [Inviare notifiche basate su modelli ai sottoscrittori]
	<br/>Informazioni su come usare i modelli per inviare notifiche push da un servizio mobile senza che sia necessario creare payload specifici della piattaforma nel back-end.
-->

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante servizi mobili.

* [Informazioni su Hub di notifica]
  <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Informazioni aggiuntive sulla risoluzione dei problemi e sul debug di soluzioni Hub di notifica. 

* [Come usare la libreria client Android per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con Android.

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni su come implementare la logica di business nel servizio mobile.


<!-- Anchors. -->
[Registrare l'app per le notifiche push e configurare Servizi mobili]: #register
[Aggiornare il codice delle notifiche push generato]: #update-scripts
[Inserire dati per la ricezione di notifiche]: #test
[Passaggi successivi]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-android-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-android-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-android-get-started-users
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-js
[Inviare notifiche push agli utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-js
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/get-started-with-push-js
[Configurare Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Come usare la libreria client Android per Servizi mobili]: /it-it/documentation/articles/mobile-services-android-how-to-use-client-library

[Oggetto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293

[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/

[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
[Inviare notifiche di trasmissione ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-android-send-breaking-news/
[Inviare notifiche basate su modelli ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-android-send-localized-breaking-news/


<!--HONumber=42-->
