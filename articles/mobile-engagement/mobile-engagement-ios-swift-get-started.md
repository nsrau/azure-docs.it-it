<properties
	pageTitle="Introduzione a Azure Mobile Engagement per iOS in Swift"
	description="Informazioni sull'uso di Azure Mobile Engagement con le funzionalità di analisi e notifiche push per le app per iOS."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="get-started-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Introduzione a Azure Mobile Engagement per app per iOS in Swift

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione per iOS. In questa esercitazione si creerà un'app per iOS vuota che raccoglie dati di base e riceve notifiche push tramite Apple Push Notification System (APNS).

Per completare questa esercitazione, è necessario disporre di:

+ XCode 6 o 7 XCode, che è possibile installare dall'App Store MAC
+ [Mobile Engagement SDK per iOS]
+ Certificato per notifiche push (.p12) che è possibile ottenere nell'Apple Dev Center.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Mobile Engagement relative ad app per iOS.

> [AZURE.IMPORTANT]Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni di Mobile Engagement per app per iOS e richiede un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2FIT-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app iOS

[AZURE.INCLUDE [Creare l'app Mobile Engagement nel portale](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione completa relativa all'integrazione è disponibile nella [integrazione di Mobile Engagement iOS SDK](../mobile-engagement-ios-sdk-overview/)

Si creerà un'app di base con Xcode per illustrare l'integrazione.

###Creare un nuovo progetto iOS

[AZURE.INCLUDE [Creare un nuovo progetto iOS](../../includes/mobile-engagement-create-new-ios-app.md)]

###Connettere l'app al back-end di Mobile Engagement

1. Scaricare [Mobile Engagement SDK per iOS].
2. Estrarre il file con estensione tar.gz in una cartella nel computer.
3. Fare clic con il pulsante destro del mouse sul progetto e scegliere "Add files to ..."

	![][1]

4. Passare alla cartella in cui è stato estratto l'SDK, selezionare la cartella `EngagementSDK` e quindi fare clic su OK.

	![][2]

5. Aprire la scheda `Build Phases` e nel menu `Link Binary With Libraries` aggiungere i framework, come illustrato di seguito:

	![][3]

6. Per **XCode 7** -aggiungere `libxml2.tbd` anziché `libxml2.dylib`.

7. Creare un'intestazione provvisoria per poter usare le API Objective-C dell'SDK scegliendo File > New > File > iOS > Source > Header File.

	![][4]

8. Modificare il file di intestazione provvisorio per esporre il codice AzME Objective-C al codice Swift e aggiungere le istruzioni import seguenti:

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushDelegate.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

9. In Build Settings assicurarsi che per l'impostazione di compilazione Objective-C Bridging Header in Swift Compiler - Code Generation sia definito un percorso a questa intestazione. Di seguito è illustrato un esempio di percorso: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (a seconda del percorso)**

10. Tornare al portale di Azure nella pagina *Informazioni di connessione* dell'app e copiare la stringa di connessione.

	![][5]

11. A questo punto, incollare la stringa di connessione nel delegato `didFinishLaunchingWithOptions`.

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Abilitazione del monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement.

1. Aprire il file **viewcontroller. H** e importare **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Ora sostituire la classe con privilegi dell’interfaccia **ViewController** con **EngagementViewController**:

	`@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connettere l'app con monitoraggio in tempo reale](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

### Abilitare l'app per la ricezione delle notifiche push Silent

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### Aggiungere la libreria Reach al progetto

1. Fare clic con il pulsante destro del mouse sul progetto.
2. Selezionare `Add file to ...`.
3. Passare alla cartella in cui è stato estratto l'SDK.
4. Selezionare la cartella `EngagementReach`.
5. Fare clic su Aggiungi.
6. Modificare il file di intestazione provvisorio per esporre le intestazioni Reach AzME Objective-C e aggiungere le istruzioni import seguenti:

		/* Mobile Engagement Reach */
		#import "AE_TBXML.h"
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
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### Modificare il delegato dell'applicazione

1. In `didFinishLaunchingWithOptions` creare un modulo Reach e passarlo alla riga di inizializzazione di Engagement esistente:

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###Abilitare l'app per la ricezione delle notifiche push APNS
1. Aggiungere la riga seguente al metodo `didFinishLaunchingWithOptions`:

		if application.respondsToSelector("registerUserNotificationSettings:")
		{
			application.registerUserNotificationSettings(UIUserNotificationSettings(
			forTypes: (UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound),
			categories: nil))
			application.registerForRemoteNotifications()
		}
		else
		{
			application.registerForRemoteNotificationTypes(UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound)
		}

2. Aggiungere il metodo `didRegisterForRemoteNotificationsWithDeviceToken` come illustrato di seguito:

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. Aggiungere il metodo `didReceiveRemoteNotification:fetchCompletionHandler:` come illustrato di seguito:

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement SDK per iOS]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

<!---HONumber=Sept15_HO4-->