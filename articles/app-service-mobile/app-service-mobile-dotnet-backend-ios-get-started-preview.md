<properties
	pageTitle="Introduzione al servizio app iOS per dispositivi mobili del servizio app di Azure"
	description="Seguire questa esercitazione per iniziare a usare il servizio app di Azure per lo sviluppo per iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="06/18/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>Creare un'app per iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

Questa esercitazione illustra come creare un'app per iOS con le app per dispositivi mobili del servizio app. Verrà creato un nuovo back-end dell'app per dispositivi mobili e una semplice app _To do list_ che archivia i dati dell'app. L'esercitazione usa .NET e Visual Studio per la logica lato server.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), dove è possibile creare un'app per dispositivi mobili di base temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## <a name="create-new-service"> </a>Creare un nuovo back-end dell'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Creare una nuova app per iOS

Dopo aver creato il back-end mobile, è possibile seguire una facile guida introduttiva nel portale di anteprima di Azure per creare una nuova app o modificare un'app esistente per connettersi al back-end dell'app per dispositivi mobili.

1. Nel portale di Azure fare clic su **App per dispositivi mobili** e selezionare il back-end dell'app per dispositivi mobili appena creato.

2. Nella parte superiore del pannello fare clic su Aggiungi client ed espandere iOS.

	![][6]

	Verranno visualizzati i passaggi per creare un'app per iOS connessa al back-end dell'app per dispositivi mobili.

3. Se necessario, scaricare e installare <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> nel computer locale o nella macchina virtuale.

4. Scaricare e installare [Xcode] v4.4 o versione successiva e [Xamarin Studio]. È anche possibile usare Xamarin per Visual Studio.

5. In **Scaricare e pubblicare il servizio nel cloud** fare clic su **Scarica**.

 Verrà scaricata una soluzione che contiene progetti sia per il back-end dell'app per dispositivi mobili, sia per l'applicazione _To do list_ di esempio connessa al back-end dell'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

6. Scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

## Testare l'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Pubblicare l'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Eseguire l'app per iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
 

<!---HONumber=August15_HO6-->