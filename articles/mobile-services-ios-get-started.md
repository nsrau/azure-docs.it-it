<properties  pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>Introduzione a Servizi mobili
<div class="dev-center-tutorial-selector sublanding">
	<a  href="/it-it/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store">Windows Store</a>
	<a  href="/it-it/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a  href="/it-it/documentation/articles/mobile-services-ios-get-started" title="iOS" class="current">iOS</a> <a  href="/it-it/documentation/articles/mobile-services-android-get-started" title="Android">Android</a> <a  href="/it-it/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a  href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a  href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a  href="/it-it/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a  href="/it-it/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

 
<div class="dev-center-tutorial-subselector">
	<a  href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started/" title="Back-end .NET">Back-end .NET</a> | 
	<a  href="/it-it/documentation/articles/mobile-services-ios-get-started/" title="Back-end JavaScript" class="current">Back-end JavaScript</a>
</div>

 
<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app per iOS mediante Servizi mobili di Azure.</p>
<p>Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione.</p>
</div>
<div class="dev-onpage-video-wrapper"><a  href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">guarda l'esercitazione</a> <a  style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span  class="icon">Riproduci video</span></a> <span  class="time">9:38</span></div>

In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Per il servizio mobile creato verrà utilizzato JavaScript per la logica di business sul lato server. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server nei linguaggi .NET supportati tramite Visual Studio, vedere la [versione per backend .NET] di questo argomento.

Di seguito è riportata una schermata dell'app completata:

![][0]

Per completare questa esercitazione, è necessario disporre di XCode 4.5 e iOS 5.0 o versioni successive.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a  href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

## <a name="create-new-service"> </a>Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2><span class="short-header">Creazione di una nuova app</span>Creazione di una nuova app per iOS</h2>

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app per iOS connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Nella scheda Quickstart fare clic su **iOS** in **Choose platform** ed espandere **Create a new iOS app**.
    
	![][6]

	Di seguito sono visualizzati i tre semplici passaggi per creare un'app per iOS connessa al servizio mobile.

	![][7]

3.  Se necessario, scaricare e installare [Xcode] 4.4 o versione successiva.

4.  Fare clic su **Create TodoItems table** per creare una tabella in cui archiviare i dati dell'app.

5.  In **Download and run your app** fare clic su **Download**.

 Verrà scaricato il progetto per l'applicazione *To do list* di esempio connessa al servizio mobile, unitamente all'SDK di Servizi Mobili per iOS. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione della nuova app per iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

1.   Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItems**.
	
	![](./media/mobile-services-ios-get-started/mobile-data-tab.png)
     
	In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.
    
    ![](./media/mobile-services-ios-get-started/mobile-data-browse.png)
    

## <a name="next-steps"> </a>Passaggi successivi 
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili: 

* [Introduzione ai dati]
  <br/>Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app.


<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. --> 
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png

<!-- URLs. -->
[Introduzione ai dati]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET backend version]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started
    