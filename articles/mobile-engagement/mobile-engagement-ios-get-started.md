---
title: Introduzione ad Azure Mobile Engagement per iOS in Objective C | Documentazione Microsoft
description: "Informazioni sull'uso di Azure Mobile Engagement con le funzionalità di analisi e notifiche push per le app per iOS."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 913a60df4ce7c431b1c260135785972aac00c69d
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Introduzione a Azure Mobile Engagement per app per iOS in Objective C
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento descrive come usare Azure Mobile Engagement per comprendere l'utilizzo delle app e inviare notifiche push a utenti segmentati di un'applicazione iOS.
In questa esercitazione si creerà un'app per iOS vuota che raccoglie dati di base e riceve notifiche push tramite Apple Push Notification System (APNS).

Per completare questa esercitazione, è necessario disporre di:

* XCode 8, che è possibile installare da MAC App Store
* [Mobile Engagement SDK per iOS]

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Mobile Engagement relative ad app per iOS.

> [!NOTE]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).
>
>

## <a id="setup-azme"></a>Configurare Mobile Engagement per l'app iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement
Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione completa relativa all'integrazione è disponibile nella [integrazione di Mobile Engagement iOS SDK](mobile-engagement-ios-sdk-overview.md)

Si creerà un'app di base con Xcode per illustrare l'integrazione.

### <a name="create-a-new-ios-project"></a>Creare un nuovo progetto iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connettere l'app al back-end di Mobile Engagement
1. Scaricare [Mobile Engagement SDK per iOS].
2. Estrarre il file .tar.gz in una cartella nel computer.
3. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi file**.

    ![][1]

4. Passare alla cartella in cui è stato estratto l'SDK, selezionare la cartella `EngagementSDK`, fare clic su **Options** (Opzioni) nell'angolo in basso a sinistra e verificare che la casella di controllo **Copy items if needed** (Copia elementi se necessario) e la casella di controllo della destinazione siano selezionate, quindi fare clic su **OK**.

    ![][2]

5. Aprire la scheda **Build Phases** (Crea fasi) e nel menu **Link Binary With Libraries** (Collega binario con librerie) aggiungere i framework come illustrato di seguito:

    ![][3]

6. Tornare al portale di Azure nella pagina **Informazioni di connessione** dell'app e copiare la stringa di connessione.

    ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)
7. Nel file **AppDelegate.m** aggiungere la seguente riga di codice.

    ```obj-c
    #import "EngagementAgent.h"
    ```
8. A questo punto, incollare la stringa di connessione nel delegato `didFinishLaunchingWithOptions` .

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
    }
    ```

9. `setTestLogEnabled` è un'istruzione facoltativa che abilita i log dell'SDK per consentire l'identificazione dei problemi.

## <a id="monitor"></a>Abilitare il monitoraggio in tempo reale
Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement.

1. Aprire il file **ViewController.h** e importare **EngagementViewController.h**:

    ```obj-c
    #import "EngagementViewController.h"
    ```

2. Sostituire ora la superclasse dell'interfaccia **ViewController** con `EngagementViewController`:
 
    ```obj-c
   @interface ViewController : EngagementViewController
   ```

## <a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app
Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto delle campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement.
Le sezioni seguenti consentono di configurare l'app per la ricezione.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Abilitare l'app per la ricezione delle notifiche push Silent
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Aggiungere la libreria Reach al progetto
1. Fare clic con il pulsante destro sul progetto.
2. Selezionare **Aggiungi file**.
3. Passare alla cartella in cui è stato estratto l'SDK.
4. Selezionare la cartella `EngagementReach` .
5. Fare clic su **Aggiungi**.

### <a name="modify-your-application-delegate"></a>Modificare il delegato dell'applicazione
1. Nel file **AppDeletegate.m** importare il modulo Engagement Reach.

    ```obj-c
    #import "AEReachModule.h"
    #import <UserNotifications/UserNotifications.h>
    ```

2. Nel metodo `application:didFinishLaunchingWithOptions` creare un modulo Reach e passarlo alla riga di inizializzazione di Engagement esistente:

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]
        return YES;
    }
    ```

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Abilitare l'app per la ricezione delle notifiche push APNS
1. Aggiungere la riga seguente al metodo `application:didFinishLaunchingWithOptions`:

    ```obj-c
    if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
    {
        if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
        {
            [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
        }else
        {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
        }
        [application registerForRemoteNotifications];
    }
    else
    {
        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }
    ```
2. Aggiungere il metodo `application:didRegisterForRemoteNotificationsWithDeviceToken` come illustrato di seguito:

    ```obj-c
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
        NSLog(@"Registered Token: %@", deviceToken);
    }
    ```

3. Aggiungere il metodo `didFailToRegisterForRemoteNotificationsWithError` come illustrato di seguito:

    ```obj-c
    - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
    {
        NSLog(@"Failed to get token, error: %@", error);
    }
    ```

4. Aggiungere il metodo `didReceiveRemoteNotification:fetchCompletionHandler` come illustrato di seguito:

    ```obj-c
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }
    ```
    
[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement SDK per iOS]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
