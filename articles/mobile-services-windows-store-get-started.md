<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Introduzione a Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store" class="current">Windows Store</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
    <a href="/it-it/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
    <a href="/it-it/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
    <a href="/it-it/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
    <a href="/it-it/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
    <a href="/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title=".NET backend">Back-end .NET</a> | 
    <a href="/it-it/documentation/articles/mobile-services-windows-store-get-started/"  title="JavaScript backend" class="current">Back-end JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app di Windows Store mediante Servizi mobili di Azure.</p>
<p>Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione. Nel video Scott Guthrie introduce Servizi mobili ed esamina tutti i passaggi necessari per creare il primo servizio mobile e connettersi ad esso da un'app di Windows Store.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">10:08:00</span></div>

</div>

In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Per il servizio mobile creato verrà utilizzato JavaScript per la logica di business sul lato server. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server nei linguaggi .NET supportati tramite Visual Studio, vedere la versione per back-end .NET di questo argomento.

Di seguito è riportata una schermata dell'app completata:

![][0]

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Servizi mobili relative ad app di Windows Store.

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

> Per completare questa esercitazione, è necessario disporre di Visual Studio 2013. Per connettersi a un'app di Windows Store tramite Visual Studio 2012, eseguire le operazioni descritte nell'argomento [Introduzione ai dati in Servizi mobili tramite Visual Studio 2012][Introduzione ai dati in Servizi mobili tramite Visual Studio 2012].

## Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Creazione di una nuova app di Windows Store

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app di Windows Store connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Nella scheda Quickstart fare clic su **Windows** in **Choose platform** ed espandere **Create a new Windows Store app**.

    ![][1]

    Di seguito sono visualizzati i tre semplici passaggi per creare un'app di Windows Store connessa al servizio mobile.

    ![][2]

3.  Se necessario, scaricare e installare [Visual Studio 2013 Express per Windows][Visual Studio 2013 Express per Windows] nel computer locale o nella macchina virtuale.

4.  Fare clic su **Create TodoItem table** per creare una tabella in cui archiviare i dati dell'app.

5.  In **Download and run your app** selezionare un linguaggio per l'app, quindi fare clic su **Download**.

    Verrà scaricato il progetto per l'applicazione *To do list* di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione dell'app per Windows

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1.  Individuare il percorso in cui sono stati salvati i file compressi del progetto ed espanderli sul computer in uso, quindi aprire il file di soluzione in Visual Studio 2013 Express per Windows.

2.  Premere **F5** per ricompilare il progetto e avviare l'app.

3.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial*, in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][3]

    Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nella seconda colonna dell'app.

    > [WACOM.NOTE]È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati trovati nel file MainPage.xaml.cs (progetto C#/XAML) o nel file default.js predefinito (progetto JavaScript/HTML).

4.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItems**.

    ![][4]

    In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

    ![][5]

## Passaggi successivi

Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

-   **Introduzione ai dati** ([C#][C#]/[JavaScript][JavaScript])
    Ulteriori informazioni sull'archiviazione e sull'esecuzione di query dei dati mediante Servizi mobili.

-   **Introduzione all'autenticazione** ([C#][6]/[JavaScript][7])
    Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

-   **Introduzione alle notifiche push** ([C#][8]/[JavaScript][9])
    Informazioni sull'invio di una notifica push di base all'app.



  [0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Introduzione ai dati in Servizi mobili tramite Visual Studio 2012]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
  [2]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
  [Visual Studio 2013 Express per Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [3]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
  [4]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png
  [C#]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet
  [JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-data-js
  [6]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
  [7]: /it-it/develop/mobile/tutorials/get-started-with-users-js
  [8]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet
  [9]: /it-it/develop/mobile/tutorials/get-started-with-push-js
