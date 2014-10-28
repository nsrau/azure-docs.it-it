<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

In questa esercitazione viene illustrato come aggiungere un servizio back-end basato su cloud a un'app per iOS mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Il servizio mobile che verrà creato utilizza i linguaggi .NET supportati tramite Visual Studio per la logica di business sul lato server e per la gestione del servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript][] di questo argomento.

Di seguito è riportata una schermata dell'app completata:

![][]

Per completare questa esercitazione, è necessario disporre di XCode 4.5 e iOS 5.0 o versioni successive.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario un account Azure. Se non si ha un account, &egrave; possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

## <a name="create-new-service"> </a>Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][]]

## Download del servizio mobile sul computer locale

Dopo aver creato il servizio mobile scaricare il progetto di servizio mobile personalizzato da eseguire sul computer locale o sulla macchina virtuale.

1.  Fare clic sul servizio mobile appena creato, quindi nella scheda Quickstart fare clic su **iOS** in **Choose platform** ed espandere **Create a new iOS app**.

    ![][1]

2.  Se necessario, scaricare e installare Visual Studio Professional 2013 o una versione successiva.

3.  Fare clic su **Download** in **Download and publish your service to the cloud**.

    Verrà scaricato il progetto di Visual Studio che implementerà il servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

4.  Inoltre, scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

## Test del servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## Pubblicazione del servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## Creazione di una nuova app per iOS

In questa sezione si creerà una nuova app per iOS connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Nella scheda Quickstart fare clic su **iOS** in **Choose platform** ed espandere **Create a new iOS app**.

3.  Se necessario, scaricare e installare [Xcode][] 4.4 o versione successiva.

4.  In **Download and run your app** fare clic su **Download**.

    Verrà scaricato il progetto per l'applicazione *To do list* di esempio connessa al servizio mobile, unitamente all'SDK di Servizi Mobili per iOS. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione della nuova app per iOS

[WACOM.INCLUDE [mobile-services-ios-run-app][]]

Viene mostrato come eseguire la nuova app client nel servizio mobile in esecuzione in Azure. Per poter testare l'app per iOS con il servizio mobile in esecuzione in un computer locale, è necessario configurare il server Web e il firewall in modo da consentire l'accesso dal computer di sviluppo iOS. Per ulteriori informazioni, vedere [Configurazione del server Web locale per consentire le connessioni a un servizio mobile locale][].

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

-   [Risoluzione dei problemi relativi a un back-end .NET di Servizi mobili][]
     Informazioni su come diagnosticare e correggere i problemi che possono verificarsi con un back-end .NET di Servizi mobili.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [versione per back-end JavaScript]: /it-it/documentation/articles/mobile-services-ios-get-started
  []: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [mobile-services-ios-run-app]: ../includes/mobile-services-ios-run-app.md
  [Configurazione del server Web locale per consentire le connessioni a un servizio mobile locale]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [Introduzione alla sincronizzazione dei dati offline]: /it-it/documentation/articles/mobile-services-ios-get-started-offline-data
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
  [Risoluzione dei problemi relativi a un back-end .NET di Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
