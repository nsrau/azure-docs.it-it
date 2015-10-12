<properties
	pageTitle="Introduzione ai dati su Android (back-end JavaScript) | Microsoft Azure"
	description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per Android.(back-end JavaScript)"
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
	ms.date="09/23/2015"
	ms.author="ricksal"/>

# Aggiungere Servizi mobili a un'app esistente (back-end JavaScript)

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

## Riepilogo

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Questo argomento illustra come usare Servizi mobili di Azure per aggiungere dati persistenti a un'app per Android. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e si integrerà l'app con il servizio mobile, in modo che archivi e aggiorni i dati in Servizi mobili di Azure invece che localmente, quindi si userà il portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.</p>

</div>


<div class="dev-onpage-video-wrapper">
<a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a><span class="time">15:32:00</span></div>
</div>


<p>Questa esercitazione illustra più dettagliatamente in che modo Servizi mobili di Azure può archiviare e recuperare dati da un'app per Android. Vengono pertanto riproposte molte delle procedure già completate nell'esercitazione basata sulla guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione <a href="/it-IT/develop/mobile/tutorials/get-started-android">Introduzione a Servizi mobili</a>.</p>

## Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

- Account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">versione di valutazione gratuita di Azure</a>.


- [Azure Mobile Services Android SDK].
- <a  href="https://developer.android.com/sdk/index.html" target="_blank">Ambiente di sviluppo integrato di Android Studio</a>, che include Android SDK, e Android 4.2 o versione successiva. Il progetto GetStartedWithData scaricato richiede Android 4.2 o versione successiva invece con Mobile Services SDK è richiesto solo Android 2.2 o versione successiva.

## Codice di esempio

Per visualizzare il codice sorgente completato, passare <a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio">qui</a>.

## Scaricare il progetto GetStartedWithData

###Scaricare il codice di esempio

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### Esaminare ed eseguire il codice di esempio

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Creare un nuovo servizio mobile nel portale di gestione

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## Aggiungere una nuova tabella al servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## Testare l'app nel nuovo servizio mobile

Ora che l'app è stata aggiornata per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili usando l'emulatore di Android o un telefono Android.

1. Nel menu **Run** scegliere **Run app** per avviare il progetto.

	L'app, compilata tramite Android SDK, che usa la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.

5. Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]

   	Si noti che la tabella **TodoItem** ora contiene dati, con alcuni valori generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Android è terminata.

## Risoluzione dei problemi

### Verificare la versione di Android SDK

[AZURE.INCLUDE [Verificare l'SDK](../../includes/mobile-services-verify-android-sdk-version.md)]



## Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per Android di usare dati in Servizi mobili.

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Utilizzo di script per la convalida e la modifica di dati] <br/>Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Utilizzo del paging per ridefinire le query] <br/>Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per Android seguenti:

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
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Utilizzo di script per la convalida e la modifica di dati]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Utilizzo del paging per ridefinire le query]: /develop/mobile/tutorials/add-paging-to-data-android
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-android
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-android
[Introduzione all'autenticazione]: /develop/mobile/tutorials/get-started-with-users-android
[Introduzione alle notifiche push]: /develop/mobile/tutorials/get-started-with-push-android

[Azure Management Portal]: https://manage.windowsazure.com/
[portale di gestione]: https://manage.windowsazure.com/
[Azure Mobile Services Android SDK]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=Oct15_HO1-->