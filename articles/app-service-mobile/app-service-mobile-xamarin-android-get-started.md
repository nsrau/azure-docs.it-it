<properties
	pageTitle="Introduzione alle app per dispositivi mobili di Azure per Xamarin.Android"
	description="Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili di Azure per lo sviluppo per Xamarin Android."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="adrianhall"
	manager="erikre"
	editor="" />  

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="10/01/2016"
	ms.author="adrianha" /> 

#Creare un'app per Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Overview

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app Xamarin.Android. Per altre informazioni, vedere [Informazioni sulle app per dispositivi mobili](app-service-mobile-value-prop.md).

Di seguito è riportata una schermata dell'app completata:

![][0]

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative alle app per dispositivi mobili per Xamarin.Android.

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio con Xamarin. Per istruzioni, vedere [Configurazione e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
 
>[AZURE.NOTE] Se si desidera iniziare a usare il servizio app di Azure prima di creare un account Azure, passare al sito [Prova il servizio app](https://tryappservice.azure.com/?appServiceName=mobile), in cui è possibile creare immediatamente un'app per dispositivi mobili di base temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare un nuovo back-end dell'app per dispositivi mobili di Azure

Per creare un nuovo back-end dell'app per dispositivi mobili, attenersi alla procedura seguente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

È stato eseguito il provisioning di un back-end dell'app per dispositivi mobili di Azure che può essere usato dalle applicazioni client per dispositivi mobili. Successivamente, scaricare un progetto server per un semplice back-end "todo list" e pubblicarlo in Azure.

## Configurare il progetto server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Scaricare ed eseguire l'app per Xamarin.Android

1. In **Scarica ed esegui il progetto Xamarin.Android** scegliere il pulsante **Scarica**.

  	Verrà scaricato un progetto che contiene un'applicazione client connessa all'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

2. Premere **F5** per compilare il progetto e avviare l'app.

3. Nell'app digitare un testo significativo, ad esempio _Completare l'esercitazione_, quindi fare clic sul pulsante **Aggiungi**.

	![][10]

	Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end per app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

	> [AZURE.NOTE] È possibile esaminare il codice che accede al back-end per app mobili per eseguire una query e inserire i dati trovati nel file C# ToDoActivity.cs.

##Passaggi successivi

* [Aggiungere l'autenticazione all'app](app-service-mobile-xamarin-android-get-started-users.md): informazioni sull'autenticazione degli utenti dell'app con un provider di identità.
* [Aggiungere notifiche push all'app Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md): informazioni sull'aggiunta di notifiche push all'app.
* [Come usare il client gestito per App per dispositivi mobili di Azure](app-service-mobile-dotnet-how-to-use-client-library.md): informazioni su come utilizzare l'SDK del client gestito nell'app Xamarin.


<!-- Images. --> 
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1005_2016-->