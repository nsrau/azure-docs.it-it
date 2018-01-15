---
title: Introduzione alle app per dispositivi mobili in Xamarin.Forms
description: Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili per lo sviluppo per Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 01a8e356649c070a5cf36ae070195abce699f85e
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-xamarinforms-app"></a>Creare un'app Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Xamarin.Forms mediante la funzionalità App per dispositivi mobili del Servizio app di Azure come back-end. Vengono creati un nuovo back-end di app per dispositivi mobili e una semplice app Xamarin.Forms di tipo elenco attività che archivia dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative alle app per dispositivi mobili per Xamarin.Forms.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per altre informazioni, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio con Xamarin. Per informazioni, vedere la pagina [Configurare e installare Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

* Un computer Mac in cui siano stati installati Xcode v7.0 o versione successiva e Xamarin Studio Community. Per informazioni, vedere [Configurare e installare Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [Configurazione, installazione e verifiche per gli utenti Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-a-new-mobile-apps-back-end"></a>Creare un nuovo back-end di App per dispositivi mobili

Per creare un nuovo back-end di App per dispositivi mobili, seguire questa procedura:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

È stato configurato un back-end di App per dispositivi mobili che può essere usato dalle applicazioni client per dispositivi mobili. Scaricare quindi in progetto server per un semplice back-end di tipo elenco attività e pubblicarlo in Azure.

## <a name="configure-the-server-project"></a>Configurare il progetto server

Per configurare il progetto server per l'uso del back-end Node.js o .NET, seguire questa procedura:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Scaricare ed eseguire la soluzione Xamarin.Forms

È possibile scaricare la soluzione in due modi. Scaricarla in un computer Mac e aprirla in Xamarin Studio oppure scaricarla in un computer Windows e aprirla in Visual Studio usando un computer Mac connesso alla rete per la compilazione dell'app iOS. Per altre informazioni, vedere [Configurare e installare Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

In un computer Mac o Windows seguire questa procedura:

1. Accedere al [portale di Azure].

2. Nel pannello delle impostazioni dell'app per dispositivi mobili, fare clic su **Avvio rapido** (in Distribuzione) > **Xamarin.Forms**. Al passaggio 3 fare clic su **Crea una nuova app**, se l'opzione non è già selezionata.  Fare quindi clic sul pulsante **Download** .

   Questa azione scarica un progetto che contiene un'applicazione client connessa all'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

3. Estrarre il progetto scaricato e aprirlo in Xamarin Studio (Mac) o in Visual Studio (Windows).

   ![Progetto estratto in Xamarin Studio][9]

   ![Progetto estratto in Visual Studio][8]

## <a name="optional-run-the-ios-project"></a>(Facoltativo) Eseguire il progetto iOS
In questa sezione viene eseguito il progetto iOS per Xamarin per dispositivi iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

#### <a name="in-xamarin-studio"></a>In Xamarin Studio
1. Fare clic con il pulsante destro del mouse sul progetto iOS e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Start Debugging** (Avvia debug) dal menu **Run** (Esegui) per compilare il progetto e avviare l'app nell'emulatore iPhone.

#### <a name="in-visual-studio"></a>In Visual Studio
1. Fare clic con il pulsante destro del mouse sul progetto iOS e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Configuration Manager** dal menu **Compila**.

3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** accanto al progetto iOS.

4. Per compilare il progetto e avviare l'app nell'emulatore iPhone, premere il tasto **F5**.

   > [!NOTE]
   > In caso di problemi di compilazione del progetto, eseguire Gestione pacchetti NuGet ed effettuare l'aggiornamento alla versione più recente dei pacchetti per il supporto Xamarin. L'aggiornamento alle versioni più recenti per i progetti di avvio rapido potrebbe risultare lento.    
   >
   >

5. Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi selezionare il segno più (**+**).

    ![][10]

    Questa azione invia una richiesta POST al nuovo back-end di App per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end di App per dispositivi mobili e i dati vengono visualizzati nell'elenco.

    > [!NOTE]
    > Il codice che accede al back-end di App per dispositivi mobili è disponibile nel file TodoItemManager.cs C# del progetto di libreria di classi portabile della soluzione.
    >
    >

## <a name="optional-run-the-android-project"></a>(Facoltativo) Eseguire il progetto Android
In questa sezione viene eseguito il progetto Xamarin droid per Android. Se non si usano dispositivi Android, è possibile ignorare questa sezione.

#### <a name="in-xamarin-studio"></a>In Xamarin Studio

1. Fare clic con il pulsante destro del mouse sul progetto Android e quindi scegliere **Imposta come progetto di avvio**.

2. Per compilare il progetto e avviare l'app in un emulatore Android, dal menu **Run** (Esegui) scegliere **Start Debugging** (Avvia debug).

#### <a name="in-visual-studio"></a>In Visual Studio

1. Fare clic con il pulsante destro del mouse sul progetto Android (Droid) e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Configuration Manager** dal menu **Compila**.

3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** accanto al progetto Android.

4. Per compilare il progetto e avviare l'app in un emulatore Android, fare clic sul tasto **F5**.

   > [!NOTE]
   > In caso di problemi di compilazione del progetto, eseguire Gestione pacchetti NuGet ed effettuare l'aggiornamento alla versione più recente dei pacchetti per il supporto Xamarin. L'aggiornamento alle versioni più recenti per i progetti di avvio rapido potrebbe risultare lento.    
   >
   >

5. Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi selezionare il segno più (**+**).

    ![][11]
    
    Questa azione invia una richiesta POST al nuovo back-end di App per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end di App per dispositivi mobili e i dati vengono visualizzati nell'elenco.
    
    > [!NOTE]
    > Il codice che accede al back-end di App per dispositivi mobili è disponibile nel file TodoItemManager.cs C# del progetto di libreria di classi portabile della soluzione.
    >
    >

## <a name="optional-run-the-windows-project"></a>(Facoltativo) Eseguire il progetto Windows

In questa sezione viene eseguito il progetto Xamarin WinApp per dispositivi Windows. Se non si usano dispositivi Windows, è possibile ignorare questa sezione.

#### <a name="in-visual-studio"></a>In Visual Studio

1. Fare clic con il pulsante destro del mouse sul progetto Windows e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Configuration Manager** dal menu **Compila**.

3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** accanto al progetto Windows scelto.

4. Per compilare il progetto e avviare l'app nell'emulatore Windows, premere il tasto **F5**.

   > [!NOTE]
   > In caso di problemi di compilazione del progetto, eseguire Gestione pacchetti NuGet ed effettuare l'aggiornamento alla versione più recente dei pacchetti per il supporto Xamarin. L'aggiornamento alle versioni più recenti per i progetti di avvio rapido potrebbe risultare lento.    
   >
   >

5. Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi selezionare il segno più (**+**).

    Questa azione invia una richiesta POST al nuovo back-end di App per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end di App per dispositivi mobili e i dati vengono visualizzati nell'elenco.
    
    ![][12]
    
    > [!NOTE]
    > Il codice che accede al back-end di App per dispositivi mobili è disponibile nel file TodoItemManager.cs C# del progetto di libreria di classi portabile della soluzione.
    >
    >

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere l'autenticazione all'app Xamarin.Forms](app-service-mobile-xamarin-forms-get-started-users.md)  
  : informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Aggiungere notifiche push all'app](app-service-mobile-xamarin-forms-get-started-push.md)  
  Informazioni su come aggiungere il supporto per le notifiche push all'app e configurare il back-end dell'app per dispositivi mobili per usare Hub di notifica di Azure per l'invio di notifiche push.

* [Abilitare la sincronizzazione offline per l'app](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Informazioni su come aggiungere il supporto offline all'app usando il back-end di un'app per dispositivi mobili. La sincronizzazione offline consente di visualizzare, aggiungere o modificare i dati dell'app per dispositivi mobili, anche se non è presente alcuna connessione di rete.

* [Usare il client gestito per le App per dispositivi mobili](app-service-mobile-dotnet-how-to-use-client-library.md)  
  : informazioni su come usare l'SDK del client gestito nell'app Xamarin.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portale di Azure]: https://portal.azure.com/
