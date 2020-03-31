---
title: Creare un'app UWP
description: Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili di Azure per la distribuzione di app UWP (Universal Windows Platform) in C#, Visual Basic o JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9188db19adab9bd46d65fc97f1c62b39141cee90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461386"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Creare un'app Windows con un back-end di Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app UWP (Universal Windows Platform). Per altre informazioni, vedere [Informazioni sulle app per dispositivi mobili](app-service-mobile-value-prop.md). Di seguito sono riportate alcune schermate dell'app completata:

![App desktop completata](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni delle app per dispositivi mobili relative ad app UWP.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere Versione di valutazione gratuita di Azure .For [details,](https://azure.microsoft.com/pricing/free-trial/)see Azure Free Trial .
* Windows 10.
* Community di Visual Studio 2017.
* Familiarità con lo sviluppo di app UWP. Vedere la [documentazione di UWP](https://docs.microsoft.com/windows/uwp/) per informazioni su come [avviare la configurazione](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) per creare app UWP.

## <a name="create-a-new-azure-mobile-app-backend"></a>Creare un nuovo back-end dell'app per dispositivi mobili di Azure

Per creare un nuovo back-end dell'app per dispositivi mobili, attenersi alla procedura seguente.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Creare una connessione al database e configurare il progetto client e server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Eseguire il progetto client

1. Aprire il progetto UWP.

2. Passare al portale di [Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili creata. Nel `Overview` pannello cercare l'URL che è l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il nome del sito per il nome https://test123.azurewebsites.netdell'app "test123" sarà .

3. Aprire il `App.xaml.cs` file in questa cartella - windows-uwp-cs/'UMOAPPNAME/. Il nome `ZUMOAPPNAME`dell'applicazione è .

4. In `App` classe `ZUMOAPPURL` sostituire il parametro con l'endpoint pubblico precedente.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    diventa
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Premere il tasto F5 per distribuire ed eseguire l'app.

6. Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* nella casella di testo **Insert a TodoItem** (Inserire TodoItem), quindi fare clic su **Save** (Salva).

    ![Desktop completo di guida introduttiva di Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure.
