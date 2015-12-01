<properties
	pageTitle="Introduzione alle app per dispositivi mobili di Azure App Service per app Xamarin.iOS | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili per lo sviluppo per Xamarin iOS."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/23/2015"
	ms.author="normesta"/>


#Creare un'app per Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Panoramica

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Xamarin.iOS mediante un back-end per app per dispositivi mobili di Azure. Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Xamarin.iOS _Todo list_ che archivia i dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni Xamarin.iOS relative all'uso della funzionalità di Azure App Service relativa alle app per dispositivi mobili.

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] o versione successiva. Se si installa Visual Studio Community 2013, installare [Xamarin] separatamente. È possibile installare gli strumenti Xamarin quando si installa Visual Studio 2015.

* Un computer Mac in cui siano stati installati [Xcode] v7.0 o versione successiva e [Xamarin Studio].
 
* Se si prevede di compilare l'app in un computer basato su Windows con Visual Studio, è comunque necessario accedere a un computer Mac in rete che esegue l'host di compilazione Xamarin.iOS per compilare e distribuire effettivamente l'app. Per altre informazioni, vedere la pagina relativa all'[installazione di Xamarin.iOS in Windows](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/)

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile). In questa pagina è possibile creare immediatamente un'app per dispositivi mobili iniziale temporanea in App Service. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare un nuovo back-end dell'app per dispositivi mobili di Azure

Per creare un nuovo back-end dell'app per dispositivi mobili, attenersi alla procedura seguente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configurare il progetto server

È stato eseguito il provisioning di un back-end dell'app per dispositivi mobili di Azure che può essere usato dalle applicazioni client per dispositivi mobili. Successivamente, scaricare un progetto server per un semplice back-end "todo list" e pubblicarlo in Azure.

Seguire questa procedura per configurare il progetto server per l'uso del back-end .NET o Node.js.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## (Facoltativo) Testare il progetto back-end in locale

Se è stata scelta una configurazione back-end .NET, facoltativamente è possibile testare il back-end in locale.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]



## Scaricare ed eseguire l'app per Xamarin.iOS

1. Nel computer Mac aprire il [portale di Azure] in una finestra del browser.

	>[AZURE.NOTE]L'esecuzione dell'app per Xamarin.iOS risulta più facile in un Mac. È anche possibile eseguire l'app per Xamarin.iOS in Visual Studio nel computer basato su Windows, ma la procedura è più complessa perché è necessario connettersi a un Mac in rete. Per informazioni su questa procedura, vedere la pagina relativa all'[installazione di Xamarin.iOS in Windows].

2. Nel pannello delle impostazioni dell'app per dispositivi mobili, fare clic su **Introduzione** > **Xamarin.iOS**. Al passaggio 3 fare clic su **Crea una nuova app**, se l'opzione non è già selezionata. Fare quindi clic sul pulsante **Download**.

  	Verrà scaricato un progetto che contiene un'applicazione client connessa all'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

3. Estrarre il progetto scaricato e aprirlo in Xamarin Studio (o in Visual Studio).

	![][9]

	![][8]

4. Premere F5 per compilare il progetto e avviare l'app nell'emulatore iPhone.

5. Nell'app digitare un testo significativo, ad esempio _Learn Xamarin_ e quindi fare clic sul pulsante **+**.

	![][10]

	Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end dell'app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

>[AZURE.NOTE]È possibile esaminare il codice che accede al back-end dell'app per dispositivi mobili per eseguire una query e inserire i dati nel file C# QSTodoService.cs.

##Passaggi successivi

* [Aggiungere l'autenticazione all'app](app-service-mobile-xamarin-ios-get-started-users.md) <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Aggiungere notifiche push all'app](app-service-mobile-xamarin-ios-get-started-push.md) <br/>Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[portale di Azure]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[installazione di Xamarin.iOS in Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/

<!---HONumber=AcomDC_1125_2015-->