<properties
	pageTitle="Introduzione a Servizi mobili per app per Xamarin iOS | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo per Xamarin iOS."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="conceptdev"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/05/2015"
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per Xamarin.iOS mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile.

Se si preferisce guardare un video, nel clip seguente vengono eseguiti gli stessi passaggi dell'esercitazione.

Video "Introduzione a Xamarin e a Servizi mobili di Azure" con Craig Dunn, Developer Evangelist per Xamarin (durata: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Di seguito è riportata una schermata dell'app completata:

![][0]

Per completare questa esercitazione è necessario disporre di XCode e [Xamarin Studio] per OS X oppure del plug-in Xamarin Visual Studio per Visual Studio in Windows. L'esempio verrà eseguito su iOS 5.0 e versioni successive.

> [AZURE.IMPORTANT]Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creare una nuova app per Xamarin.iOS

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app per Xamarin.iOS connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2. Nella scheda Quickstart fare clic su **Xamarin.iOS** in **Choose platform** ed espandere **Create a new Xamarin.iOS app**.

	![][6]

	Di seguito sono visualizzati i tre semplici passaggi per creare un'app di Xamarin.iOS connessa al servizio mobile.

  	![][7]

3. Se necessario, scaricare e installare Xcode (si consiglia la versione più recente, v6.0 o versione successiva) e [Xamarin Studio].

4. Fare clic su **Create TodoItems table** per creare una tabella in cui archiviare i dati dell'app.

5. In **Download and run app** fare clic su **Download**.

	Verrà scaricato il progetto per l'applicazione di esempio _To do list_ connessa al servizio mobile e si farà riferimento al componente Servizi mobili di Azure per Xamarin.iOS. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Eseguire la nuova app per Xamarin.iOS

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso, quindi aprire il file di soluzione **XamarinTodoQuickStart.iOS.sln** in Xamarin Studio o in Visual Studio.

	![][8]

	![][9]

2. Fare clic su **Esegui** per compilare il progetto e avviare l'applicazione nell'emulatore iPhone, che è l'impostazione predefinita per questo progetto.

3. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_, quindi fare clic sull'icona con il segno PIÙ (**+**).

	![][10]

	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

	> [AZURE.NOTE]È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file TodoService.cs C#.

4. Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItems**.

	![][11]

	In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

	![][12]


## Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

* [Introduzione alla sincronizzazione dei dati offline] Informazioni sull'uso della sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

* [Introduzione all'autenticazione] Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push] Informazioni sull'invio di una notifica push di base all'app.

* [Come utilizzare il client componente Xamarin per Servizi mobili di Azure](partner-xamarin-mobile-services-how-to-use-client-library.md) Informazioni su come eseguire una query nel servizio mobile, utilizzare i dati e accedere alle API personalizzate.
  

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introduzione alla sincronizzazione dei dati offline]: mobile-services-xamarin-ios-get-started-offline-data.md
[Introduzione all'autenticazione]: partner-xamarin-mobile-services-ios-get-started-users.md
[Introduzione alle notifiche push]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/

<!---HONumber=Nov15_HO3-->