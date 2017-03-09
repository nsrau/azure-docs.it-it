---
title: Introduzione alle app per dispositivi mobili in Xamarin.Forms
description: Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili di Azure per lo sviluppo per Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 6b8c5c2ac2e721c4d6f73c7c17f34eadc041e0c9
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-xamarinforms-app"></a>Creare un'app Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Xamarin.Forms mediante un back-end per app per dispositivi mobili di Azure. Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Xamarin.Forms *Todo list* che archivia dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative alle app per dispositivi mobili per Xamarin.Forms.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio con Xamarin. Per le istruzioni vedere [Configurazione e installazione di Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) . 
* Un computer Mac in cui siano stati installati Xcode v7.0 o versione successiva e Xamarin Studio Community. Vedere [Configurazione e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [Configurazione, installazione e verifiche per gli utenti Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di iscriversi per ottenere un account Azure, passare alla pagina in cui è possibile [provare il servizio app](https://azure.microsoft.com/try/app-service/mobile/)e creare immediatamente un'app per dispositivi mobili di base temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="create-a-new-azure-mobile-app-backend"></a>Creare un nuovo back-end dell'app per dispositivi mobili di Azure
Per creare un nuovo back-end dell'app per dispositivi mobili, attenersi alla procedura seguente.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

È stato eseguito il provisioning di un back-end dell'app per dispositivi mobili di Azure che può essere usato dalle applicazioni client per dispositivi mobili. Successivamente, scaricare un progetto server per un semplice back-end "todo list" e pubblicarlo in Azure.

## <a name="configure-the-server-project"></a>Configurare il progetto server
Seguire questa procedura per configurare il progetto server per l'uso del back-end .NET o Node.js.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Scaricare ed eseguire la soluzione Xamarin.Forms
Sono disponibili un paio di opzioni. È possibile scaricare la soluzione in un computer Mac e aprirla in Xamarin Studio oppure è possibile scaricarla in un computer Windows e aprirla in Visual Studio usando un Mac collegato alla rete per creare l'app per iOS. Se sono necessarie istruzioni più dettagliate sugli scenari di configurazione di Xamarin, vedere [Configurazione e installazione](https://msdn.microsoft.com/library/mt613162.aspx) per Visual Studio e Xamarin.

Procedura:

1. Nel computer Mac o Windows passare al [portale di Azure] in una finestra del browser.
2. Nel pannello delle impostazioni dell'app per dispositivi mobili fare clic su **Introduzione** (in Dispositivi mobili) > **Xamarin.Forms**. Al passaggio 3 fare clic su **Crea una nuova app**, se l'opzione non è già selezionata.  Fare quindi clic sul pulsante **Download** .
   
   Verrà scaricato un progetto che contiene un'applicazione client connessa all'app per dispositivi mobili. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.
3. Estrarre il progetto scaricato e aprirlo in Xamarin Studio o in Visual Studio.
   
   ![][9]
   
   ![][8]

## <a name="optional-run-the-ios-project"></a>(Facoltativo) Eseguire il progetto iOS
Questa sezione illustra l'esecuzione del progetto Xamarin iOS per dispositivi iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

#### <a name="in-xamarin-studio"></a>In Xamarin Studio
1. Fare clic con il pulsante destro del mouse sul progetto iOS e quindi scegliere **Set As Startup Project**(Imposta come progetto di avvio).
2. Scegliere **Start Debugging** (Avvia debug) dal menu **Run** (Esegui) per compilare il progetto e avviare l'app nell'emulatore iPhone.

#### <a name="in-visual-studio"></a>In Visual Studio
1. Fare clic con il pulsante destro del mouse sul progetto iOS e quindi scegliere **Imposta come progetto di avvio**.
2. Scegliere **Configuration Manager** dal menu **Compila**.
3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** del progetto iOS.
4. Premere **F5** per compilare il progetto e avviare l'app nell'emulatore iPhone.
   
   > [!NOTE]
   > In caso di problemi di compilazione, eseguire Gestione pacchetti NuGet ed effettuare l'aggiornamento alla versione più recente dei pacchetti per il supporto Xamarin. A volte i progetti di Avvio rapido possono accumulare ritardo nell'aggiornamento alla versione più recente.    
   > 
   > 

Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi fare clic sul pulsante **+**.

![][10]

Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end per app mobili e i dati vengono visualizzati nell'elenco.

> [!NOTE]
> Il codice che accede al back-end dell'app per dispositivi mobili è disponibile nel file TodoItemManager.cs C# del progetto di libreria di classi portabile della soluzione.
> 
> 

## <a name="optional-run-the-android-project"></a>(Facoltativo) Eseguire il progetto Android
Questa sezione illustra l'esecuzione del progetto Xamarin droid per dispositivi Android. Se non si usano dispositivi Android, è possibile ignorare questa sezione.

#### <a name="in-xamarin-studio"></a>In Xamarin Studio
1. Fare clic con il pulsante destro del mouse sul progetto Android e quindi scegliere **Set As Startup Project**(Imposta come progetto di avvio).
2. Scegliere **Start Debugging** (Avvia debug) dal menu **Run** (Esegui) per compilare il progetto e avviare l'app nell'emulatore Android.

#### <a name="in-visual-studio"></a>In Visual Studio
1. Fare clic con il pulsante destro del mouse sul progetto Android (Droid) e quindi scegliere **Imposta come progetto di avvio**.
2. Scegliere **Configuration Manager** dal menu **Compila**.
3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** del progetto Android.
4. Premere **F5** per compilare il progetto e avviare l'app nell'emulatore Android.
   
   > [!NOTE]
   > In caso di problemi di compilazione, eseguire Gestione pacchetti NuGet ed effettuare l'aggiornamento alla versione più recente dei pacchetti per il supporto Xamarin. A volte i progetti di Avvio rapido possono accumulare ritardo nell'aggiornamento alla versione più recente.    
   > 
   > 

Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi fare clic sul pulsante **+**.

![][11]

Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end per app mobili e i dati vengono visualizzati nell'elenco.

> [!NOTE]
> Il codice che accede al back-end dell'app per dispositivi mobili è disponibile nel file TodoItemManager.cs C# del progetto di libreria di classi portabile della soluzione.
> 
> 

## <a name="optional-run-the-windows-project"></a>(Facoltativo) Eseguire il progetto Windows
Questa sezione illustra l'esecuzione del progetto Xamarin WinApp per dispositivi Windows. Se non si usano dispositivi Windows, è possibile ignorare questa sezione.

#### <a name="in-visual-studio"></a>In Visual Studio
1. Fare clic con il pulsante destro del mouse sul progetto Windows e quindi scegliere **Imposta come progetto di avvio**.
2. Scegliere **Configuration Manager** dal menu **Compila**.
3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** del progetto Windows scelto.
4. Premere **F5** per compilare il progetto e avviare l'app nell'emulatore Windows.
   
   > [!NOTE]
   > In caso di problemi di compilazione, eseguire Gestione pacchetti NuGet ed effettuare l'aggiornamento alla versione più recente dei pacchetti per il supporto Xamarin. A volte i progetti di Avvio rapido possono accumulare ritardo nell'aggiornamento alla versione più recente.    
   > 
   > 

Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi fare clic sul pulsante **+**.

Verrà inviata una richiesta POST al nuovo back-end dell'app per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end per app mobili e i dati vengono visualizzati nell'elenco.

![][12]

> [!NOTE]
> Il codice che accede al back-end dell'app per dispositivi mobili è disponibile nel file TodoItemManager.cs C# del progetto di libreria di classi portabile della soluzione.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere l'autenticazione all'app Xamarin.Forms](app-service-mobile-xamarin-forms-get-started-users.md)  
  : informazioni sull'autenticazione degli utenti dell'app con un provider di identità.
* [Aggiungere notifiche push all'app Xamarin.Forms](app-service-mobile-xamarin-forms-get-started-push.md)  
  : informazioni su come aggiungere il supporto per le notifiche push all'app e configurare il back-end dell'app per dispositivi mobili per usare Hub di notifica di Azure per l'invio di notifiche push.
* [Abilitare la sincronizzazione offline per l'app per dispositivi mobili Xamarin.Forms](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  : informazioni su come aggiungere il supporto offline all'app usando il back-end di un'app per dispositivi mobili. La sincronizzazione offline consente agli utenti finali di interagire con un'app&mdash;visualizzando, aggiungendo e modificando i dati&mdash;anche se non è disponibile una connessione di rete.
* [Come usare il client gestito per App per dispositivi mobili di Azure](app-service-mobile-dotnet-how-to-use-client-library.md)  
  : informazioni su come usare l'SDK del client gestito nell'app Xamarin. 

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


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


