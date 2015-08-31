<properties 
	pageTitle="Introduzione ai dati (Android) | Microsoft Azure" 
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
	ms.date="08/08/2015" 
	ms.author="ricksal"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data-ec.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Questo argomento illustra come usare Servizi mobili di Azure per sfruttare i dati in un'app per Android. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a><span class="time">15:32:00</span></div>
</div>

> [AZURE.NOTE]Questa esercitazione è stata ideata per illustrare come usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per Android e ripropone quindi molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili](../get-started-android-ec.md).
> 
> Per visualizzare il codice sorgente dell'app completata, fare clic [qui](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData).

Per completare l'esercitazione, sono necessari gli elementi seguenti:

+ Un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-android-get-started-data-EC%2F). 

+ [Mobile Services SDK per Android], <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android SDK</a> che include l'IDE (Integrated Development Environment) di Eclipse, il plug-in Android Developer Tools (ADT) e Android 4.2 o versione successiva.

> [AZURE.NOTE]Questa esercitazione fornisce istruzioni per installare Android SDK e Mobile Services SDK per Android. Il progetto GetStartedWithData scaricato richiede Android 4.2 o versione successiva invece con Mobile Services SDK è richiesto solo Android 2.2 o versione successiva.

<!-- -->

> [AZURE.NOTE]Questa esercitazione usa la versione più recente di Mobile Services SDK. È possibile trovare una versione precedente <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126">qui</a> per motivi di compatibilità con le versioni precedenti, ma il codice contenuto in queste esercitazioni non funzionerà.

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

###Scaricare il codice di esempio

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code-ec.md)]

###Verificare la versione di Android SDK

[AZURE.INCLUDE [Verificare l'SDK](../../includes/mobile-services-verify-android-sdk-version-ec.md)]


###Esaminare ed eseguire il codice di esempio

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code-ec.md)]

##<a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data-ec.md)]

##<a name="test-app"></a>Testare l'app nel nuovo servizio mobile

Ora che l'app è stata aggiornata per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili usando l'emulatore di Android o un telefono Android.

1. Nel menu **Run** scegliere **Run** per avviare il progetto.

	L'app, compilata tramite Android SDK, che usa la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.

5. Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con alcuni valori generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Android è terminata.

## <a name="next-steps"> </a>Passaggi successivi

Questa esercitazione ha illustrato le nozioni di base per consentire a un'app per Android di usare dati in Servizi mobili.

Successivamente, provare a eseguire le esercitazioni per Android seguenti:

* [Introduzione all'autenticazione] <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data-EC/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Introduzione all'autenticazione]: mobile-services-android-get-started-users.md
[Introduzione alle notifiche push]: mobile-services-javascript-backend-android-get-started-push-ec.md

[Azure Management Portal]: https://manage.windowsazure.com/
[portale di gestione]: https://manage.windowsazure.com/
[Mobile Services SDK per Android]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
 

<!---HONumber=August15_HO8-->