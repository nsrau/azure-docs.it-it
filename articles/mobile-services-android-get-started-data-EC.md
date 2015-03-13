<properties 
	pageTitle="Introduzione ai dati (Android) | Mobile Developer Center" 
	description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per Android." 
	services="mobile-services" 
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
	ms.date="02/06/2015" 
	ms.author="ricksal,glenga"/>

# Aggiunta di Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Questo argomento descrive come usare Servizi mobili di Azure per sfruttare i dati in un'app per Android. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a><span class="time">15:32</span></div>
</div>

> [AZURE.NOTE] In questa esercitazione viene descritto come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per Android e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se questa è la prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili](/it-it/develop/mobile/tutorials/get-started-android-EC).
> 
> Per visualizzare il codice sorgente dell'app completata, fare clic [qui](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData).


Questa esercitazione spiega come eseguire le seguenti operazioni di base:

1. [Scaricare il progetto dell'app per Android] 
2. [Creare il servizio mobile]
3. [Aggiungere una tabella dati per l'archiviazione]
4. [Aggiornare l'app per l'uso di Servizi mobili]
5. [Testare l'app in Servizi mobili]

> [AZURE.IMPORTANT] Per completare l'esercitazione è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357"%20target="_blank). 

Per completare questa esercitazione, è necessario disporre di [Mobile Services Android SDK], di <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android SDK</a>, che include l'IDE (Integrated Development Environment) di Eclipse e il plug-in Android Developer Tools (ADT), e di Android 4.2 o versione successiva. 

> [AZURE.NOTE] In questa esercitazione vengono fornite le istruzioni per installare Android SDK e Mobile Services Android SDK. Il progetto GetStartedWithData scaricato richiede Android 4.2 o versione successiva invece con Mobile Services SDK è richiesto solo Android 2.2 o versione successiva.

<!-- -->

> [AZURE.NOTE] In questa esercitazione viene usata la versione più recente di Mobile Services SDK. È possibile trovare una versione precedente <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> qui</a> per motivi di compatibilità, ma il codice contenuto in queste esercitazioni non funziona con le versioni precedenti dell'SDK.

<h2><a name="download-app"></a>Scaricare il progetto GetStartedWithData</h2>

### Scaricare il codice di esempio

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code-EC.md)]

### Verificare la versione di Android SDK

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version-EC.md)]


### Esaminare ed eseguire il codice di esempio

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code-EC.md)]

<h2><a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data-EC.md)]


<h2><a name="test-app"></a>Testare l'app sul nuovo servizio mobile</h2>

Ora che l'app è stata aggiornata per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili usando l'emulatore di Android o un telefono Android.

1. Dal menu **Run** scegliere **Run** per avviare il progetto.

	L'app, compilata tramite Android SDK che usa la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.

5. Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con alcuni valori generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Android è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per Android di usare dati in Servizi mobili. 

In seguito, è consigliabile eseguire una delle seguenti esercitazioni, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Usare script per la convalida e la modifica di dati]
  <br/>Informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Informazioni sull'uso del paging nelle query per controllare la quantità di dati gestiti in una singola richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire queste esercitazioni per Android:

* [Introduzione all'autenticazione] 
	<br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

<!-- Anchors. -->
[Scaricare il progetto dell'app per Android]: #download-app
[Creare il servizio mobile]: #create-service
[Aggiungere una tabella dati per l'archiviazione]: #add-table
[Aggiornare l'app per l'uso di Servizi mobili]: #update-app
[Testare l'app in Servizi mobili]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Usare script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-android
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-android
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-android
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-android
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-android

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Mobile Services Android SDK]: http://aka.ms/Iajk6q
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!--HONumber=45--> 
