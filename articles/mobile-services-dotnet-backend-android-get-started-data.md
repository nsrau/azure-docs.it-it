<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Introduzione ai dati in Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows Store C#">Windows Store C#</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS">iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title=".NET backend" class="current">Back-end .NET</a> | 
    <a href="/it-it/develop/mobile/tutorials/get-started-with-data-android/"  title="JavaScript backend">Back-end JavaScript</a>
</div>

Questo argomento descrive come usare Servizi mobili di Azure come origine dati di back-end per un'app per Android. In questa esercitazione si creerà un nuovo servizio mobile, si scaricherà un progetto Eclipse Android per un'app che archivia dati in memoria, si integrerà il servizio mobile con l'app e quindi si visualizzeranno le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile creato in questa esercitazione supporta il runtime .NET. Sarà quindi possibile usare linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript][versione per back-end JavaScript] di questo argomento.

<div class="dev-callout"><b>Nota</b>
<p>Per completare questa esercitazione, &egrave; necessario disporre di Visual Studio 2013.</p>
</div>

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Creazione di un nuovo servizio mobile][Creazione di un nuovo servizio mobile]
2.  [Scaricare il servizio mobile in locale][Scaricare il servizio mobile in locale]
3.  [Test del servizio mobile][Test del servizio mobile]
4.  [Pubblicazione del servizio mobile in Azure][Pubblicazione del servizio mobile in Azure]
5.  [Scaricare il progetto GetStartedWithData][Scaricare il progetto GetStartedWithData]
6.  [Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati][Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati]
7.  [Eseguire il test dell'app sul servizio mobile pubblicato][Eseguire il test dell'app sul servizio mobile pubblicato]

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario un account Azure. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

## <a name="create-service"></a><span class="short-header">Creazione di un nuovo servizio mobile</span>Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service"></a><span class="short-header">Scaricare il servizio</span>Scaricare il servizio mobile nel computer locale

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">Eseguire il test del servizio</span>Eseguire il test del servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-the-service"></a><span class="short-header">Pubblicare il servizio</span>Pubblicare il servizio mobile in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="download-app"></a><span class="short-header">Download del progetto</span>Download del progetto GetStartedWithData

### Scaricare il codice di esempio

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

### Verificare la versione di Android SDK

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]

### Esaminare ed eseguire il codice di esempio

[WACOM.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

## <a name="update-app"></a><span class="short-header">Aggiornamento dell'app</span>Aggiornamento dell'app per l'utilizzo del servizio mobile per l'accesso ai dati

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

## <a name="test-app"></a><span class="short-header">Eseguire il test dell'app</span>Eseguire il test dell'app sul servizio mobile pubblicato

Ora che l'app è stata aggiornata per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili utilizzando l'emulatore di Android o un telefono Android.

1.  Nel menu **Run** scegliere **Run** per avviare il progetto.

    L'app, compilata tramite Android SDK che utilizza la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.

2.  Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

    È possibile riavviare l'app per verificare che le modifiche siano state salvate in modo permanente nel database in Azure. È anche possibile esaminare il database nel portale di gestione di Azure. I due passaggi successivi illustrano come eseguire questa operazione per visualizzare le modifiche nel database.

3.  Nel portale di gestione di Azure fare clic su Manage per il database associato al servizio mobile.

    ![][0]

4.  Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app di Windows Store. La query da utilizzare sarà simile alla seguente, ma anziché `todolist` conterrà il nome del database.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

L'esercitazione **Introduzione ai dati** per Android è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per Android di utilizzare dati in Servizi mobili.

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:  * [Validate and modify data with scripts]   <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.  * [Refine queries with paging]   <br/>Learn how to use paging in queries to control the amount of data handled in a single request.  Once you have completed the data series, try -->

Provare a eseguire una delle esercitazioni seguenti:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.


 


  [versione per back-end JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-data-android
  [Creazione di un nuovo servizio mobile]: #create-service
  [Scaricare il servizio mobile in locale]: #download-the-service-locally
  [Test del servizio mobile]: #test-the-service
  [Pubblicazione del servizio mobile in Azure]: #publish-mobile-service
  [Scaricare il progetto GetStartedWithData]: #download-app
  [Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati]: #update-app
  [Eseguire il test dell'app sul servizio mobile pubblicato]: #test-app
  [0]: ./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-android
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-android
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
