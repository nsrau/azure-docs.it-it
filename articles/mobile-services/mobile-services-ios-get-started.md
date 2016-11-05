---
title: Introduzione a Servizi mobili di Azure per le app iOS | Microsoft Docs
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
ms.topic: hero-article
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

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per iOS mediante Servizi mobili di Azure.

In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Per il servizio mobile creato verrà usato JavaScript per la logica di business sul lato server. Per creare un servizio mobile con la logica di business sul lato server in .NET, vedere la sezione [Versione per back-end .NET] di questo argomento.

> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e [ottenere servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione](https://azure.microsoft.com/pricing/details/mobile-services/). Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20).
> 
> 

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creare una nuova app per iOS
Per creare una nuova app connessa al servizio mobile, è possibile seguire una facile guida introduttiva nel portale di Azure classico:

1. Nel [portale di Azure classico] fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.
2. Nella scheda Avvio rapido fare clic su **iOS** in **Scegli una piattaforma** ed espandere **Crea una nuova app iOS**. Verranno visualizzati i passaggi per creare un'app per iOS connessa al servizio mobile.
3. Fare clic su **Create TodoItem table** per creare una tabella in cui archiviare i dati dell'app.
4. In **Download and run your app** fare clic su **Download**. Verrà scaricato il progetto per l'applicazione *To do list* di esempio connessa al servizio mobile, unitamente all'SDK di Servizi Mobili per iOS. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione della nuova app per iOS
[!INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4"> <li><p>Tornare al [portale di Azure classico], fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**. In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.<p></li></ol></p>

## <a name="next-steps"> </a>Passaggi successivi
Informazioni su come eseguire altre importanti attività in Servizi mobili:

* [Introduzione alla sincronizzazione dei dati offline] <br/>Informazioni su come usare la sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.
* [Aggiungere l'autenticazione a un'app esistente] <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.
* [Aggiungere notifiche push a un'app esistente] <br/>Informazioni sull'invio di una notifica push di base all'app.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introduzione alla sincronizzazione dei dati offline]: mobile-services-ios-get-started-offline-data.md
[Aggiungere l'autenticazione a un'app esistente]: mobile-services-dotnet-backend-ios-get-started-users.md
[Aggiungere notifiche push a un'app esistente]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[portale di Azure classico]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Versione per back-end .NET]: mobile-services-dotnet-backend-ios-get-started.md

<!---HONumber=AcomDC_0727_2016-->