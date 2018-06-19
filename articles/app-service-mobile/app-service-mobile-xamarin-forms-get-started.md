---
title: Introduzione alle app per dispositivi mobili in Xamarin.Forms
description: Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili per lo sviluppo per Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/25/2018
ms.author: crdun
ms.openlocfilehash: b2dafbcf0e41e7387157590e145f74430686321c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597672"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Creare un'app Xamarin.Forms con Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Xamarin.Forms mediante la funzionalità App per dispositivi mobili del Servizio app di Azure come back-end. Vengono creati un nuovo back-end di app per dispositivi mobili e una semplice app Xamarin.Forms di tipo elenco attività che archivia dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative alle app per dispositivi mobili per Xamarin.Forms.

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per altre informazioni, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* Strumenti di Visual Studio per Xamarin, in Visual Studio 2017 o Visual Studio per Mac. Vedere la [pagina di installazione di Xamarin][Install Xamarin] per le istruzioni.

* (facoltativo) Per compilare un'app iOS, è necessario un Mac con Xcode 9.0 o versione successiva. Per sviluppare app iOS è possibile usare Visual Studio per Mac oppure Visual Studio 2017 (purché il Mac sia disponibile nella rete).

## <a name="create-a-new-mobile-apps-back-end"></a>Creare un nuovo back-end di App per dispositivi mobili

Per creare un nuovo back-end di App per dispositivi mobili, seguire questa procedura:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

È stato configurato un back-end di App per dispositivi mobili che può essere usato dalle applicazioni per dispositivi mobili. Scaricare quindi in progetto server per un semplice back-end di tipo elenco attività e pubblicarlo in Azure.

## <a name="configure-the-server-project"></a>Configurare il progetto server

Per configurare il progetto server per l'uso del back-end Node.js o .NET, seguire questa procedura:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Scaricare ed eseguire la soluzione Xamarin.Forms

Per aprire la soluzione sono necessari gli Strumenti di Visual Studio per Xamarin. Vedere le [istruzioni di installazione di Xamarin][Install Xamarin]. Se gli strumenti sono già installati, seguire questa procedura per scaricare e aprire la soluzione:

### <a name="visual-studio"></a>Visual Studio

1. Accedere al [portale di Azure].

2. Nel pannello delle impostazioni dell'app per dispositivi mobili, fare clic su **Avvio rapido** (in Distribuzione) > **Xamarin.Forms**. Al passaggio 3 fare clic su **Crea una nuova app**, se l'opzione non è già selezionata.  Fare quindi clic sul pulsante **Download** .

   Questa azione scarica un progetto che contiene un'applicazione client connessa all'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

3. Estrarre il progetto scaricato e aprirlo in Visual Studio 2017.

   ![Progetto estratto in Visual Studio][8]

4. Seguire le istruzioni riportate di seguito per eseguire i progetti Android o Windows e, se nella rete è disponibile un computer Mac, il progetto iOS.

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Accedere al [portale di Azure].

2. Nel pannello delle impostazioni dell'app per dispositivi mobili, fare clic su **Avvio rapido** (in Distribuzione) > **Xamarin.Forms**. Al passaggio 3 fare clic su **Crea una nuova app**, se l'opzione non è già selezionata.  Fare quindi clic sul pulsante **Download** .

   Questa azione scarica un progetto che contiene un'applicazione client connessa all'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

3. Estrarre il progetto scaricato e aprirlo in Visual Studio per Mac.

   ![Progetto estratto in Visual Studio per Mac][9]

4. Seguire le istruzioni riportate di seguito per eseguire i progetti Android o iOS.



## <a name="optional-run-the-android-project"></a>(Facoltativo) Eseguire il progetto Android

In questa sezione viene eseguito il progetto Xamarin.Android. Se non si usano dispositivi Android, è possibile ignorare questa sezione.

### <a name="visual-studio"></a>Visual Studio

1. Fare clic con il pulsante destro del mouse sul progetto Android (Droid) e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Configuration Manager** dal menu **Compila**.

3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** accanto al progetto Android e verificare che la casella di controllo **Compila** sia selezionata per il progetto di codice condiviso.

4. Per compilare il progetto e avviare l'app in un emulatore Android, premere **F5** o fare clic sul pulsante **Avvia**.

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Fare clic con il pulsante destro del mouse sul progetto Android e quindi scegliere **Imposta come progetto di avvio**.

2. Per compilare il progetto e avviare l'app in un emulatore Android, scegliere **Avvia debug** dal menu **Esegui**.



Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi selezionare il segno più (**+**).

![App elenco attività Android][11]

Questa azione invia una richiesta POST al nuovo back-end di App per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end di App per dispositivi mobili e i dati vengono visualizzati nell'elenco.

> [!NOTE]
> Il codice che accede al back-end di App per dispositivi mobili è disponibile nel file C# **TodoItemManager.cs** del progetto di codice condiviso della soluzione.
>

## <a name="optional-run-the-ios-project"></a>(Facoltativo) Eseguire il progetto iOS

In questa sezione viene eseguito il progetto Xamarin.iOS per dispositivi iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

### <a name="visual-studio"></a>Visual Studio

1. Fare clic con il pulsante destro del mouse sul progetto iOS e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Configuration Manager** dal menu **Compila**.

3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** accanto al progetto iOS e verificare che la casella di controllo **Compila** sia selezionata per il progetto di codice condiviso.

4. Per compilare il progetto e avviare l'app nell'emulatore iPhone, premere il tasto **F5**.

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Fare clic con il pulsante destro del mouse sul progetto iOS e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Start Debugging** (Avvia debug) dal menu **Run** (Esegui) per compilare il progetto e avviare l'app nell'emulatore iPhone.



Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi selezionare il segno più (**+**).

![App elenco attività iOS][10]

Questa azione invia una richiesta POST al nuovo back-end di App per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end di App per dispositivi mobili e i dati vengono visualizzati nell'elenco.

> [!NOTE]
> Il codice che accede al back-end di App per dispositivi mobili è disponibile nel file C# **TodoItemManager.cs** del progetto di codice condiviso della soluzione.
>

## <a name="optional-run-the-windows-project"></a>(Facoltativo) Eseguire il progetto Windows

In questa sezione viene eseguito il progetto Xamarin.Forms della piattaforma UWP (Universal Windows Platform) per dispositivi Windows. Se non si usano dispositivi Windows, è possibile ignorare questa sezione.

### <a name="visual-studio"></a>Visual Studio

1. Fare clic con il pulsante destro del mouse su un progetto della piattaforma UWP e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Configuration Manager** dal menu **Compila**.

3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** accanto al progetto Windows scelto e verificare che la casella di controllo **Compila** sia selezionata per il progetto di codice condiviso.

4. Per compilare il progetto e avviare l'app in un emulatore Android, premere **F5** o fare clic sul pulsante **Avvia** (che dovrebbe indicare **Computer locale**).

> [!NOTE]
> Non è possibile eseguire il progetto Windows su macOS.



Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi selezionare il segno più (**+**).

Questa azione invia una richiesta POST al nuovo back-end di App per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end di App per dispositivi mobili e i dati vengono visualizzati nell'elenco.

![App elenco attività UWP][12]

> [!NOTE]
> Il codice che accede al back-end di App per dispositivi mobili è disponibile nel file C# **TodoItemManager.cs** del progetto di libreria di classi portabile della soluzione.
>

## <a name="troubleshooting"></a>Risoluzione dei problemi

In caso di problemi di compilazione della soluzione, eseguire Gestione pacchetti NuGet ed effettuare l'aggiornamento alla versione più recente di Xamarin.Forms e nel progetto Android aggiornare i pacchetti per il supporto Xamarin.Android. I progetti di avvio rapido potrebbero non includere sempre le versioni più recenti.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere l'autenticazione all'app](app-service-mobile-xamarin-forms-get-started-users.md) Informazioni su come autenticare gli utenti dell'app con un provider di identità.

* [Aggiungere notifiche push all'app](app-service-mobile-xamarin-forms-get-started-push.md) Informazioni su come aggiungere il supporto per le notifiche push all'app e configurare il back-end di App per dispositivi mobili in modo da usare Hub di notifica di Azure per l'invio di notifiche push.

* [Abilitare la sincronizzazione offline per l'app](app-service-mobile-xamarin-forms-get-started-offline-data.md) Informazioni su come aggiungere il supporto offline per l'app tramite un back-end di App per dispositivi mobili. La sincronizzazione offline consente di visualizzare, aggiungere o modificare i dati dell'app per dispositivi mobili, anche se non è presente alcuna connessione di rete.

* [Usare il client gestito per le app per dispositivi mobili](app-service-mobile-dotnet-how-to-use-client-library.md) Informazioni su come usare l'SDK del client gestito nell'app Xamarin.

* [Uso di altri servizi Azure con Xamarin.Forms](https://docs.microsoft.com/xamarin/xamarin-forms/data-cloud/) Informazioni sull'aggiunta di altre funzionalità di Azure, come la ricerca, l'archiviazione e i servizi cognitivi, alle app Xamarin.Forms.

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
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portale di Azure]: https://portal.azure.com/
