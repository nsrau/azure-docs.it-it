---
title: Aggiungere notifiche push all&quot;app Xamarin.iOS tramite il servizio app di Azure
description: Informazioni su come usare il servizio app di Azure per inviare notifiche push all&quot;app Xamarin.iOS.
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 185a32376dd1c40fd480e16d3ac7607ac798afae
ms.lasthandoff: 11/17/2016


---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Aggiungere notifiche push all'app Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione vengono aggiunte notifiche push al progetto [avvio rapido di Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md), in modo che una notifica push venga inviata al dispositivo a ogni inserimento di record.

Se non si usa il progetto server di avvio rapido scaricato, sarà necessario aggiungere il pacchetto di estensione di notifica push. Per altre informazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Prerequisiti
* Completare l'esercitazione della [guida introduttiva di Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) .
* Un dispositivo iOS fisico. Le notifiche push non sono supportate dal simulatore iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrare l'app per le notifiche push nel portale per sviluppatori Apple
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Configurare l'app per dispositivi mobili per l'invio di notifiche push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aggiornare il progetto server per l'invio di notifiche push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Configurare il progetto Xamarin.iOS
[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Aggiungere notifiche push all'app
1. In **QSTodoService** aggiungere la proprietà seguente in modo che l'oggetto **AppDelegate** possa acquisire il client per dispositivi mobili:
   
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }
2. Aggiungere l'istruzione `using` seguente all'inizio del file **AppDelegate.cs** .
   
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
3. In **AppDelegate** eseguire l'override dell'evento **FinishedLaunching**:
   
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());
   
            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
   
            return true;
        }
4. Nello stesso file, eseguire l'override dell'evento **RegisteredForRemoteNotifications** . In questo codice ci si sta registrando per una semplice notifica del modello che verrà inviata a tutte le piattaforme supportate dal server.
   
    Per ulteriori informazioni sui modelli con Hub di notifica, vedere [Modelli](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


1. Sovrascrivere quindi l'evento **DidReceivedRemoteNotification** :
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;
   
            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();
   
            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

L'app è ora aggiornata per il supporto delle notifiche push.

## <a name="a-nametestatest-push-notifications-in-your-app"></a><a name="test"></a>Testare le notifiche push nell'app
1. Scegliere **Run** (Esegui) per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.
   
   > [!NOTE]
   > È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.
   > 
   > 
2. Nell'app digitare un'attività e fare clic sull'icona con il segno più (**+**).
3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.
4. Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzata una notifica push.

L'esercitazione è stata completata.

<!-- Images. -->

<!-- URLs. -->




