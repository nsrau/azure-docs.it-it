<properties
	pageTitle="Introduzione alle app per dispositivi mobili di Azure App Service per app Xamarin.iOS | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili per lo sviluppo per Xamarin iOS."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="normesta"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>


#Creare un'app per Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]& nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Panoramica

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Xamarin.iOS mediante un back-end per app per dispositivi mobili di Azure. Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Xamarin.iOS _Todo list_ che archivia i dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni Xamarin.iOS relative all'uso della funzionalità di Azure App Service relativa alle app per dispositivi mobili.

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] o versione successiva. Se si installa Visual Studio Community 2013, installare [Xamarin] separatamente. È possibile installare gli strumenti Xamarin quando si installa Visual Studio 2015.

* Un computer Mac in cui siano stati installati [Xcode] v7.0 o versione successiva e [Xamarin Studio].

     >[AZURE.NOTE]Se si prevede di compilare l'app in un computer basato su Windows usando Visual Studio, sarà comunque necessario accedere a un computer Mac in rete per eseguire questa operazione.

>[AZURE.NOTE]Per iniziare a usare Azure App Service prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile). In questa pagina è possibile creare immediatamente un'app per dispositivi mobili iniziale temporanea in App Service. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare un nuovo back-end dell'app per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Scaricare il progetto server

1. Sul PC visitare il [portale di Azure]. Fare clic su **Esplora tutto** > **App per dispositivi mobili** e quindi fare clic sul back-end dell'app per dispositivi mobili appena creato.

2. Nel pannello App per dispositivi mobili fare clic su **Impostazioni** e in **App per dispositivi mobili** fare clic su **Avvio rapido** > **Xamarin.iOS**.

3. In **Scarica ed esegui il progetto server** fare clic su **Scarica**. Estrarre i file compressi del progetto nel PC e aprire la soluzione in Visual Studio.

## Testare il progetto back-end in locale

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Pubblicare il progetto server in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Scaricare ed eseguire l'app per Xamarin.iOS

1. Sul Mac aprire il [portale di Azure] in una finestra del browser.

>[AZURE.NOTE]L'esecuzione dell'app per Xamarin.iOS risulta più facile in un Mac. È anche possibile eseguire l'app per Xamarin.iOS in Visual Studio nel computer basato su Windows, ma la procedura è più complessa perché è necessario connettersi a un Mac in rete. Per informazioni su questa procedura, vedere [Installazione di Xamarin.iOS in Windows].

2. In **Scarica ed esegui il progetto Xamarin.iOS** scegliere il pulsante **Scarica**.

  	Verrà scaricato un progetto che contiene un'applicazione client connessa all'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

3. Estrarre il progetto scaricato e aprirlo in Xamarin Studio (o in Visual Studio).

	![][9]

	![][8]

4. Premere F5 per compilare il progetto e avviare l'app nell'emulatore iPhone.

5. Nell'app digitare un testo significativo, ad esempio _Learn Xamarin_, e quindi fare clic sul pulsante **+**.

	![][10]

	Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end dell'app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

>[AZURE.NOTE]È possibile esaminare il codice che accede al back-end dell'app per dispositivi mobili per eseguire una query e inserire i dati nel file C# QSTodoService.cs.

##Passaggi successivi

* [Aggiungere l'autenticazione all'app](app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md) <br/>Informazioni sull'autenticazione degli utenti dell'app mediante un provider di identità.

* [Aggiungere notifiche push all'app](app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md) <br/>Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portale di Azure]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installazione di Xamarin.iOS in Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/

<!---HONumber=August15_HO9-->