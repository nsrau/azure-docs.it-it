<properties
	pageTitle="Introduzione alle app per dispositivi mobili di Azure per Xamarin.Android"
	description="Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili di Azure per lo sviluppo per Xamarin Android."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>

#Creare un'app per Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]& nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]
 
##Panoramica

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per Xamarin.Android con un back-end dell'app per dispositivi mobili di Azure. Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Xamarin.Andorid _Todo list_ che archivia dati delle app in Azure.

Di seguito è riportata una schermata dell'app completata:

![][0]

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative alle app per dispositivi mobili per Xamarin.Android.
 
##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] o versione successiva. Se si installa Visual Studio Community 2013, installare [Xamarin] separatamente. È possibile installare gli strumenti Xamarin quando si installa Visual Studio 2015.
 
>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), dove è possibile creare un'app per dispositivi mobili di base temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


## Creare un nuovo back-end dell'app per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Scaricare il progetto server

1. Nel PC visitare il [portale di Azure]. Fare clic su **Esplora tutto** > **App per dispositivi mobili**, quindi fare clic sul back-end dell'app per dispositivi mobili appena creato.

2. Nel pannello App per dispositivi mobili fare clic su **Impostazioni** e in **App per dispositivi mobili** fare clic su **Avvio rapido** > **Xamarin.Android**.
 
3. In **Scarica ed esegui il progetto server** fare clic su **Scarica**. Estrarre i file compressi del progetto nel PC e aprire la soluzione in Visual Studio.
 
## Testare il progetto back-end in locale

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Pubblicare il progetto server in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Scaricare ed eseguire l'app per Xamarin.Android

1. In **Scarica ed esegui il progetto Xamarin.Android** scegliere il pulsante **Scarica**.

  	Verrà scaricato un progetto che contiene un'applicazione client connessa all'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

	![][8]

	![][9]

2. Premere **F5** per compilare il progetto e avviare l'app.

3. Nell'app digitare un testo significativo, ad esempio _Completare l'esercitazione_, quindi fare clic sul pulsante **Aggiungi**.

	![][10]

	Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end per app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

	> [AZURE.NOTE]È possibile esaminare il codice che accede al back-end per app mobili per eseguire una query e inserire i dati trovati nel file C# ToDoActivity.cs.

##Passaggi successivi

* [Aggiungere l'autenticazione all'app](app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md) <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.


<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[portale di Azure]: https://azure.portal.com/
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=September15_HO1-->