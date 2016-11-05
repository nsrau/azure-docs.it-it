---
title: Introduzione a Servizi mobili di Azure per le app iOS
description: Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo per iOS.
services: mobile-services
documentationcenter: ios
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: krisragh

---
# <a name="getting-started"> </a>Introduzione a Servizi mobili
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Creare un'app per iOS in App per dispositivi mobili di Azure](../app-service-mobile/app-service-mobile-ios-get-started.md).
> 
> 

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per iOS mediante Servizi mobili di Azure. In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Il servizio mobile usa .NET e Visual Studio per la logica di business sul lato server. Per creare un servizio mobile con la logica di business sul lato server in JavaScript, vedere la sezione [Versione per back-end JavaScript] di questo argomento.

> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e [ottenere servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione](https://azure.microsoft.com/pricing/details/mobile-services/). Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-ios-get-started%2F).
> 
> 

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile
[!INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Scaricare il servizio mobile e l'app nel computer locale
Dopo aver creato il servizio mobile, scaricare i progetti che è possibile eseguire in locale.

1. Fare clic sul servizio mobile appena creato, quindi nella scheda Avvio rapido fare clic su **iOS** in **Scegli piattaforma** ed espandere **Crea una nuova app iOS**.
2. In un PC Windows fare clic su **Scarica** in **Scaricare e pubblicare il servizio nel cloud**. Verrà scaricato il progetto di Visual Studio che implementerà il servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.
3. In un computer Mac fare clic su **Scarica** in **Scaricare ed eseguire l'applicazione**. Verrà scaricato il progetto per l'applicazione *To do list* di esempio connessa al servizio mobile, unitamente a Mobile Services iOS SDK. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Testare il servizio mobile
[!INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Pubblicare il servizio mobile
[!INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Esecuzione della nuova app per iOS
[!INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

## <a name="next-steps"> </a>Passaggi successivi
Viene mostrato come eseguire la nuova app client nel servizio mobile in esecuzione in Azure. Per poter testare l'app per iOS con il servizio mobile in esecuzione in un computer locale, è necessario configurare il server Web e il firewall in modo da consentire l'accesso dal computer di sviluppo iOS. Per ulteriori informazioni, vedere [Configurazione del server Web locale per consentire le connessioni a un servizio mobile locale](mobile-services-dotnet-backend-how-to-configure-iis-express.md).

Informazioni su come eseguire altre importanti attività in Servizi mobili:

* [Introduzione alla sincronizzazione dei dati offline] <br/>Informazioni su come usare la sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.
* [Aggiungere l'autenticazione a un'app esistente] <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.
* [Aggiungere notifiche push a un'app esistente] <br/>Informazioni sull'invio di una notifica push di base all'app.
* [Risoluzione dei problemi relativi a un back-end .NET di Servizi mobili] <br/> Informazioni su come diagnosticare e correggere i problemi che possono verificarsi con un back-end .NET di Servizi mobili.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

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
[Introduzione alla sincronizzazione dei dati offline]: mobile-services-ios-get-started-offline-data.md
[Aggiungere l'autenticazione a un'app esistente]: mobile-services-dotnet-backend-ios-get-started-users.md
[Aggiungere notifiche push a un'app esistente]: mobile-services-dotnet-backend-ios-get-started-push.md
[Risoluzione dei problemi relativi a un back-end .NET di Servizi mobili]: mobile-services-dotnet-backend-how-to-troubleshoot.md
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Versione per back-end JavaScript]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_0727_2016-->