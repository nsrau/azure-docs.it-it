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
	ms.topic="article" 
	ms.date="04/30/2015" 
	ms.author="piyushjo" />

# Introduzione a Azure Mobile Engagement per app per iOS in Swift

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md) 
- [iOS - Obj C](mobile-engagement-ios-get-started.md) 
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md) 

Questo argomento descrive come usare Azure Mobile Engagement per comprendere l'utilizzo delle app e inviare notifiche push a utenti segmentati di un'applicazione iOS. In questa esercitazione si creerà un'app per iOS vuota che raccoglie dati di base e riceve notifiche push tramite Apple Push Notification System (APNS). Al termine, si sarà in grado di trasmettere notifiche push a tutti i dispositivi o a utenti specifici in base alle proprietà dei loro dispositivi.

Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Seguire anche l'esercitazione successiva per imparare a usare Mobile Engagement per rivolgersi a gruppi di dispositivi e a utenti specifici.

Per completare questa esercitazione, è necessario disporre di:

+ Xcode, che è possibile installare da App Store MAC
+ [Mobile Engagement iOS SDK]
+ Certificato per notifiche push (.p12), che è possibile ottenere nell'Apple Dev Center.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Mobile Engagement relative ad app per iOS.

> [AZURE.IMPORTANT]Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni di Mobile Engagement per app per iOS e richiede un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app

1. Accedere al portale di gestione di Azure e quindi fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, quindi su **Mobile Engagement** e infine su **Crea**.

   	![][7]

3. Nella finestra popup che viene visualizzata, immettere le informazioni seguenti:
 
   	![][8]

	- **Nome dell'applicazione**: digitare il nome dell'applicazione. È possibile usare qualsiasi carattere.
	- **Piattaforma**: selezionare la piattaforma di destinazione per l'app (**iOS**). Se destinata a più piattaforme, ripetere questa esercitazione per ogni piattaforma. 
	- **Nome della risorse dell'applicazione**: nome tramite cui l'applicazione è accessibile mediante API e URL. È necessario usare solo caratteri URL convenzionali. Il nome generato automaticamente deve fornire una base sicura. È anche necessario aggiungere il nome della piattaforma per evitare conflitti tra i nomi in quanto questo nome deve essere univoco.
	- **Percorso**: selezionare il data center dove l'applicazione (e soprattutto la relativa raccolta) verrà ospitata.
	- **Raccolta**: se è già stata creata un'applicazione, selezionare una raccolta creata in precedenza, in caso contrario selezionare Nuova raccolta.
	- **Nome della raccolta**: rappresenta il gruppo di applicazioni. Garantisce, inoltre, che tutte le app si trovino in un gruppo che consentirà calcoli aggregati delle metriche. Se applicabile, è possibile usare il nome della società o del reparto.

4. Selezionare l'app appena creata nella scheda **Applicazioni**.

5. Fare clic su **Informazioni di connessione** per visualizzare le impostazioni di connessione da inserire nell'integrazione dell'SDK nell'app per dispositivi mobili.
 
   	![][10]

6. Copiare la **stringa di connessione**, che è necessaria per identificare l'app nel codice dell'applicazione e connettersi a Mobile Engagement dall'app per il telefono.

   	![][11]

##<a id="connecting-app"></a>Connessione dell'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione completa relativa all'integrazione è disponibile nella [Documentazione di Mobile Engagement iOS SDK].

Verrà creata un'app di base con XCode per illustrare l'integrazione.

###Creare un nuovo progetto iOS

Se si dispone già di un'app e si ha familiarità con lo sviluppo per iOS, è possibile ignorare questo passaggio.

1. Avviare Xcode e nella finestra popup selezionare **Create a new Xcode project**.

   	![][12]

2. Selezionare **Single View Application** e fare clic su Next.

   	![][14]

3. Specificare **nome prodotto**, **nome organizzazione** e **identificatore organizzazione**. Assicurarsi di aver selezionato **Swift** per la voce relativa al linguaggio.

   	![][40]

Xcode creerà l'app demo in cui verrà integrato Mobile Engagement.

###Connettere l'app al back-end di Mobile Engagement 

1. Scaricare [Mobile Engagement iOS SDK]
2. Estrarre il file con estensione tar.gz in una cartella nel computer.
3. Fare clic con il pulsante destro del mouse sul progetto e scegliere "Add files to"

	![][17]

4. Passare alla cartella in cui è stato estratto l'SDK, selezionare la cartella `EngagementSDK` e quindi fare clic su OK.

	![][18]

5. Aprire la scheda `Build Phases` e nel menu `Link Binary With Libraries` aggiungere i framework, come illustrato di seguito:

	![][19]

6. Creare un'intestazione provvisoria per poter usare le API Objective-C dell'SDK scegliendo File > New > File > iOS > Source > Header File.

	![][41]

7. Modificare il file di intestazione provvisorio per esporre il codice AzME Objective-C al codice Swift e aggiungere le istruzioni import seguenti:

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushDelegate.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

8. In Build Settings assicurarsi che per l'impostazione di compilazione Objective-C Bridging Header in Swift Compiler - Code Generation sia definito un percorso a questa intestazione. Di seguito è illustrato un esempio di percorso: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (a seconda del percorso)**

9. Tornare al portale di Azure nella pagina *Informazioni di connessione* dell'app e copiare la stringa di connessione.

	![][11]

10. A questo punto, incollare la stringa di connessione nel delegato `didFinishLaunchingWithOptions`.

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Abilitazione del monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Attività) al back-end di Mobile Engagement.

- Aprire il file `ViewController.h`, importare `EngagementViewController.h` e sostituire la super classe dell'interfaccia `ViewController` con `EngagementViewController`.

###Controllare che l'app sia connessa tramite il monitoraggio in tempo reale

Questa sezione descrive come assicurarsi che l'app si connetta al back-end di Mobile Engagement usando la funzionalità di monitoraggio in tempo reale di Mobile Engagement.

1. Passare al portale di Mobile Engagement

	Dal portale di Azure verificare di trovarsi nell'app usata per questo progetto e quindi fare clic sul pulsante "Engagement" nella parte inferiore:

	![][26]

2. Verrà visualizzata la pagina delle impostazioni del portale di Mobile Engagement per l'app. Da questa posizione fare clic sulla scheda "Monitor".

	![][30]

3. Verranno visualizzati, in tempo reale, tutti i dispositivi in cui l'app verrà avviata.

	![][31]

4. Tornare a Xcode e avviare l'app nel simulatore o in un dispositivo connesso.

5. A questo punto dovrebbe essere possibile visualizzare una sessione nel monitor.

**Congratulazioni**. È stato completato il primo passaggio dell'esercitazione e si dispone di un'app che si connette al back-end di Mobile Engagement e che sta già inviando dati.

6. Selezionando il pulsante per tornare alla home page del simulatore, il numero di sessioni visualizzato sarà di nuovo pari a 0, come illustrato sopra.

	![][33]

##<a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app

Mobile Engagement consente di interagire con gli utenti garantendo la COPERTURA, tramite notifiche push e messaggistica in-app nel contesto delle campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

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

3. Aggiungere il metodo `didReceiveRemoteNotification` come illustrato di seguito:

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject])
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo)
		}

###Concedere a Mobile Engagement l'accesso al certificato push

Per consentire a Mobile Engagement di inviare notifiche push per conto dell'utente, è necessario concedere l'accesso al certificato. A tale scopo, è necessario configurare il certificato e immetterlo nel portale di Mobile Engagement. Assicurarsi di avere ottenuto il certificato con estensione p12 come descritto nella documentazione di Apple.

1. Passare al portale di Mobile Engagement. Verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante nella parte inferiore della schermata:

	![][26]

2. Verrà visualizzata la pagina delle impostazioni del portale di Mobile Engagement. Da questa posizione fare clic sulla sezione "Push nativo" per aprire il certificato p12:

	![][27]

3. Selezionare il certificato p12, caricarlo e digitare la password:

	![][28]

4. Aggiungere il profilo di provisioning e compilare l'applicazione per un dispositivo di destinazione.

Ora che le impostazioni sono state completate, sarà necessario verificare di avere eseguito correttamente l'integrazione di base.

##<a id="send"></a>Inviare una notifica all'app

Verrà ora creata una semplice campagna di notifica push per l'invio di un push all'app.

1. Passare alla scheda Reach nel portale di Mobile Engagement.

2. Fare clic su **Nuovo annuncio** per creare una campagna di push.
	
	![][35]

3. Configurare il primo campo della campagna:

	![][36]

	- 	Assegnare un nome alla campagna.
	- 	Selezionare come tempo di recapito "Solo all'esterno dell'app": si tratta di un tipo di notifica push Apple semplice che include testo.
	- 	Nel testo della notifica digitare innanzitutto il titolo che sarà la prima riga del push.
	- 	Digitare quindi il messaggio che costituirà la seconda riga.


4. Scorrere verso il basso e nella sezione relativa al contenuto selezionare "Solo notifica".

	![][37]

5. L'impostazione della campagna più semplice possibile è stata completata. Scorrere ancora verso il basso e creare la campagna per salvarla.![][38]

6. Infine, attivare la campagna ![][39].

7. Nel dispositivo dovrebbe essere visualizzata una notifica push.

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-swift-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-swift-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-swift-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-swift-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-swift-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-swift-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-swift-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-swift-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-swift-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-swift-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-swift-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-swift-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-swift-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-swift-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-swift-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-swift-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-swift-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-swift-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-swift-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-swift-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-ios-swift-get-started/SwiftSelection.png
[41]: ./media/mobile-engagement-ios-swift-get-started/AddHeaderFile.png

<!--HONumber=54--> 