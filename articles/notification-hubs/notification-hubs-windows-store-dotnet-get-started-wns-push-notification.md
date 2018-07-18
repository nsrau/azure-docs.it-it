---
title: Inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione della piattaforma UWP (Universal Windows Platform).
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c3bb170800508d5a546573850f445b2a8991ea8c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597745"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>Esercitazione: inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Questa esercitazione illustra come creare un hub di notifica per inviare notifiche push a un'app della piattaforma UWP (Universal Windows Platform). Si crea un'app di Windows Store vuota che riceve notifiche push tramite Windows Push Notification Service (WNS). Sarà quindi possibile usare l'hub di notifica per trasmettere notifiche push a tutti i dispositivi che eseguono l'app.

> [!NOTE]
> Il codice completo per questa esercitazione è disponibile in [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare un'app in Windows Store
> * Creare un hub di notifica
> * Creare un'app Windows di esempio
> * Inviare notifiche di prova


## <a name="prerequisites"></a>prerequisiti
- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) o versione successiva.
- [Strumenti per lo sviluppo di app UWP installati](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Account di Windows Store attivo

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative a Hub di notifica per le app UWP.

## <a name="create-an-app-in-windows-store"></a>Creare un'app in Windows Store
Per inviare notifiche push ad app UWP, associare l'app a Windows Store. Configurare quindi l'hub di notifica per l'integrazione con WNS.

1. Passare a [Windows Dev Center](https://dev.windows.com/overview), accedere con il proprio account Microsoft e quindi selezionare **Create a new app** (Crea una nuova app).

    ![Pulsante New app (Nuova app)](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
1. Digitare un nome per l'app e quindi selezionare **Reserve product name** (Riserva nome del prodotto). Verrà così creata una nuova registrazione a Windows Store per l'app.

    ![Archiviare il nome app](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
1. Espandere **App Management** (Gestione app), selezionare **WNS/MPNS**, selezionare **WNS/MPNS**, quindi selezionare il **sito Live Services**. Effettuare l'accesso all'account Microsoft. L'**Application Registration Portal** (Portale di registrazione delle applicazioni) si apre in una nuova scheda. In alternativa è possibile passare direttamente all'[Application Registration Portal](http://apps.dev.microsoft.com) (Portale di registrazione delle applicazioni) e selezionare il nome dell'applicazione per accedere a questa pagina.

    ![Pagina WNS MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
1.   Prendere nota della password dell'**Application Secret** (Segreto dell'applicazione) e del **Package security identifier (SID)** (ID di sicurezza (SID) del pacchetto).

        >[!WARNING]
        >Il segreto applicazione e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti né distribuirli con l'app.

## <a name="create-a-notification-hub"></a>Creare un hub di notifica
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


### <a name="configure-wns-settings-for-the-hub"></a>Configurare le impostazioni WNS per l'hub

1. Selezionare **Windows (WNS)** nella categoria **NOTIFICATION SETTINGS** (IMPOSTAZIONI DI NOTIFICA). 
2. Immettere i valori per **Package SID** (SID pacchetto) e **Security Key** (Chiave di sicurezza) annotati nella sezione precedente. 
3. Sulla barra degli strumenti selezionare **Salva**.

    ![Caselle SID pacchetto e Chiave di sicurezza](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

L'hub di notifica è ora configurato per l'uso di WNS. Sono disponibili le stringhe di connessione per la registrazione dell'app e l'invio di notifiche.

## <a name="create-a-sample-windows-app"></a>Creare un'app Windows di esempio
1. In Visual Studio selezionare **File**, **New** (Nuovo) e quindi **Project** (Progetto). 
2. Nella finestra di dialogo **Nuovo progetto** seguire questa procedura: 

    1. Espandere **Visual C#**.
    2. Selezionare **Windows Universal**. 
    3. Selezionare **Blank App (Universal Windows)** (App vuota (Windows universale)). 
    4. Immettere un **nome** per il progetto. 
    5. Selezionare **OK**. 

        ![Finestra di dialogo Nuovo progetto](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)
1. Accettare le impostazioni predefinite per le versioni di **destinazione** e **minima** della piattaforma e selezionare **OK**. 
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app di Windows Store, scegliere **Store** e quindi selezionare **Associa applicazione a Store**. Viene visualizzata la procedura guidata **Associa l'applicazione con Windows Store** .
3. Nella procedura guidata accedere con il proprio account Microsoft.
4. Selezionare l'app registrata nel passaggio 2, quindi **Avanti** e infine **Associa**. Le informazioni di registrazione a Windows Store necessarie verranno così aggiunte al manifesto dell'applicazione.
5. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet**. Verrà visualizzata la finestra **Gestisci pacchetti NuGet**.
6. Nella casella di ricerca immettere **WindowsAzure.Messaging.Managed**, selezionare **Installa** e accettare le condizioni per l'utilizzo.
   
    ![Finestra Gestisci pacchetti NuGet][20]
   
    Questa azione scarica, installa e aggiunge un riferimento alla libreria di Hub di notifica di Azure per Windows usando il [pacchetto NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).

3. Aprire il file di progetto App.xaml.cs e aggiungere le istruzioni `using` seguenti: 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. In App.xaml.cs, aggiungere alla classe **App** la definizione del metodo **InitNotificationsAsync** seguente:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    Questo codice recupera il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi lo registra con l'hub di notifica.
   
    >[!NOTE]
    >* Sostituire il segnaposto **hub name** con il nome dell'hub di notifica visualizzato nel portale di Azure. 
    >* Sostituire anche il segnaposto relativo alla stringa di connessione con la stringa di connessione **DefaultListenSharedAccessSignature** ottenuta nella pagina **Criteri di accesso** dell'hub di notifica in una sezione precedente.
   > 
   > 
5. All'inizio del gestore dell'evento **OnLaunched** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **InitNotificationsAsync**:
   
        InitNotificationsAsync();
   
    Questa azione garantisce la registrazione dell'URI di canale nell'hub di notifica a ogni avvio dell'applicazione.

6. Per eseguire l'app, premere **F5**. Verrà visualizzata una finestra di dialogo contenente la chiave di registrazione. Fare clic su **OK** per chiudere la finestra di dialogo. 

    ![Registrazione completata](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

L'app è ora pronta per ricevere notifiche di tipo avviso popup.

## <a name="send-test-notifications"></a>Inviare notifiche di prova
È possibile testare rapidamente la ricezione di notifiche nell'app inviando notifiche nel [portale di Azure](https://portal.azure.com/). 

1. Nel portale di Azure passare alla scheda Overview (Informazioni generali) e selezionare **Test Send** (Invio di prova) sulla barra degli strumenti.     

    ![Pulsante Test Send (Invio di prova)](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Nella finestra **Test Send** (Invio di prova) eseguire le operazioni seguenti: 
    1. Per **Platforms** (Piattaforme) selezionare **Windows**.
    2. Per **Notification Type** (Tipo di notifica) selezionare **Toast** (Avviso popup). 
    3. Selezionare **Send** (Invia). 
    
        ![Riquadro Invio di prova](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Visualizzare il risultato dell'operazione di invio nell'elenco dei **risultati** nella parte inferiore della finestra. È inoltre possibile visualizzare un messaggio di avviso. 

    ![Risultato dell'operazione di invio](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
1. Viene visualizzato il messaggio di notifica: **Test message** (Messaggio di prova) sul desktop. 

    ![Messaggio di notifica](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono state inviate notifiche di trasmissione a tutti i dispositivi Windows usando il portale o un'app console. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi specifici, passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)


<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
