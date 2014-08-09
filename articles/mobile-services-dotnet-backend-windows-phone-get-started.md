<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introduzione a Servizi mobili
=============================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started "Windows Store C#")[Windows Store JavaScript](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started "Windows Store JavaScript")[Windows Phone](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started "Windows Phone")[iOS](/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started "iOS") [Android](/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started "Android")
[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/ "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-windows-phone-get-started/ "Back-end JavaScript")

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app per Windows Phone 8 mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Il servizio mobile che verrà creato utilizza i linguaggi .NET supportati tramite Visual Studio per la logica di business sul lato server e per la gestione del servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript](/it-it/documentation/articles/mobile-services-windows-phone-get-started) di questo argomento.

Di seguito è riportata una schermata dell'app completata:

![](./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png)

> [WACOM.NOTE]Per completare questa esercitazione, è necessario disporre di un account Azure in cui sia abilitata la funzionalità Servizi mobili di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F).
> Per completare questa esercitazione, è necessario disporre di [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546). È disponibile una versione di valutazione gratuita.

Creazione di un nuovo servizio mobile
-------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

Creazione di una nuova app per Windows Phone
--------------------------------------------

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app per Windows Phone 8 connessa al servizio mobile.

1. Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2. Nella scheda Quickstart fare clic su **Windows Phone 8** in **Choose platform** ed espandere **Create a new Windows Phone 8 app**.

    ![][6]

    Di seguito sono visualizzati i tre semplici passaggi per creare un'app per Windows Phone connessa al servizio mobile.

	![](./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png)

1. Se necessario, scaricare e installare [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546) nel computer locale o nella macchina virtuale.

2. In **Download and publish you service to the cloud** fare clic su **Download**.

	Verrà scaricata una soluzione che contiene progetti sia per il servizio mobile, sia per l'applicazione *To do list* di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

5. In **Publish your service to the cloud** scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

Test del servizio mobile sul computer locale
--------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE]Sono necessari passaggi aggiuntivi per la configurazione per eseguire un'app per Windows Phone che si connette a un servizio locale. Questa procedura non verrà illustrata nel presente argomento, ma ulteriori informazioni sono disponibili nella pagina relativa alla [connessione a un servizio Web locale dall'emulatore di Windows Phone 8](http://go.microsoft.com/fwlink/p/?LinkId=391930).

Pubblicazione del servizio mobile
---------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  Nel progetto di app per Windows Phone aprire il file App.xaml.cs, individuare il codice che crea un'istanza di [MobileServiceClient](http://msdn.microsoft.com/it-it/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx), rimuovere i simboli di commento dal codice che crea il client utilizzando *localhost*, quindi rimuovere i simboli di commento dal codice che crea il client utilizzando l'URL del servizio remoto, che ha un aspetto simile al seguente:

    ``` {}
    public static MobileServiceClient MobileService = new MobileServiceClient(
                "https://todolist.azure-mobile.net/",
                "XXXXXXX-APPLICATION-KEY-XXXXXXXX");
    ```

    Il client accederà al servizio mobile pubblicato in Azure.

2.  Premere **F5** per ricompilare il progetto e avviare l'app.

3.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* e quindi fare clic su **Save**.

    Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

    **Nota**

    È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file MainPage.xaml.cs.

![](./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png)

Viene mostrato come eseguire la nuova app client nel servizio mobile in esecuzione in Azure. Per poter testare l'app per Windows Phone con il servizio mobile in esecuzione in un computer locale, è necessario configurare il server Web e il firewall in modo da consentire l'accesso dal dispositivo o dall'emulatore di Windows Phone. Per ulteriori informazioni, vedere [Configurazione del server Web locale per consentire le connessioni a un servizio mobile locale](/it-it/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

Passaggi successivi
-------------------

Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

-   [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data) <br/>
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users) <br/>
    Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

-   [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push) <br/>
    Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png

[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-service-startup.pnG

[10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Get started with authentication]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Management Portal]: https://manage.windowsazure.com/
[JavaScript backend version]: /it-it/documentation/articles/mobile-services-windows-phone-get-started
[How to connect to a local web service from the Windows Phone 8 emulator]: http://go.microsoft.com/fwlink/p/?LinkId=391930
