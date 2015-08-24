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
	ms.date="07/29/2015" 
	ms.author="ricksal"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

Questo argomento descrive come usare Servizi mobili di Azure come origine dati di back-end per un'app per Android. In questa esercitazione si creerà un nuovo servizio mobile, si scaricherà un progetto Eclipse Android per un'app che archivia dati in memoria, si integrerà il servizio mobile con l'app e quindi si visualizzeranno le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile creato in questa esercitazione supporta il runtime .NET. Sarà quindi possibile usare linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript](mobile-services-android-get-started-data.md) di questo argomento.

> [AZURE.NOTE]Per visualizzare la versione per Eclipse di questa esercitazione, vedere [Introduzione ai dati (Eclipse)].

Per completare l'esercitazione, sono necessari gli elementi seguenti:

+ <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> Update 3 o versione successiva. 

+ Un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-android-get-started-data%2F).

##<a name="create-service"></a>Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service"></a>Scaricare il servizio nel computer locale

[AZURE.INCLUDE [mobile-services-download-service-locally](../../includes/mobile-services-download-service-locally.md)]

##<a name="test-the-service"></a>Testare il servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-the-service"></a>Pubblicare il servizio mobile in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

###Scaricare il codice di esempio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/download-android-sample-code.md)]

###Verificare la versione di Android SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version.md)]


###Esaminare ed eseguire il codice di esempio

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

##<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]

##<a name="test-app"></a>Testare l'app sul servizio mobile pubblicato

Ora che l'app è stata aggiornata per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili usando l'emulatore di Android o un telefono Android.

1. Nel menu **Run** scegliere **Run app** per avviare il progetto.

	L'app, compilata tramite Android SDK, che usa la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.

5. Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

    È possibile riavviare l'app per verificare che le modifiche siano state salvate in modo permanente nel database in Azure. È anche possibile esaminare il database nel portale di gestione di Azure. I due passaggi successivi illustrano come eseguire questa operazione per visualizzare le modifiche nel database.

4. Nel portale di gestione di Azure fare clic su Gestisci per il database associato al servizio mobile.

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png)

5. Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app di Windows Store. La query da usare sarà simile alla seguente, ma anziché `todolist` conterrà il nome del database.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png)

L'esercitazione **Introduzione ai dati** per Android è terminata.


## <a name="next-steps"> </a>Passaggi successivi

Questa esercitazione ha illustrato le nozioni di base per consentire a un'app per Android di usare dati in Servizi mobili.

Successivamente, provare a eseguire una delle esercitazioni seguenti:

* [Introduzione all'autenticazione] <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di Servizi mobili con Android](mobile-services-android-how-to-use-client-library.md) <br/>Informazioni su come usare Servizi mobili con Android.
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Introduzione ai dati (Eclipse)]: mobile-services-dotnet-backend-android-get-started-data-EC.md
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Introduzione all'autenticazione]: mobile-services-dotnet-backend-android-get-started-users.md
[Introduzione alle notifiche push]: mobile-services-dotnet-backend-android-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
   

<!---HONumber=August15_HO7-->