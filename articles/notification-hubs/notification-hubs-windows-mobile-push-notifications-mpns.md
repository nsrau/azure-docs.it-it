---
title: Eseguire il push di notifiche alle app Windows Phone con Hub di notifica di Azure | Microsoft Docs
description: Questa esercitazione descrive come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Windows Phone 8 o Windows Phone 8.1 Silverlight.
services: notification-hubs
documentationcenter: windows
keywords: notifica push, inviare notifiche push, push per windows phone
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: e055b51af19ad3958c0c9155490c598ed0f4a80e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235208"
---
# <a name="tutorial-push-notifications-to-windows-phone-apps-by-using-azure-notification-hubs"></a>Esercitazione: eseguire il push di notifiche alle app Windows Phone con Hub di notifica di Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Questa esercitazione descrive come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Windows Phone 8 o Windows Phone 8.1 Silverlight. Se si usa Windows Phone 8.1 (non Silverlight), fare riferimento alla versione [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) di questa esercitazione.

In questa esercitazione si creerà un'app per Windows Phone 8 vuota che riceve notifiche push tramite il Servizio di notifica Push di Microsoft. Dopo aver creato l'app è possibile usare l'hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app.

> [!NOTE]
> L’SDK Hub di notifica per Windows Phone [AZURE.NOTE] non supporta l'uso di Windows Push Notification Service (WNS) con app per Windows Phone 8.1 Silverlight. Per usare WNS (invece di MPNS) con le app di Windows Phone 8.1 Silverlight, completare la seguente [Esercitazione su Hub di notifica - Windows Phone Silverlight]che usa le API REST.

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Creare un hub di notifica
> * Creare un'applicazione di Windows Phone
> * Invio di prova di una notifica 

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- [Visual Studio 2015 Express con componenti di sviluppo per dispositivi mobili](https://www.visualstudio.com/vs/older-downloads/)

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub notifica relative ad app per Windows Phone 8.

## <a name="create-your-notification-hub"></a>Creare l'hub di notifica
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


### <a name="configure-windows-phone-mpns-settings"></a>Configurare le impostazioni di Windows Phone (MPNS)
1. Selezionare **Windows Phone (MPNS)** in **NOTIFICATION SETTINGS** (IMPOSTAZIONI DI NOTIFICA).
2. Selezionare **Enable authentication push** (Abilita push di autenticazione).
3. Sulla barra degli strumenti selezionare **Salva**.

    ![Portale di Azure - Abilita notifiche push non autenticate](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    L'hub viene creato e configurato per l'invio di notifiche autenticate per Windows Phone.

    > [!NOTE]
    > In questa esercitazione verrà usato il Servizio notifica Push Microsoft in modalità senza autenticazione. Tale modalità prevede restrizioni in relazione alle notifiche che è possibile inviare a ciascun canale. Hub di notifica supporta la [modalità di autenticazione con Servizio notifica push Microsoft](https://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) consentendo di caricare il certificato.

## <a name="create-a-windows-phone-application"></a>Creare un'applicazione di Windows Phone
In questa sezione viene creata un'applicazione Windows Phone che si registra con l'hub di notifica. 

1. In Visual Studio creare una nuova applicazione per Windows Phone 8. 
   
    ![Visual Studio - Nuovo progetto - App di Windows Phone][13]
   
    In Visual Studio 2013 Update 2 o versioni successive verrà invece creata un'applicazione per Windows Phone Silverlight.
   
    ![Visual Studio - Nuovo progetto - App vuota - Windows Phone Silverlight][11]
2. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet**.
3. Cercare `WindowsAzure.Messaging.Managed` , quindi fare clic su **Installa**e accettare le condizioni per l'utilizzo.
   
    ![Visual Studio - Gestione pacchetti NuGet][20]
4. Aprire il file App.xaml.cs e aggiungere le istruzioni `using` seguenti:
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Aggiungere il codice seguente all'inizio del metodo **Application_Launching** in App.xaml.cs:
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {

            var channel = HttpNotificationChannel.Find("MyPushChannel");
            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
            }
       
            channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
            {
                var hub = new NotificationHub("<hub name>", "<connection string>");
                var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
       
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
            });
        }
   
   > [!NOTE]
   > Il valore **MyPushChannel** è un indice che viene usato per cercare un canale esistente nella raccolta [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Se non è disponibile, creare una nuova voce con lo stesso nome.
   > 
   > 
   
    Inserire il nome dell'hub e la stringa di connessione denominata **DefaultListenSharedAccessSignature** e ottenuta nella sezione precedente.
    Questo codice consente di recuperare il valore di ChannelURI per l'app dal Servizio di notifica Push di Microsoft e quindi di registrarlo con l'hub di notifica. Garantisce inoltre che il valore di ChannelURI venga registrato nell'hub di notifica a ogni avvio dell'applicazione.
   
   > [!NOTE]
   > In questa esercitazione viene inviata una notifica di tipo avviso popup al dispositivo. Quando si invia una notifica di tipo riquadro, è invece necessario chiamare il metodo **BindToShellTile** sul canale. Per supportare sia la notifica di tipo riquadro che quella di tipo avviso popup, chiamare sia **BindToShellTile** che **BindToShellToast**.
   > 
   > 
6. In Esplora soluzioni espandere **Proprietà`WMAppManifest.xml`, aprire il file**, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità **ID__CAP__PUSH_NOTIFICATION** sia selezionata. L'app può ora ricevere le notifiche push. 
   
    ![Visual Studio - Funzionalità delle app di Windows Phone][14]    
7. Premere `F5` per eseguire l'app.
   
    Nell'app verrà visualizzato un messaggio di registrazione.
8. Chiudere l'app o passare alla home page. 
   
   > [!NOTE]
   > Per ricevere una notifica push di tipo avviso popup, l'applicazione non deve essere in esecuzione in primo piano.

## <a name="test-send-a-notification"></a>Invio di prova di una notifica 

1. Nel portale di Azure, passare alla scheda Overview (Informazioni generali).
2. Selezionare **Test Send** (Invio di prova).

    ![Pulsante Test Send (Invio di prova)](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. Nella finestra **Test Send** (Invio di prova) eseguire le operazioni seguenti:

    1. Per **Platforms** (Piattaforme) selezionare **Windows Phone**. 
    2. Per **Notification Type** (Tipo di notifica) selezionare **Toast** (Avviso popup). 
    3. Selezionare **Send** (Invia)
    4. Vedere il **risultato** nell'elenco nella parte inferiore della finestra. 

        ![Finestra Test Send (Invio di prova)](./media/notification-hubs-windows-phone-get-started/test-send-window.png)    
4. Nell'emulatore di Windows Phone o in Windows Phone verificare che il messaggio di notifica venga visualizzato. 

    ![Notifica su Windows Phone](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>Passaggi successivi
In questo semplice esempio le notifiche push sono state trasmesse a tutti i dispositivi Windows Phone 8. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi specifici, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)


<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Esercitazione su Hub di notifica - Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

