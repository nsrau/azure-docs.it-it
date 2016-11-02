<properties
    pageTitle="Introduzione ad Azure Mobile Engagement per iOS in Swift | Microsoft Azure"
    description="Informazioni sull'uso di Azure Mobile Engagement con le funzionalità di analisi e notifiche push per le app per iOS."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />


# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Introduzione a Azure Mobile Engagement per app per iOS in Swift

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento descrive come usare Azure Mobile Engagement per comprendere l'utilizzo delle app e inviare notifiche push a utenti segmentati di un'applicazione iOS.
In questa esercitazione si creerà un'app per iOS vuota che raccoglie dati di base e riceve notifiche push tramite Apple Push Notification System (APNS).

Per completare questa esercitazione, è necessario disporre di:

+ XCode 8, che è possibile installare da MAC App Store
+ [Mobile Engagement SDK per iOS]
+ Certificato per notifiche push (.p12) che è possibile ottenere nell'Apple Dev Center.

> [AZURE.NOTE] Questa esercitazione usa Swift versione 3.0. 

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Mobile Engagement relative ad app per iOS.

> [AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a name="<a-id="setup-azme"></a>setup-mobile-engagement-for-your-ios-app"></a><a id="setup-azme"></a>Configurare Mobile Engagement per l'app iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a name="<a-id="connecting-app"></a>connect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione completa relativa all'integrazione è disponibile nella [integrazione di Mobile Engagement iOS SDK](mobile-engagement-ios-sdk-overview.md)

Si creerà un'app di base con Xcode per illustrare l'integrazione.

###<a name="create-a-new-ios-project"></a>Creare un nuovo progetto iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connettere l'app al back-end di Mobile Engagement

1. Scaricare [Mobile Engagement SDK per iOS]
2. Estrarre il file con estensione tar.gz in una cartella nel computer.
3. Fare clic con il pulsante destro del mouse sul progetto e scegliere "Add files to ..."

    ![][1]

4. Passare alla cartella in cui è stato estratto l'SDK, selezionare la cartella `EngagementSDK` e quindi fare clic su OK.

    ![][2]

5. Aprire la scheda `Build Phases` e nel menu `Link Binary With Libraries` aggiungere i framework, come illustrato di seguito:

    ![][3]

8. Creare un'intestazione provvisoria per poter usare le API Objective-C dell'SDK scegliendo File > New > File > iOS > Source > Header File.

    ![][4]

9. Modificare il file di intestazione provvisorio per esporre il codice Mobile Engagement Objective-C al codice Swift e aggiungere le istruzioni import seguenti:

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. In Build Settings assicurarsi che per l'impostazione di compilazione Objective-C Bridging Header in Swift Compiler - Code Generation sia definito un percorso a questa intestazione. Di seguito è illustrato un esempio di percorso: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (a seconda del percorso)**

    ![][6]

11. Tornare al portale di Azure nella pagina *Informazioni di connessione* dell'app e copiare la stringa di connessione.

    ![][5]

12. A questo punto, incollare la stringa di connessione nel delegato `didFinishLaunchingWithOptions` .

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a name="<a-id="monitor"></a>enabling-real-time-monitoring"></a><a id="monitor"></a>Abilitazione del monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement.

1. Aprire il file **ViewController.swift** e sostituire la classe di base **ViewController** per **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a name="<a-id="monitor"></a>connect-app-with-real-time-monitoring"></a><a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a name="<a-id="integrate-push"></a>enabling-push-notifications-and-in-app-messaging"></a><a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement.
Le sezioni seguenti consentono di configurare l'app per la ricezione.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Abilitare l'app per la ricezione delle notifiche push Silent

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Aggiungere la libreria Reach al progetto

1. Fare clic con il pulsante destro del mouse sul progetto.
2. Selezionare `Add file to ...`.
3. Passare alla cartella in cui è stato estratto l'SDK.
4. Selezionare la cartella `EngagementReach`.
5. Fare clic su Aggiungi.
6. Modificare il file di intestazione provvisorio per esporre le intestazioni Reach Mobile Engagement Objective-C e aggiungere le istruzioni import seguenti:

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modificare il delegato dell'applicazione

1. In `didFinishLaunchingWithOptions` creare un modulo Reach e passarlo alla riga di inizializzazione di Engagement esistente:

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Abilitare l'app per la ricezione delle notifiche push APNS
1. Aggiungere la riga seguente al metodo `didFinishLaunchingWithOptions`:

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. Aggiungere il metodo `didRegisterForRemoteNotificationsWithDeviceToken` come illustrato di seguito:

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Aggiungere il metodo `didReceiveRemoteNotification:fetchCompletionHandler:` come illustrato di seguito:

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement SDK per iOS]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png



<!--HONumber=Oct16_HO2-->


