<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app per Windows Phone 8 mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Il servizio mobile che verrà creato utilizza i linguaggi .NET supportati tramite Visual Studio per la logica di business sul lato server e per la gestione del servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript][versione per back-end JavaScript] di questo argomento.

Di seguito è riportata una schermata dell'app completata:

![][0]

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].
> Per completare questa esercitazione, è necessario disporre di [Visual Studio Professional 2013][Visual Studio Professional 2013]. È disponibile una versione di valutazione gratuita. Per creare una nuova app per Windows Phone 8.1, è necessario che sia installato Visual Studio 2013 Update 2 o versione successiva.

## <a name="create-new-service"> </a>Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creazione di una nuova app per Windows Phone

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile.

In questa sezione si creerà una nuova app per Windows Phone 8 connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Nella scheda Quickstart fare clic su **Windows Phone 8** in **Choose platform** ed espandere **Create a new Windows Phone 8 app**.

    ![][1]

    Di seguito sono visualizzati i tre semplici passaggi per creare un'app per Windows Phone connessa al servizio mobile.

    ![][2]

3.  Se necessario, scaricare e installare [Visual Studio Professional 2013][Visual Studio Professional 2013] nel computer locale o nella macchina virtuale.

4.  In **Download and publish you service to the cloud** fare clic su **Download**.

    Verrà scaricata una soluzione che contiene progetti sia per il servizio mobile, sia per l'applicazione *To do list* di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

5.  In **Publish your service to the cloud** scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

## Test del servizio mobile sul computer locale

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE]Sono necessari passaggi aggiuntivi per la configurazione per eseguire un'app per Windows Phone che si connette a un servizio locale. Questa procedura non verrà illustrata nel presente argomento, ma ulteriori informazioni sono disponibili nella pagina relativa alla [connessione a un servizio Web locale dall'emulatore di Windows Phone 8][connessione a un servizio Web locale dall'emulatore di Windows Phone 8].

## Pubblicazione del servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  Nel progetto di app per Windows Phone aprire il file App.xaml.cs, individuare il codice che crea un'istanza di [MobileServiceClient][MobileServiceClient], impostare come commento il codice per la creazione del client usando *localhost*, quindi rimuovere il commento dal codice per la creazione del client usando l'URL del servizio remoto, simile al seguente:

        public static MobileServiceClient MobileService = new MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");

    Il client accederà al servizio mobile pubblicato in Azure.

2.  (Facoltativo) Se si crea un'app per Windows Phone 8.1, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, scegliere **Proprietà**, impostare **Versione di destinazione di Windows Phone OS** su **Windows Phone 8.1**, quindi fare clic su **Sì** per aggiornare il progetto.

3.  Premere **F5** per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* e quindi fare clic su **Save**.

    Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

    <div class="dev-callout"> 
    <b>Nota</b> 
    <p>&Egrave; possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file MainPage.xaml.cs.</p> 
    </div>

![][3]

Viene mostrato come eseguire la nuova app client nel servizio mobile in esecuzione in Azure. Per poter testare l'app per Windows Phone con il servizio mobile in esecuzione in un computer locale, è necessario configurare il server Web e il firewall in modo da consentire l'accesso dal dispositivo o dall'emulatore di Windows Phone. Per ulteriori informazioni, vedere [Configurazione del server Web locale per consentire le connessioni a un servizio mobile locale][Configurazione del server Web locale per consentire le connessioni a un servizio mobile locale].

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione alla sincronizzazione dei dati offline][Introduzione alla sincronizzazione dei dati offline]
    Informazioni su come usare la sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Risoluzione dei problemi relativi a un back-end .NET di Servizi mobili][Risoluzione dei problemi relativi a un back-end .NET di Servizi mobili]
     Informazioni su come diagnosticare e correggere i problemi che possono verificarsi con un back-end .NET di Servizi mobili.



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [versione per back-end JavaScript]: /it-it/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [connessione a un servizio Web locale dall'emulatore di Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkId=391930
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [MobileServiceClient]: http://msdn.microsoft.com/it-it/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [Configurazione del server Web locale per consentire le connessioni a un servizio mobile locale]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Introduzione alla sincronizzazione dei dati offline]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [Risoluzione dei problemi relativi a un back-end .NET di Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
