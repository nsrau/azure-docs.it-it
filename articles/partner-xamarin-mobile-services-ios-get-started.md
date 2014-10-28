<properties pageTitle="Get Started with Mobile Services for Xamarin iOS apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="craigd"></tags>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app di Xamarin.iOS mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile.

Se si preferisce guardare un video, nel clip seguente vengono eseguiti gli stessi passaggi dell'esercitazione.

Video: "Introduzione a Xamarin e a Servizi mobili di Azure" con Craig Dunn, Developer Evangelist per Xamarin (durata: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Di seguito è riportata una schermata dell'app completata:

![][]

Per completare questa esercitazione, è necessario disporre di XCode 4.5 e iOS 5.0 o versioni successive nonché di [Xamarin Studio][] per OS X oppure del plug-in Xamarin Visual Studio per Visual Studio in Windows.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario un account Azure. Se non si ha un account, &egrave; possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

## <a name="create-new-service"> </a>Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## <span class="short-header">Creazione di una nuova app</span>Creazione di una nuova app di Xamarin.IOS

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app di Xamarin.iOS connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Nella scheda Quickstart fare clic su **Xamarin.iOS** in **Choose platform** ed espandere **Create a new Xamarin.iOS app**.

    ![][1]

    Di seguito sono visualizzati i tre semplici passaggi per creare un'app di Xamarin.iOS connessa al servizio mobile.

    ![][2]

3.  Se necessario, scaricare e installare [Xcode] 4.4 o versione successiva e [Xamarin Studio][].

4.  Fare clic su **Create TodoItems table** per creare una tabella in cui archiviare i dati dell'app.

5.  In **Download and run app** fare clic su **Download**.

    Viene scaricato il progetto per l'applicazione di esempio *To do list* collegato al servizio mobile e viene fatto riferimento al componente Servizi mobili di Azure per Xamarin.iOS. Salvare il file di progetto compresso nel computer locale e prendere nota dell'ubicazione in cui è stato salvato.

## <span class="short-header">Esecuzione dell'app</span>Esecuzione della nuova app di Xamarin.iOS

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1.  Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli nel computer in uso, quindi aprire il file di soluzione **XamarinTodoQuickStart.iOS.sln** in Xamarin Studio o in Visual Studio.

    ![][3]

    ![][4]

2.  Fare clic su **Esegui** per compilare il progetto e avviare l'applicazione nell'emulatore iPhone, che è l'impostazione predefinita per questo progetto.

3.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial*, quindi fare clic sull'icona con il segno PIÙ (**+**).

    ![][5]

    Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

    <div class="dev-callout"> 
<b>Nota</b> 
<p>&Egrave; possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file TodoService.cs C#.</p> 
</div>

4.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItems**.

    ![][6]

    In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

    ![][7]

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

-   [Introduzione ai dati][]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione alla sincronizzazione dei dati offline][]
    Informazioni su come usare la sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

-   [Introduzione alle notifiche push][]
    Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  []: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [Xamarin Studio]: http://xamarin.com/download
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
  [5]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
  [6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Introduzione alla sincronizzazione dei dati offline]: /it-it/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios
