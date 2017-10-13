---
title: Introduzione alle app per dispositivi mobili del servizio app di Azure per app Xamarin.iOS | Documentazione Microsoft
description: Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili per lo sviluppo per Xamarin iOS.
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: syntaxc4
ms.openlocfilehash: 8dc965df2cd45366970effb29f246b0045a94717
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-xamarinios-app"></a>Creare un'app per Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Overview
Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Xamarin.iOS mediante un back-end per app per dispositivi mobili di Azure.  Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Xamarin.iOS *Todo list* che archivia i dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni Xamarin.iOS relative all'uso della funzionalità di Azure App Service relativa alle app per dispositivi mobili.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Un account Azure attivo. Se non è disponibile un account, iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio con Xamarin. Per le istruzioni vedere [Configurazione e installazione di Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .
* Un computer Mac in cui siano stati installati Xcode v7.0 o versione successiva e Xamarin Studio Community. Vedere [Configurazione e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [Configurazione, installazione e verifiche per gli utenti Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-an-azure-mobile-app-backend"></a>Creare un back-end dell'app per dispositivi mobili di Azure
Seguire questa procedura per creare un back-end dell'app per dispositivi mobili.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurare il progetto server
È stato eseguito il provisioning di un back-end dell'app per dispositivi mobili di Azure che può essere usato dalle applicazioni client per dispositivi mobili. Scaricare quindi un progetto server per un semplice back-end "todo list" e pubblicarlo in Azure.

Seguire questa procedura per configurare il progetto server per l'uso del back-end .NET o Node.js.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Scaricare ed eseguire l'app per Xamarin.iOS
1. Aprire il [portale di Azure] in una finestra del browser.
2. Nel pannello delle impostazioni dell'app per dispositivi mobili fare clic su **Introduzione** > **Xamarin.iOS**. Al passaggio 3 fare clic su **Crea una nuova app** , se l'opzione non è già selezionata.  Fare quindi clic sul pulsante **Download** .

      Verrà scaricata un'applicazione client che si connette al back-end mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.
3. Estrarre il progetto scaricato e aprirlo in Xamarin Studio (o in Visual Studio).

    ![][9]

    ![][8]
4. Premere F5 per compilare il progetto e avviare l'app nell'emulatore iPhone.
5. Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi fare clic sul pulsante **+**.

    ![][10]

    I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end per app mobili e i dati vengono visualizzati nell'elenco.

> [!NOTE]
> È possibile esaminare il codice che accede al back-end dell'app per dispositivi mobili per eseguire una query e inserire i dati nel file C# QSTodoService.cs.
>
>

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere la sincronizzazione offline all'app](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Add authentication to your app ](app-service-mobile-xamarin-ios-get-started-users.md)
* [Aggiungere notifiche push all'app Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Come usare il client gestito per App per dispositivi mobili di Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[portale di Azure]: https://portal.azure.com/
