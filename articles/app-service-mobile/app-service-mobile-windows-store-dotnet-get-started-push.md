---
title: Aggiungere notifiche push all'app UWP (Universal Windows Platform) | Documentazione Microsoft
description: Informazioni su come usare le app per dispositivi mobili del servizio app di Azure e gli hub di notifica di Azure per inviare notifiche push all'app UWP (Universal Windows Platform).
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 5e71ecc6539179efdcfa6bfc4567bddaa96a59d9
ms.contentlocale: it-it
ms.lasthandoff: 03/01/2017

---
# <a name="add-push-notifications-to-your-windows-app"></a>Aggiungere notifiche push all'app di Windows
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overview
In questa esercitazione vengono aggiunte notifiche push al progetto [avvio rapido di Windows](app-service-mobile-windows-store-dotnet-get-started.md), in modo che una notifica push venga inviata al dispositivo a ogni inserimento di record.

Se non si usa il progetto server di avvio rapido scaricato, sarà necessario aggiungere il pacchetto di estensione di notifica push. Per altre informazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="configure-hub"></a>Configurare un hub di notifica
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrare l'app per le notifiche push
È necessario inviare l'app a Windows Store, quindi configurare il progetto server in modo che venga eseguita l'integrazione con Servizi notifica Windows per l'invio di notifiche push.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto dell'app UWP, quindi scegliere **Store** > **Associa applicazione a Store**.

    ![Associa l’app con Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. Nella procedura guidata fare clic su **Avanti**, effettuare l'accesso con l'account Microsoft, immettere un nome per l'app in **Riserva un nuovo nome dell'app** e quindi fare clic su **Reserva**.
3. Dopo la creazione della registrazione dell'app, selezionare il nuovo nome dell'app, fare clic su **Avanti** e quindi su **Associa**. Le informazioni di registrazione a Windows Store necessarie verranno aggiunte al manifesto dell'applicazione.  
4. Passare a [Windows Dev Center](https://dev.windows.com/en-us/overview), accedere con l'account Microsoft, fare clic sulla nuova registrazione di app in **App personali**, quindi espandere **Servizi** > **Notifiche push**.
5. Nella pagina **Notifiche push** fare clic su **Sito di servizi Live** in **Servizi mobili di Microsoft Azure**.
6. Nella pagina di registrazione prendere nota del valore in **Segreti applicazione** e **SID pacchetto**. Questi valori verranno utilizzati successivamente per configurare il back-end dell'app per dispositivi mobili.

    ![Associa l’app con Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti né distribuirli con l'app. L' **ID applicazione** viene usato con il segreto per configurare l'autenticazione dell'account Microsoft.
   >
   >

## <a name="configure-the-backend-to-send-push-notifications"></a>Configurare il back-end per l'invio di notifiche push
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Aggiornare il server per l'invio di notifiche push
Usare la procedura corrispondente al tipo di progetto di back-end in corso:&mdash; [back-end .NET](#dotnet) o [back-end Node.js](#nodejs).

### <a name="dotnet"></a>Progetto di back-end .NET
1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server, scegliere **Gestisci pacchetti NuGet**, cercare Microsoft.Azure.NotificationHubs e quindi fare clic su **Installa**. Verrà installata la libreria client dell'Hub di notifica.
2. Espandere **Controller**, aprire TodoItemController.cs e aggiungere le istruzioni using seguenti:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. Nel metodo **PostTodoItem** aggiungere il codice seguente dopo la chiamata a **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Questo codice indica all'hub di notifica di inviare una notifica push dopo l'inserimento di un nuovo elemento.
4. Pubblicare di nuovo il progetto server.

### <a name="nodejs"></a>Progetto di back-end Node.js
1. [Scaricare il progetto di avvio rapido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) (se non è ancora stato scaricato) oppure usare l'[editor online del portale di Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Sostituire il codice esistente nel file todoitem.js file con il codice seguente:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;

    Ogni volta che viene inserito un nuovo elemento Todo, viene inviata una notifica popup WNS contenente l'elemento item.text.
3. Quando si modifica il file nel computer locale, ripubblicare il progetto server.

## <a id="update-app"></a>Aggiungere notifiche push all'app
L'app dovrà quindi registrarsi per le notifiche push all'avvio. Se l'autenticazione è già stata abilitata, verificare che l'utente esegua l'accesso prima di registrarsi per le notifiche push.

1. Aprire il file di progetto **App.xaml.cs** e aggiungere le istruzioni `using` seguenti:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
2. Nello stesso file aggiungere la seguente definizione del metodo **InitNotificationsAsync** alla classe **App**:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo con l'app per dispositivi mobili del servizio app.
3. All'inizio del gestore eventi **OnLaunched** nel file **App.xaml.cs** aggiungere il modificatore **async** alla definizione del metodo e quindi aggiungere la seguente chiamata al nuovo metodo **InitNotificationsAsync**, come illustrato di seguito:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    In tal modo si garantirà che il valore di ChannelURI temporaneo venga registrato ogni volta che l'applicazione viene avviata.
4. Ricompilare il progetto dell'app UWP. L'app è ora pronta per ricevere notifiche di tipo avviso popup.

## <a id="test"></a>Testare le notifiche push nell'app
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Passaggi successivi
Altre informazioni sulle notifiche push:

* [Come usare il client gestito per App per dispositivi mobili di Azure](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications)  
  I modelli offrono flessibilità per inviare notifiche push multipiattaforma e push localizzati. Informazioni su come registrare i modelli.
* [Diagnose push notification issues](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  (Diagnosticare i problemi relativi alle notifiche push) Esistono varie ragioni per cui le notifiche possono essere eliminate o non giungere ai dispositivi. Questo argomento illustra come analizzare e capire la causa radice degli errori relativi alle notifiche push.

È consigliabile proseguire con una delle esercitazioni seguenti:

* [Aggiungere l'autenticazione all'app Windows](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.
* [Abilitare la sincronizzazione offline per l'app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  : informazioni su come aggiungere il supporto offline all'app usando il back-end di un'app per dispositivi mobili. La sincronizzazione offline consente agli utenti finali di interagire con un'app&mdash;visualizzando, aggiungendo e modificando i dati&mdash;anche se non è disponibile una connessione di rete.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

