<properties
	pageTitle="Introduzione alle app per dispositivi mobili in Xamarin iOS"
	description="Informazioni sull'uso di Xamarin iOS per creare un'app per dispositivi mobili di Azure con il servizio app di Azure."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Creare un'app Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato sul cloud a un'app Xamarin iOS con il servizio per app per dispositivi mobili di Azure. In questa esercitazione verranno creati un nuovo servizio .NET e una semplice app _To do list_ che archivia i dati dell'app nel back-end .NET.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), dove è possibile creare un'app per dispositivi mobili di base temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare un nuovo back-end dell'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Creare una nuova app per Xamarin iOS

Dopo aver creato il back-end dell'app per dispositivi mobili, usare l'avvio rapido disponibile nel portale di Azure per creare una nuova app o modificare un'app esistente per connettersi al back-end dell'app per dispositivi mobili.

In questa sezione verrà scaricata una nuova app Xamarin iOS e un progetto di servizio per il back-end dell'app per dispositivi mobili.

1. Nel portale di Azure fare clic su **App per dispositivi mobili** e selezionare il back-end dell'app per dispositivi mobili appena creato.

2. Nella parte superiore del pannello fare clic su **Aggiungi client** ed espandere **Xamarin iOS**.

	![][6]

	Vengono visualizzati i tre semplici passaggi per creare un'app Xamarin iOS connessa al back-end dell'app per dispositivi mobili.

3. Se necessario, scaricare e installare <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> nel computer locale o nella macchina virtuale.

4. Scaricare e installare [Xcode] v4.4 o versione successiva e [Xamarin Studio]. È anche possibile usare Xamarin per Visual Studio.

5. In **Scaricare e pubblicare il servizio nel cloud** fare clic su **Scarica**.

 Verrà scaricata una soluzione che contiene progetti sia per il back-end dell'app per dispositivi mobili, sia per l'applicazione _To do list_ di esempio connessa al back-end dell'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

6. Scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

## Testare il back-end dell'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Pubblicare il back-end dell'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Eseguire l'app per Xamarin iOS

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Passare al progetto client nella soluzione di back-end dell'app per dispositivi mobili in Visual Studio o in Xamarin Studio.

	![][8]

	![][9]

2. Fare clic su **Esegui** per compilare il progetto e avviare l'app nell'emulatore iPhone.

3. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_, quindi fare clic sull'icona con il segno PIÙ (**+**).

	![][10]

	Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end dell'app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

>[AZURE.NOTE]È possibile esaminare il codice che accede al back-end dell'app per dispositivi mobili per eseguire una query e inserire i dati nel file C# QSTodoService.cs.


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
[Azure Portal]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=August15_HO6-->