<properties
	pageTitle="Introduzione a Azure Mobile Engagement per iOS in Objective C"
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
	ms.devlang="objective-c"
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Introduzione a Azure Mobile Engagement per app per iOS in Objective C

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione per iOS. In questa esercitazione si creerà un'app per iOS vuota che raccoglie dati di base e riceve notifiche push tramite Apple Push Notification System (APNS). Al termine, si sarà in grado di trasmettere notifiche push a tutti i dispositivi o a utenti specifici in base alle proprietà dei loro dispositivi.

Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Seguire anche l'esercitazione successiva per imparare a usare Mobile Engagement per rivolgersi a gruppi di dispositivi e a utenti specifici.

Per completare questa esercitazione, è necessario disporre di:

+ Xcode, che è possibile installare da MAC App Store
+ [Mobile Engagement iOS SDK]
+ Certificato per notifiche push (.p12) che è possibile ottenere nell'Apple Dev Center.

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
	- **Piattaforma**: selezionare la piattaforma di destinazione (\*\*iOS\*\*) per l'app. Se l'app è destinata a più piattaforme, ripetere questa esercitazione per ogni piattaforma.
	- **Nome della risorsa dell'applicazione**: nome usato per rendere accessibile l'applicazione mediante API e URL. È necessario usare solo caratteri di URL convenzionali. Il nome generato automaticamente dovrebbe fornire una buona base di partenza. È anche necessario aggiungere il nome della piattaforma per evitare conflitti in quanto questo nome deve essere univoco.
	- **Percorso**: selezionare il data center in cui verrà ospitata l'app (e soprattutto la relativa raccolta).
	- **Raccolta**: se è già stata creata un'applicazione, selezionare una raccolta creata in precedenza, in caso contrario selezionare Nuova raccolta.
	- **Nome della raccolta**: nome che identifica il gruppo di applicazioni. Garantisce, inoltre, che tutte le app siano incluse in un gruppo per consentire calcoli aggregati delle metriche. Se applicabile, è possibile usare il nome della società o del reparto.

4. Selezionare l'app appena creata nella scheda **Applicazioni**.

5. Fare clic su **Informazioni di connessione** per visualizzare le impostazioni di connessione da inserire nell'integrazione dell'SDK nell'app per dispositivi mobili.

   	![][10]

6. Copiare la **stringa di connessione**, che è necessaria per identificare l'app nel codice dell'applicazione e connettersi a Mobile Engagement dall'app per il telefono.

   	![][11]

##<a id="connecting-app"></a>Connessione dell'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione completa relativa all'integrazione è disponibile nella [documentazione di Mobile Engagement iOS SDK].

Si creerà un'app di base con Xcode per illustrare l'integrazione.

###Creare un nuovo progetto iOS

Se si ha già un'app e si ha familiarità con lo sviluppo per iOS, è possibile ignorare questo passaggio.

1. Avviare Xcode e nella finestra popup selezionare **Create a new Xcode project**.

   	![][12]

2. Selezionare **Single View Application** e fare clic su Next.

   	![][14]

3. Specificare **nome prodotto**, **nome organizzazione** e **identificatore organizzazione**. Assicurarsi di aver selezionato **Objective-C** per la voce relativa al linguaggio.

   	![][13]

Xcode creerà l'app demo in cui si integrerà Mobile Engagement.

###Connettere l'app al back-end di Mobile Engagement

1. Scaricare [Mobile Engagement iOS SDK].
2. Estrarre il file con estensione tar.gz in una cartella nel computer.
3. Fare clic con il pulsante destro del mouse sul progetto e scegliere "Add files to ..."

	![][17]

4. Passare alla cartella in cui è stato estratto l'SDK, selezionare la cartella `EngagementSDK` e quindi fare clic su OK.

	![][18]

5. Aprire la scheda `Build Phases` e nel menu `Link Binary With Libraries` aggiungere i framework, come illustrato di seguito:

	![][19]

6. Tornare al portale di Azure nella pagina *Informazioni di connessione* dell'app e copiare la stringa di connessione.

	![][11]

7. Aprire il file di implementazione del delegato dell'applicazione e aggiungere la riga di codice seguente.

		#import "EngagementAgent.h"

8. A questo punto, incollare la stringa di connessione nel delegato `didFinishLaunchingWithOptions`.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##<a id="monitor"></a>Abilitazione del monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Attività) al back-end di Mobile Engagement.

- Aprire il file `ViewController.h`, importare `EngagementViewController.h` e sostituire la superclasse dell'interfaccia `ViewController` con `EngagementViewController`, come illustrato di seguito:

![][22]

###Controllare che l'app sia connessa tramite il monitoraggio in tempo reale

Questa sezione descrive come assicurarsi che l'app si connetta al back-end di Mobile Engagement usando la funzionalità di monitoraggio in tempo reale di Mobile Engagement.

1. Passare al portale di Mobile Engagement

	Dal portale di Azure verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante di "Engagement" nella parte inferiore della schermata:

	![][26]

2. Verrà visualizzata la pagina delle impostazioni del portale di Mobile Engagement per l'app. Da questa posizione fare clic sulla scheda "Monitoraggio".

	![][30]

3. Verranno visualizzati, in tempo reale, tutti i dispositivi in cui l'app verrà avviata.

	![][31]

4. Tornare a Xcode e avviare l'app nel simulatore o in un dispositivo connesso.

5. A questo punto dovrebbe essere possibile visualizzare una sessione nel monitoraggio.

**Congratulazioni**. È stato completato il primo passaggio dell'esercitazione e si dispone di un'app che si connette al back-end di Mobile Engagement e che sta già inviando dati.

6. Facendo clic sul pulsante Home del simulatore, il numero di sessioni visualizzato sarà di nuovo pari a 0, come illustrato sopra.

	![][33]

##<a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

### Aggiungere la libreria Reach al progetto

1. Fare clic con il pulsante destro del mouse sul progetto.
2. Selezionare `Add file to ...`.
3. Passare alla cartella in cui è stato estratto l'SDK.
4. Selezionare la cartella `EngagementReach`.
5. Fare clic su Aggiungi.

### Modificare il delegato dell'applicazione

1. Nella parte superiore del file di implementazione importare il modulo di copertura di Engagement:

		#import "AEReachModule.h"

2. In `application:didFinishLaunchingWithOptions` creare un modulo di copertura e passarlo alla riga di inizializzazione di Engagement esistente:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###Abilitare l'app per la ricezione delle notifiche push APNS

1. Aggiungere la riga seguente al metodo `application:didFinishLaunchingWithOptions`:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Aggiungere il metodo `application:didRegisterForRemoteNotificationsWithDeviceToken` come illustrato di seguito:

		(void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Aggiungere il metodo `didReceiveRemoteNotification` come illustrato di seguito:

		(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

###Concedere a Mobile Engagement l'accesso al certificato push

Per consentire a Mobile Engagement di inviare notifiche push per conto dell'utente, è necessario concedere l'accesso al certificato. A tale scopo, è necessario configurare il certificato e immetterlo nel portale di Mobile Engagement. Assicurarsi di avere ottenuto il certificato con estensione p12 come descritto nella documentazione di Apple.

1. Passare al portale di Mobile Engagement. Verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante di Engagement nella parte inferiore della schermata:

	![][26]

2. Verrà visualizzata la pagina delle impostazioni del portale di Mobile Engagement. Da questa posizione fare clic sulla sezione "Push nativo" per aprire il certificato p12:

	![][27]

3. Selezionare il certificato p12, caricarlo e digitare la password:

	![][28]

4. Aggiungere il profilo di provisioning e compilare l'applicazione per un dispositivo di destinazione.

Ora che le impostazioni sono state completate, sarà necessario verificare di avere eseguito correttamente l'integrazione di base.

##<a id="send"></a>Inviare una notifica all'app

Si creerà ora una semplice campagna di notifica push per l'invio di un push all'app.

1. Passare alla scheda Reach nel portale di Mobile Engagement.

2. Fare clic su **Nuovo annuncio** per creare la campagna push.

	![][35]

3. Configurare il primo campo della campagna:

	![][36]

	- 	Assegnare un nome alla campagna.
	- 	Selezionare come tempo di recapito "Solo all'esterno dell'app": si tratta di un tipo di notifica push Apple semplice che include testo.
	- 	Nel testo della notifica digitare innanzitutto il titolo che sarà la prima riga del push.
	- 	Digitare quindi il messaggio che costituirà la seconda riga.


4. Scorrere verso il basso e nella sezione relativa al contenuto selezionare "Solo notifica".

	![][37]

5. L'impostazione della campagna più semplice possibile è stata completata. Scorrere ancora verso il basso e creare la campagna per salvarla. ![][38]

6. Infine, attivare la campagna ![][39].

7. Nel dispositivo dovrebbe essere visualizzata una notifica push.

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png
 

<!---HONumber=July15_HO5-->