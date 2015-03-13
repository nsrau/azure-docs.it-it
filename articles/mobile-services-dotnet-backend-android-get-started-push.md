<properties 
	pageTitle="Introduzione alle notifiche push (Android) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili di Azure per inviare notifiche push all'app .Net per Android." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="ricksal"/>

# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per Android. L'esercitazione consente di aggiungere notifiche push al progetto di guida introduttiva tramite Google Cloud Messaging (GCM). Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record. 

Questa esercitazione descrive come:

1. [Abilitare Google Cloud Messaging](#register)
2. [Configurare il servizio mobile per l'invio di richieste push](#configure)
5. [Aggiornare il server per l'invio di notifiche push](#update-server)
7. [Aggiungere notifiche push all'app](#update-app)
8. [Abilitare le notifiche push per test locali](#local-testing)
9. [Testare l'app nel servizio mobile pubblicato]


Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili] o [Introduzione ai dati] per collegare il progetto al servizio mobile. Di conseguenza, per completare l'esercitazione è necessario anche Visual Studio 2013. 

>[AZURE.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">versione di valutazione gratuita di Azure</a>. 


##<a id="register"></a>Abilitare Google Cloud Messaging

[AZURE.INCLUDE [Abilitare GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure"></a>Configurare Servizi mobili per l'invio di richieste push

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Fare clic sulla scheda **Push** e immettere il valore **Chiave API** ottenuto da GCM nella procedura precedente, quindi fare clic su **Salva**.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

> [AZURE.IMPORTANT] Le credenziali GCM per le notifiche push avanzate configurate nella scheda Push del portale vengono condivise con Hub di notifica per configurare l'hub di notifica con l'app.


Il servizio mobile è ora configurato per funzionare con GCM e Hub di notifica.


<h2><a name="download-the-service"></a>Scaricare il servizio nel computer locale</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Testare il servizio mobile</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>Aggiornare il server per l'invio di notifiche push

1. In Esplora soluzioni di Visual Studio espandere la cartella **Controller** nel progetto del servizio mobile. Open TodoItemController.cs. Aggiungere le istruzioni `using` seguenti all'inizio del file:


		using System;
		using System.Collections.Generic;

2. Aggiornare la definizione del metodo `PostTodoItem` con il codice seguente:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Questo codice invierà una notifica push (con il testo dell'elemento inserito) dopo l'inserimento di un elemento Todo. In caso di errore, il codice aggiungerà una voce di log errori visualizzabile nella scheda **Log** del servizio mobile nel portale di gestione.


<h2><a name="publish-the-service"></a>Pubblicare il servizio mobile in Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="update-app"></a>Aggiungere notifiche push all'App

###Verificare la versione di Android SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


Il passaggio successivo comporta l'installazione di Google Play Services. Google Cloud Messaging impone alcuni requisiti minimi a livello di API per lo sviluppo e il testing. La proprietà **minSdkVersion** nel file manifesto deve essere conforme a tali requisiti. 

Se i test verranno eseguiti con un dispositivo meno recente, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK] per determinare il livello minimo su cui è possibile impostare tale valore.

###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Aggiungere Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Aggiungere codice

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

<h2><a name="test-app"></a>Testare l'app nel servizio mobile pubblicato</h2>

È possibile testare l'app collegando direttamente un telefono Android con un cavo USB oppure usando un dispositivo virtuale nell'emulatore.

###Uso dell'emulatore per il test

Assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

1. In **Finestra** selezionare **Android Virtual Device Manager**, quindi selezionare il dispositivo e fare clic su **Modifica** (oppure su **Nuovo** se non sono presenti dispositivi).

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Selezionare **Google APIs** (o **Google APIs x86**) in **Target**, quindi fare clic su OK.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	AVD sarà quindi configurato per l'uso di Google APIs. Se sono installate diverse versioni di Android SDK, assicurarsi che il livello dell'API corrisponda a quello impostato in precedenza nelle impostazioni del progetto.

###<a id="local-testing"></a> Abilitare le notifiche push per test locali

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

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
  
<!-- Anchors. -->

[Creare un nuovo servizio mobile]: #create-service
[Scaricare il servizio in locale]: #download-the-service-locally
[Testare il servizio mobile]: #test-the-service
[Scaricare il progetto GetStartedWithData]: #download-app
[Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati]: #update-app
[Testare l'app per Android nel servizio ospitato localmente]: #test-locally-hosted
[Pubblicare il servizio mobile in Azure]: #publish-mobile-service
[Testare l'app per Android nel servizio ospitato in Azure]: #test-azure-hosted
[Testare l'app nel servizio mobile pubblicato]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-ite
	ms.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Usare script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/get-started-with-data-js
[Versione per back-end JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-data-android
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Sito degli esempi di codice di Developer Network]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
[Classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

[Come usare la libreria client Android per Servizi mobili]: /it-it/documentation/articles/mobile-services-android-how-to-use-client-library

[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/

[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
[Inviare notifiche di trasmissione ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Inviare notifiche basate su modelli ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
[Portale di gestione di Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
