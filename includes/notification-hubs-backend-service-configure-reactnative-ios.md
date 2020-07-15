---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: e67f53e26f2081e1aa12f7c0e6a219c240f5027a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060442"
---
### <a name="configure-required-ios-packages"></a>Configurare i pacchetti iOS necessari

Il pacchetto viene [collegato automaticamente](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) quando si compila l'app. È sufficiente installare i pod nativi:

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Configurare Info.plist e Entitlements.plist

1. Passare alla cartella "PushDemo/ios" e aprire l'area di lavoro "PushDemo.xcworkspace". Selezionare il progetto principale "PushDemo" e quindi la scheda "Signing & Capabilities" (Firma e funzionalità).

1. Aggiornare l'identificatore del bundle in modo che corrisponda al valore usato nel profilo di provisioning.

1. Aggiungere due nuove funzionalità facendo clic sul pulsante "+":

    - Funzionalità per modalità in background e notifiche remote con tick.
    - Funzionalità per notifiche push

### <a name="handle-push-notifications-for-ios"></a>Gestire le notifiche push per iOS

1. Aprire "AppDelegate.h" e aggiungere l'importazione seguente:

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. Aggiornare l'elenco dei protocolli, supportato da "AppDelegate", aggiungendo `UNUserNotificationCenterDelegate`:

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. Aprire "AppDelegate.m" e configurare tutti i callback iOS necessari:

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
