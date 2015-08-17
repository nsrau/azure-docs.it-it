<properties
	pageTitle="Introduzione alle app per dispositivi mobili di Azure per app Xamarin Android - App per dispositivi mobili di Azure"
	description="Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili di Azure per lo sviluppo per Xamarin Android."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="chrisanderson"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/01/2015"
	ms.author="chrande"/>

# <a name="getting-started"> </a>Creare un'app per Xamarin Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato sul cloud a un'app per Xamarin Android con il servizio App per dispositivi mobili di Azure. In questa esercitazione verranno creati un nuovo servizio .NET e una semplice app _To do list_ che archivia i dati dell'app nel back-end .NET.

Di seguito è riportata una schermata dell'app completata:

![][0]

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative alle app per dispositivi mobili di Azure per Xamarin Android.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), dove è possibile creare un'app per dispositivi mobili di base temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare un nuovo back-end dell'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Creare una nuova app per Xamarin Android

Dopo aver creato il back-end dell'app per dispositivi mobili, usare l'avvio rapido disponibile nel [portale di Azure] per creare una nuova app o modificare un'app esistente per connettersi al back-end dell'app per dispositivi mobili.

In questa esercitazione verrà scaricata una nuova app per Xamarin Android e un progetto di servizio back-end .NET per l'app per dispositivi mobili.

1. Nel portale di Azure fare clic su **Sfoglia**, quindi su **App per dispositivi mobili** e selezionare l'app per dispositivi mobili appena creata.

2. Nella parte superiore del pannello fare clic su **Aggiungi client** ed espandere **Xamarin.Android**.

    ![][6]

    Vengono visualizzati i tre semplici passaggi per creare un'app per Xamarin Android connessa al back-end dell'app per dispositivi mobili.


3. Se necessario, scaricare e installare <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> nel computer locale o nella macchina virtuale.

4. Se necessario, scaricare e installare [Xamarin Studio]. È anche possibile usare Xamarin per Visual Studio.

5. In **Scaricare e pubblicare il servizio nel cloud** fare clic su **Scarica**.

  	Verrà scaricata una soluzione che contiene progetti sia per il codice del back-end dell'app per dispositivi mobili, sia per l'applicazione client _To do list_ di esempio connessa al back-end dell'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

6. Scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

## Testare il back-end dell'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Pubblicare il back-end dell'app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Eseguire l'app per Xamarin Android

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Passare al progetto client nella soluzione dell'app per dispositivi mobili in Visual Studio o in Xamarin Studio.

	![][8]

	![][9]

2. Premere il pulsante **Esegui** per compilare il progetto e avviare l'app. Verrà chiesto di selezionare un emulatore o un dispositivo USB collegato.

	> [AZURE.NOTE]Per poter eseguire il progetto nell'emulatore di Android, è necessario definire almeno un dispositivo Android Virtual Device (AVD). Usare AVD Manager per creare e gestire questi dispositivi.

3. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_, quindi fare clic sull'icona con il segno PIÙ (**+**).

	![][10]

	Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end per app mobili e i dati vengono visualizzati nell'elenco.

	> [AZURE.NOTE]È possibile esaminare il codice che accede al back-end per app mobili per eseguire una query e inserire i dati trovati nel file C# ToDoActivity.cs.



<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[portale di Azure]: https://azure.portal.com/
[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=August15_HO6-->