<properties 
	pageTitle="Introduzione a Azure Mobile Engagement per iOS" 
	description="Informazioni su come usare Azure Mobile Engagement con le funzionalità di analisi e le notifiche push in iOS." 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />

# Introduzione a Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS"  class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

Questo argomento descrive come usare Azure Mobile Engagement per comprendere l'utilizzo delle app e inviare notifiche push a utenti segmentati di un'applicazione iOS. 
In questa esercitazione si creerà un'app per iOS vuota che raccoglie dati di base e riceve notifiche push tramite Apple Push Notification System (APNS). Al termine, si sarà in grado di trasmettere notifiche push a tutti i dispositivi o a utenti specifici in base alle proprietà dei loro dispositivi.

Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Seguire anche l'esercitazione successiva per imparare a usare Mobile Engagement per rivolgersi a gruppi di dispositivi e a utenti specifici. 

Per completare questa esercitazione, è necessario disporre di:

+ Xcode, che è possibile installare da App Store MAC.
+ [Mobile Engagement iOS SDK].
+ Certificato per notifiche push (.p12) che è possibile ottenere nell'Apple Dev Center.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Mobile Engagement relative ad app per iOS. 

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app

1. Accedere al [portale di gestione di Azure] e fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, quindi su **Mobile Engagement** e infine su **Crea**.

   	![][7]

3. Nella finestra popup visualizzata sono presenti alcuni campi da compilare:
 
   	![][8]

	1. *Nome dell'applicazione*: digitare il nome dell'applicazione. È possibile usare qualsiasi carattere.
	2. *Piattaforma*: selezionare la piattaforma di destinazione per l'app (se l'app è destinata a più piattaforme, ripetere l'esercitazione per ogni piattaforma).
	3. *Nome risorsa dell'applicazione*: nome tramite cui l'applicazione sarà accessibile mediante API e URL. È consigliabile usare solo caratteri URL convenzionali: il nome generato automaticamente dovrebbe fornire una buona base di partenza. È anche consigliabile aggiungere il nome della piattaforma per evitare conflitti tra i nomi in quanto questo nome deve essere univoco.
	4. *Percorso*: selezionare il data center in cui l'app (e soprattutto la relativa raccolta, come illustrato di seguito) verrà ospitata.
	5. *Raccolta*: se è già stata creata un'applicazione, selezionare una raccolta creata in precedenza, in caso contrario selezionare Nuova raccolta.


	Al termine, fare clic sul segno di spunta per completare la creazione dell'app.

4. Selezionare o fare clic sull'app appena creata nella scheda Applicazione:
 
   	![][9]

5. Fare quindi clic su "Informazioni di connessione" per visualizzare le impostazioni di connessione da inserire nell'integrazione SDK.
 
   	![][10]

6. Infine, annotare il valore di "Stringa di connessione", che consentirà di identificare l'app dal codice dell'applicazione.

   	![][11]

	>[AZURE.TIP] È possibile usare l'icona "Copia" a destra della stringa di connessione per copiare la stringa negli Appunti, per comodità.

##<a id="connecting-app"></a>Connessione dell'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione relativa all'integrazione completa è disponibile nella [documentazione di Mobile Engagement iOS SDK].

Per illustrare l'integrazione, verrà creata un'app di base con Android Studio.

###Creare un nuovo progetto iOS

Se si dispone già di un'app e si ha familiarità con lo sviluppo per iOS, è possibile ignorare questo passaggio.

1. Avviare Xcode e nella finestra popup selezionare "Create a new Xcode project".

   	![][12]

2. Specificare il nome dell'app, oltre al nome e all'ID della società. Annotare questi valori in quanto saranno necessari in seguito, quindi fare clic su Next.

   	![][13]

3. Selezionare Single View Application e fare clic su Next.

   	![][14]


Xcode creerà l'app demo in cui verrà integrato Mobile Engagement.

###Includere nel progetto la libreria SDK

Scaricare e integrare la libreria SDK

1. Scaricare [Mobile Engagement iOS SDK]
2. Estrarre il file con estensione tar.gz in una cartella nel computer.
3. Fare clic con il pulsante destro del mouse sul progetto e scegliere "Add files to ..."

	![][17]

4. Passare alla cartella in cui è stato estratto l'SDK e selezionare la cartella `EngagementSDK`, quindi fare clic su OK.

	![][18]

5. Aprire la scheda `Build Phases` e nel menu `Link Binary With Libraries` aggiungere i framework come illustrato di seguito:

	![][19]


###Connettere l'app al back-end di Mobile Engagement usando la stringa di connessione

1. Copiare la riga di codice seguente all'inizio del file di implementazione Application Delegate.

		#import "EngagementAgent.h"

2. Tornare al portale di Azure e nella pagina *Informazioni di connessione* dell'app copiare la stringa di connessione.

	![][11]

3. Incollarla nel delegato `didFinishLaunchingWithOptions`.		

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##Inviare una schermata a Mobile Engagement

Per iniziare a inviare i dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (attività) al back-end di Mobile Engagement. Per farlo, verrà eseguito l'overload della classe `UIViewController` tramite  `EngagementViewController` disponibile nell'SDK.
A questo scopo, aprire il file `ViewController.h` importare `EngagementViewController.h` e sostituire la superclasse dell'interfaccia `ViewController` con `EngagementViewController`, come illustrato di seguito:

![][22]

##<a id="monitor"></a>Come controllare che l'app sia connessa con il monitoraggio in tempo reale

Questa sezione descrive come assicurarsi che l'app si connetta al back-end di Mobile Engagement usando la funzionalità di monitoraggio in tempo reale di Mobile Engagement.

1. Passare al portale di Mobile Engagement

	Dal portale di Azure verificare di trovarsi nell'app usata per questo progetto e quindi fare clic sul pulsante "Engagement" nella parte inferiore:

	![][26]

2. Verrà visualizzata la pagina delle impostazioni del portale di Mobile Engagement per l'app. Da questa posizione fare clic sulla scheda "Monitoraggio".

	![][30]

3. Verranno visualizzati, in tempo reale, tutti i dispositivi in cui l'app verrà avviata.

	![][31]

4. Tornare a Xcode e avviare l'app nel simulatore o in un dispositivo connesso.

5. Se l'operazione ha funzionato, dovrebbe venire ora visualizzata una sessione. 

**Congratulazioni.** È stato completato il primo passaggio dell'esercitazione e si dispone di un'app che si connette al back-end di Mobile Engagement, che sta già inviando dati.

6. Facendo clic sul pulsante Home del simulatore, il numero di sessioni visualizzato sarà di nuovo pari a 0, come illustrato sopra.

	![][33]



##<a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app

Mobile Engagement consente di interagire con gli utenti garantendo la COPERTURA, tramite notifiche push e messaggistica in-app nel contesto delle campagne. Questo modulo è denominato COPERTURA nel portale di Mobile Engagement.
Le sezioni seguenti consentono di configurare l'app per la ricezione.

### Aggiungere la libreria di Copertura al progetto

1. Fare clic con il pulsante destro del mouse sul progetto.
2. Selezionare `Aggiungi file a ...`
3. Passare alla cartella in cui è stato estratto l'SDK.
4. Selezionare la cartella `EngagementReach`.
5. Fare clic su Aggiungi.

### Modificare il delegato dell'applicazione

1. Nella parte superiore del file di implementazione importare il modulo di copertura di Engagement:

		#import "AEReachModule.h"
	
2. All'interno di `application:didFinishLaunchingWithOptions` creare un modulo di copertura e passarlo alla riga di inizializzazione di Engagement esistente:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}	

###Abilitare l'app per la ricezione delle notifiche push APNS

1. Aggiungere la riga seguente al metodo  `application:didFinishLaunchingWithOptions`:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Aggiungere il metodo `application:didRegisterForRemoteNotificationsWithDeviceToken` come illustrato di seguito:
 
		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 		   [[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Aggiungere il metodo `didReceiveRemoteNotification` come illustrato di seguito:
4. 
		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

###Concedere a Mobile Engagement l'accesso al certificato push

Per consentire a Mobile Engagement di inviare notifiche push per conto dell'utente, è necessario concedere l'accesso al certificato. A tale scopo, è necessario configurare il certificato e immetterlo nel portale di Mobile Engagement. Assicurarsi di avere ottenuto il certificato con estensione p12 come descritto nella documentazione di Apple.

1. Passare al portale di Mobile Engagement

	Dal portale di Azure verificare di trovarsi nell'app usata per questo progetto e quindi fare clic sul pulsante "Engagement" nella parte inferiore:

	![][26]

2. Verrà visualizzata la pagina delle impostazioni del portale di Mobile Engagement. Da questa posizione fare clic sulla sezione "Push nativo" per aprire il certificato p12:

	![][27]

3. Selezionare il certificato p12, caricarlo e digitare la password:

	![][28]

4. Aggiungere il profilo di provisioning e compilare l'applicazione per un dispositivo di destinazione.

Ora che le impostazioni sono state completate, sarà necessario verificare di avere eseguito correttamente l'integrazione di base.

##<a id="send"></a>Come inviare una notifica all'app

Verrà ora creata una semplice campagna di notifica push per l'invio di un push all'app.

1. Passare alla scheda Copertura nel portale di Mobile Engagement.

2. Fare clic su "Nuovo annuncio" per creare la campagna push.
	
	![][35]

3. Configurare il primo campo della campagna:

	![][36]

	1. Assegnare alla campagna il nome desiderato.
	2. Selezionare come tempo di recapito "Solo all'esterno dell'app": questo è il semplice tipo di notifica push Apple che include testo.
	3. Nel testo della notifica digitare innanzitutto il titolo che sarà la prima riga del push.
	4. Digitare quindi il messaggio che costituirà la seconda riga.

4. Scorrere verso il basso e nella sezione relativa al contenuto selezionare "Solo notifica".

	![][37]

5. L'impostazione della campagna più semplice possibile è stata completata. Scorrere ancora verso il basso e creare la campagna per salvarla.
![][38]

6. Come ultimo passaggio, attivare la campagna.
![][39]

7. Nel dispositivo dovrebbe essere visualizzata una notifica push.

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Documentazione di Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9874682
[Portale di gestione di Azure]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-ios-get-started/select-app.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[23]: ./media/mobile-engagement-ios-get-started/copy-resources.png
[24]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[25]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-ios-get-started/api-key.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[32]: ./media/mobile-engagement-ios-get-started/launch.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!--HONumber=47-->
