---
title: Inviare notifiche push a Xamarin iOS con Hub di notifica di Azure | Microsoft Docs
description: In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione Xamarin.iOS.
services: notification-hubs
keywords: notifiche push di ios,messaggi push,notifiche push,messaggio push
documentationcenter: xamarin
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: 38a4924cd6bee2f6e2860320d51cfb61aa6fe1bb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387697"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Esercitazione: Inviare notifiche push alle app Xamarin.iOS con Hub di notifica di Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per iOS. Creare un'app Xamarin.iOS vuota che riceve notifiche push mediante [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Al termine è possibile usare l'hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][GitHub].

In questa esercitazione si crea/aggiorna il codice per eseguire le attività seguenti:

> [!div class="checklist"]
> * Generare il file della richiesta di firma del certificato
> * Registrare l'app per le notifiche push
> * Creare un profilo di provisioning per l'app
> * Configurare l'hub di notifica per l'invio di notifiche push di iOS
> * Inviare notifiche push di prova

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Ultima versione di [Xcode][Install Xcode]
* Dispositivo compatibile con iOS 10 o versione successiva
* [Apple Developer Program](https://developer.apple.com/programs/) .
* [Visual Studio per Mac]
  
  > [!NOTE]
  > Considerati i requisiti di configurazione delle notifiche push di iOS, è necessario distribuire e testare l'applicazione di esempio in un dispositivo iOS, iPhone o iPad, anziché in un simulatore.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>Connettere l'app all'hub di notifica

### <a name="create-a-new-project"></a>Creare un nuovo progetto

1. In Visual Studio creare un nuovo progetto iOS e selezionare il modello **App visualizzazione singola**, quindi fare clic su **Avanti**

     ![Visual Studio - Selezionare il tipo di applicazione][31]

2. Immettere il nome dell'app e l'identificatore dell'organizzazione, quindi fare clic su **Avanti** e su **Crea**

3. Nella visualizzazione della soluzione fare doppio clic su *Info.plist* e in **Identità** assicurarsi che l'identificatore del bundle corrisponda a quello usato per la creazione del profilo di provisioning. In **Firma** accertarsi che sotto **Team** sia selezionato il proprio account per sviluppatore, che sia selezionato "Automatically manage signing" (Gestisci firma automaticamente) e che vengano automaticamente selezionati il certificato di firma e il profilo di provisioning.

    ![Visual Studio - Configurazione di un'app iOS][32]

4. Nella visualizzazione della soluzione fare doppio clic su `Entitlements.plist` e verificare che l'opzione **Abilita notifiche push** sia selezionata.

    ![Visual Studio - Configurazione di entitlement iOS][33]

5. Aggiungere il pacchetto di messaggistica di Azure. Nella visualizzazione della soluzione fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Aggiungi pacchetti NuGet**. Cercare **Xamarin.Azure.NotificationHubs.iOS** e aggiungere il pacchetto al progetto.

6. Aggiungere un nuovo file alla classe, assegnargli il nome `Constants.cs`, aggiungere le variabili seguenti e sostituire i segnaposto dei valori letterali stringa con i valori `hubname` e `DefaultListenSharedAccessSignature` annotati in precedenza.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. In `AppDelegate.cs` aggiungere l'istruzione using seguente:

    ```csharp
    using WindowsAzure.Messaging;
    using UserNotifications
    ```

8. Dichiarare un'istanza di `SBNotificationHub`:

    ```csharp
    private SBNotificationHub Hub { get; set; }
    ```

9. In `AppDelegate.cs` aggiornare `FinishedLaunching()` in base al codice seguente:

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert | UNAuthorizationOptions.Badge | UNAuthorizationOptions.Sound,
                                                                    (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
        } else if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                    new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        } else {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
        }

        return true;
    }
    ```

10. In `AppDelegate.cs` eseguire l'override del metodo `RegisteredForRemoteNotifications()`:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        Hub = new SBNotificationHub(Constants.ListenConnectionString, Constants.NotificationHubName);

        Hub.UnregisterAllAsync (deviceToken, (error) => {
            if (error != null)
            {
                System.Diagnostics.Debug.WriteLine("Error calling Unregister: {0}", error.ToString());
                return;
            }

            NSSet tags = null; // create tags if you want
            Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                if (errorCallback != null)
                    System.Diagnostics.Debug.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
            });
        });
    }
    ```

11. In `AppDelegate.cs` eseguire l'override del metodo `ReceivedRemoteNotification()`:

    ```csharp
    public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
    {
        ProcessNotification(userInfo, false);
    }
    ```

12. In `AppDelegate.cs` creare il metodo `ProcessNotification()`:

    ```csharp
    void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
    {
        // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
        if (null != options && options.ContainsKey(new NSString("aps")))
        {
            //Get the aps dictionary
            NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;

            //Extract the alert text
            // NOTE: If you're using the simple alert by just specifying
            // "  aps:{alert:"alert msg here"}  ", this will work fine.
            // But if you're using a complex alert with Localization keys, etc.,
            // your "alert" object from the aps dictionary will be another NSDictionary.
            // Basically the JSON gets dumped right into a NSDictionary,
            // so keep that in mind.
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //If this came from the ReceivedRemoteNotification while the app was running,
            // we of course need to manually process things like the sound, badge, and alert.
            if (!fromFinishedLaunching)
            {
                //Manually show an alert
                if (!string.IsNullOrEmpty(alert))
                {
                    UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                    avAlert.Show();
                }
            }
        }
    }
    ```

    > [!NOTE]
    > È possibile scegliere di eseguire l'override di `FailedToRegisterForRemoteNotifications()` per gestire situazioni come l'assenza di una connessione di rete. Questo risulta particolarmente importante nei casi in cui l'utente può avviare l'applicazione in modalità offline, ad esempio in aereo, e si vogliono gestire scenari di messaggistica push specifici per l'applicazione.

13. Eseguire l'app sul dispositivo.

## <a name="send-test-push-notifications"></a>Inviare notifiche push di prova

È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione *Invio di prova* nel [portale di Azure]. Verrà inviata una notifica push di prova al dispositivo.

![Portale di Azure - Invio di prova][30]

Le notifiche push vengono in genere inviate in un servizio back-end come App per dispositivi mobili o ASP.NET usando una libreria compatibile. Se non è disponibile una libreria per il back-end è anche possibile usare direttamente l'API REST per inviare messaggi di notifica.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione le notifiche sono state trasmesse a tutti i dispositivi iOS registrati con il back-end. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi iOS specifici, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio per Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Portale di Azure]: https://portal.azure.com
