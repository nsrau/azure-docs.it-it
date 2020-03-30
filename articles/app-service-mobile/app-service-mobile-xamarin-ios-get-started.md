---
title: Introduzione alle app Xamarin.iOS
description: Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili per lo sviluppo per Xamarin iOS.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 6c35189e7c841fa2724f1cfe84afc689d5510676
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459005"
---
# <a name="create-a-xamarinios-app"></a>Creare un'app per Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Xamarin.iOS mediante un back-end per app per dispositivi mobili di Azure.  Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Xamarin.iOS *Todo list* che archivia i dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni Xamarin.iOS relative all'uso della funzionalità di Azure App Service relativa alle app per dispositivi mobili.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Un account Azure attivo. Se non è disponibile un account, iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere Versione di valutazione gratuita di Azure .For [details,](https://azure.microsoft.com/pricing/free-trial/)see Azure Free Trial .
* Visual Studio per Mac. Vedere [Installazione e installazione di Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Un Mac con Xcode 9.0 o versione successiva.
  
## <a name="create-an-azure-mobile-app-backend"></a>Creare un back-end dell'app per dispositivi mobili di Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Creare una connessione al database e configurare il progetto client e server
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Eseguire l'app Xamarin.iOS
1. Aprire il progetto Xamarin.iOS.

2. Passare al portale di [Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili creata. Nel `Overview` pannello cercare l'URL che è l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il nome del sito per il nome https://test123.azurewebsites.netdell'app "test123" sarà .

3. Aprire il `QSTodoService.cs` file in questa cartella - xamarin.iOS/ . Il nome `ZUMOAPPNAME`dell'applicazione è .

4. In `QSTodoService` classe `ZUMOAPPURL` sostituire la variabile con l'endpoint pubblico precedente.

    `const string applicationURL = @"ZUMOAPPURL";`

    diventa
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Premi il tasto F5 per distribuire ed eseguire l'app in un emulatore iPhone.

6. Nell'app digitare un testo significativo, ad esempio *Completare l'esercitazione* e quindi fare clic sul pulsante .

    ![][10]

    I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end per app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

   > [!NOTE]
   > È possibile esaminare il codice che accede al back-end per app mobili per eseguire una query e inserire i dati trovati nel file C# ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
