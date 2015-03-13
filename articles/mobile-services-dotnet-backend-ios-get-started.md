<properties 
	pageTitle="Introduzione a Servizi mobili di Azure per le app iOS" 
	description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo per iOS." 
	services="" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per iOS mediante Servizi mobili di Azure. In questa esercitazione si creeranno un nuovo servizio mobile e una semplice app _To do list_ che archivia i dati dell'app nel nuovo servizio mobile. Il servizio mobile che verrà creato usa i linguaggi .NET supportati tramite Visual Studio per la logica di business sul lato server e per la gestione del servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript] di questo argomento.

Di seguito è riportata una schermata dell'app completata:

![][0]

Per completare questa esercitazione, è necessario disporre di XCode 4.5 e iOS 5.0 o versioni successive.

> [AZURE.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20target="_blank").

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Scaricare il servizio mobile nel computer locale

Dopo aver creato il servizio mobile scaricare il progetto di servizio mobile personalizzato da eseguire sul computer locale o sulla macchina virtuale.

1. Fare clic sul servizio mobile appena creato, quindi nella scheda Avvio rapido fare clic su **iOS** in **Scegli piattaforma** ed espandere **Crea una nuova app iOS**.

	![][1]

2. Se necessario, scaricare e installare Visual Studio Professional 2013 o una versione successiva.

3. Fare clic su **Scarica** in **Scaricare e pubblicare il servizio nel cloud**.

	Verrà scaricato il progetto di Visual Studio che implementerà il servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

4. Inoltre, scaricare il proprio profilo di pubblicazione, salvare il file scaricato nel computer locale e prendere nota del percorso.

## Testare il servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Pubblicare il servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Creare una nuova app per iOS

In questa sezione si creerà una nuova app per iOS connessa al servizio mobile.

1. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Nella scheda Avvio rapido fare clic su **iOS** in **Scegli piattaforma** ed espandere **Crea una nuova app iOS**.

3. Se necessario, scaricare e installare [Xcode] 4.4 o versione successiva.

4. In **Scaricare ed eseguire l'applicazione** fare clic su **Download**.

  	Verrà scaricato il progetto per l'applicazione _To do list_ di esempio connessa al servizio mobile, unitamente all'SDK di Servizi Mobili per iOS. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione della nuova app per iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Viene mostrato come eseguire la nuova app client nel servizio mobile in esecuzione in Azure. Per poter testare l'app per iOS con il servizio mobile in esecuzione in un computer locale, è necessario configurare il server Web e il firewall in modo da consentire l'accesso dal computer di sviluppo iOS. Per altre informazioni, vedere [Configurare il server Web locale per consentire le connessioni a un servizio mobile locale](/it-it/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

## <a name="next-steps"> </a>Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione alla sincronizzazione dei dati offline]
  <br/>Informazioni su come usare la sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Risolvere i problemi relativi a un back-end .NET di servizi mobili]
  <br/> Informazioni su come diagnosticare e correggere i problemi che possono verificarsi con un back-end .NET di Servizi mobili.

<!-- Anchors. -->
[Introduzione a Servizi mobili]:#getting-started
[Creare un nuovo servizio mobile]:#create-new-service
[Definire l'istanza del servizio mobile]:#define-mobile-service-instance
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Introduzione alla sincronizzazione dei dati offline]: /it-it/documentation/articles/mobile-services-ios-get-started-offline-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
[Risolvere i problemi relativi a un back-end .NET di servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portale di gestione]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Versione per back-end JavaScript]: /it-it/documentation/articles/mobile-services-ios-get-started


<!--HONumber=42-->
