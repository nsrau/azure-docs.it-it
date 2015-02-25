<properties 
	pageTitle="Introduzione a Servizi mobili per app per Xamarin" 
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
	ms.date="11/22/2014" 
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per Xamarin.iOS mediante Servizi mobili di Azure. In questa esercitazione si creeranno un nuovo servizio mobile e una semplice app <em>To do list</em> che archivia i dati dell'app nel nuovo servizio mobile.

Se si preferisce guardare un video, nel clip seguente vengono eseguiti gli stessi passaggi dell'esercitazione.

Video: "Introduzione a Xamarin e a Servizi mobili di Azure" con Craig Dunn, Developer Evangelist per Xamarin (durata: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



Di seguito è riportata una schermata dell'app completata:

![][0]

Per completare questa esercitazione è necessario disporre di XCode e [Xamarin Studio] per OS X oppure del plug-in Xamarin Visual Studio per Visual Studio in Windows. L'esempio verrà eseguito su iOS 5.0 e versioni successive.

> [AZURE.IMPORTANT] Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F"%20target="_blank).

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Creare una nuova app per Xamarin.iOS</h2>

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile. 

In questa sezione si creerà una nuova app di Xamarin.iOS connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Nella scheda Guida introduttiva fare clic su **Xamarin.iOS** in **Scegli piattaforma** ed espandere **Crea una nuova app Xamarin.iOS**.

	![][6]

	Di seguito sono visualizzati i tre semplici passaggi per creare un'app di Xamarin.iOS connessa al servizio mobile.

  	![][7]

3. Se necessario, scaricare e installare Xcode v6.0 o versione successiva e [Xamarin Studio].

4. Fare clic su **Crea tabella TodoItem** per creare una tabella in cui archiviare i dati dell'app.

5. In **Scaricare ed eseguire l'applicazione** fare clic su **Download**. 

	Verrà scaricato il progetto per l'applicazione _To do list_ di esempio connessa al servizio mobile e si farà riferimento al componente Servizi mobili di Azure per Xamarin.iOS. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

<h2>Eseguire la nuova app per Xamarin.iOS</h2>

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso, quindi aprire il file di soluzione **XamarinTodoQuickStart.iOS.sln** in Xamarin Studio o in Visual Studio.

	![][8]

	![][9]

2. Premere il pulsante **Run** per compilare il progetto e avviare l'applicazione nell'emulatore di iPhone, che corrisponde all'impostazione predefinita per questo progetto.

3. Nell'app digitare un testo significativo, ad esempio _Completare l'esercitazione_, quindi fare clic sull'icona con il segno più (**+**).

	![][10]

	Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

	> [AZURE.NOTE] È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file TodoService.cs C#.

4. Tornare al portale di gestione e fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItems**.

	![][11]

	In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

	![][12]

## <a name="next-steps"> </a>Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili: 

## Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili: 

* [Introduzione alla sincronizzazione dei dati offline]
  <br/>Informazioni su come usare la sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. -->
[Introduzione a Servizi mobili]:#getting-started
[Creare un nuovo servizio mobile]:#create-new-service
[Definire l'istanza del servizio mobile]:#define-mobile-service-instance
[Passaggi successivi]:#next-steps

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
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introduzione alla sincronizzazione dei dati offline]: /it-it/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portale di gestione]: https://manage.windowsazure.com/


<!--HONumber=42-->
