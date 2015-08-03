<properties
	pageTitle="Introduzione ad Azure Mobile Engagement per Cordova/Phonegap"
	description="Informazioni sull'uso di Azure Mobile Engagement con le funzionalità di analisi e notifiche push per le app per Cordova/Phonegap."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="get-started-article" 
	ms.date="07/02/2015"
	ms.author="piyushjo" />

# Introduzione ad Azure Mobile Engagement per Cordova/Phonegap

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Questo argomento illustra come usare Azure Mobile Engagement per comprendere l'utilizzo dell'app e inviare notifiche push a utenti segmentati per un'applicazione mobile sviluppata con Cordova.

In questa esercitazione verrà creata un'app Cordova vuota mediante Mac e quindi verrà eseguita l'integrazione di Mobile Engagement SDK. L'app raccoglierà dati analitici di base e riceverà notifiche push tramite Apple Push Notification System (APNS) per iOS e Google Cloud Messaging (GCM) per Android. L'app verrà distribuita in un dispositivo iOS o Android per il test.

> [AZURE.IMPORTANT]Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

Per completare questa esercitazione, è necessario disporre di:

+ XCode, che può essere installato da Mac App Store (per la distribuzione in iOS)
+ [Android SDK ed emulatore](http://developer.android.com/sdk/installing/index.html) (per la distribuzione in Android)
+ Certificato per notifiche push (con estensione p12) che è possibile ottenere nell'Apple Dev Center per APNS
+ Numero di progetto GCM, che è possibile ottenere da Google Developer Console per GCM
+ [Plug-in di Cordova per Mobile Engagement](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE]Il codice della risorsa e il file Leggimi per il plug-in di Cordova sono disponibili in [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app

1. Accedere al portale di gestione di Azure e quindi fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, quindi su **Mobile Engagement** e infine su **Crea**.

   	![][1]

3. Nella finestra popup che viene visualizzata, immettere le informazioni seguenti:

   	![][2]

	- **Nome dell'applicazione**: nome dell'applicazione. 
	- **Piattaforma**: piattaforma di destinazione per l'app. Scegliere **iOS** o **Android**, in base alla destinazione della distribuzione dell'app Cordova.
	- **Nome della risorsa dell'applicazione**: nome usato per rendere accessibile l'applicazione mediante API e URL. 
	- **Località**: data center in cui verranno ospitate l'app e la raccolta di app.
	- **Raccolta**: selezionare una raccolta creata in precedenza oppure 'Nuova raccolta'.
	- **Nome raccolta**: identifica il gruppo di applicazioni. Garantisce, inoltre, che tutte le app siano incluse in un gruppo per consentire calcoli aggregati delle metriche. Se applicabile, è possibile usare il nome della società o del reparto.

4. Selezionare l'app appena creata nella scheda **Applicazioni**.

5. Fare clic su **Informazioni di connessione** per visualizzare le impostazioni di connessione da inserire nell'integrazione dell'SDK nell'app per dispositivi mobili.

   	![][3]

6. Copiare la **stringa di connessione**, che è necessaria per identificare l'app nel codice dell'applicazione e connettersi a Mobile Engagement dall'app per il telefono.

   	![][4]

##<a id="connecting-app"></a>Connessione dell'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push.

Verrà creata un'app di base con Xcode per illustrare l'integrazione.

###Creare un nuovo progetto Cordova

1. Avviare la finestra *Terminal* nel computer Mac e digitare il codice seguente, per creare un nuovo progetto Cordova dal modello predefinito:

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

> [AZURE.IMPORTANT]Assicurarsi che il profilo di pubblicazione usato per la distribuzione dell'app iOS usi 'com.mycompany.myapp' come ID dell'app.

2. Eseguire il codice seguente per configurare il progetto per **iOS** ed eseguirlo nel simulatore iOS:

		$ cordova platform add ios 
		$ cordova run ios

3. Eseguire il codice seguente per configurare il progetto per **Android** ed eseguirlo nel simulatore Android:

		$ cordova platform add android
		$ cordova run android

4. 	Aggiungere il plug-in per la console Cordova.

		$ cordova plugin add cordova-plugin-console 

###Connettere l'app al back-end di Mobile Engagement

1. Installare il plug-in di Cordova per Azure Mobile Engagement, specificando i valori delle variabili per la configurazione del plug-in:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			--variable AZME_IOS_COLLECTION=<iOSAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=... 
	        --variable AZME_IOS_APPID=... 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_COLLECTION=<AndroidAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=...
	        --variable AZME_ANDROID_APPID=...
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

	Ad esempio:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement   
			--variable AZME_IOS_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=26dxxxxxxxxxxxxb794 
	        --variable AZME_IOS_APPID=piyxxxxxx
	        --variable AZME_IOS_REACH_ICON=icon.png 
			--variable AZME_ANDROID_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=c3d296xxxxxxxxxxc6540
	        --variable AZME_ANDROID_APPID=piyxxxxxxx
			--variable AZME_ANDROID_REACH_ICON=icon   
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=393xxxxxxx718
			--variable AZME_REDIRECT_URL=myapp 
			--variable AZME_ENABLE_LOG=true

> [AZURE.TIP]I valori AppId, SDKKey e Collection possono essere recuperati dalla stringa di connessione **Endpoint={RaccoltaApp.Dominio};SdkKey={CodiceSDK};AppId={IDApp}**

##<a id="monitor"></a>Abilitazione del monitoraggio in tempo reale

1. Nel progetto Cordova modificare **www/js/index.js** in modo da aggiungere la chiamata a Mobile Engagement per dichiarare una nuova attività dopo la ricezione dell'evento *deviceReady*.

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. Eseguire l'applicazione:
		
	- **Per iOS**
	
		Nella finestra `Terminal` avviare l'app in una nuova istanza del simulatore eseguendo il codice seguente:

			cordova run ios

	- **Per Android**
		
		Nella finestra `Terminal` avviare l'app in una nuova istanza dell'emulatore eseguendo il codice seguente:

			cordova run android

3. I log della console avranno un aspetto analogo al seguente:

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

###Controllare che l'app sia connessa tramite il monitoraggio in tempo reale

Questa sezione descrive come assicurarsi che l'app si connetta al back-end di Mobile Engagement usando la funzionalità di monitoraggio in tempo reale di Mobile Engagement.

1. Passare al portale di Mobile Engagement

	Dal portale di Azure verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante di **Engagement** nella parte inferiore della schermata:

	![][6]

2. Nel **portale di Engagement** verrà visualizzata la pagina delle impostazioni dell'app. Da questa posizione fare clic sulla scheda **Monitoraggio**:

	![][7]

3. Se l'app in esecuzione nell'emulatore è configurata correttamente, verrà visualizzata la registrazione di una sessione in tempo reale durante l'esecuzione dell'app:

	![][8]

##<a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app

Mobile Engagement consente di interagire con gli utenti tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

###Configurare le credenziali push per Mobile Engagement

Per consentire a Mobile Engagement di inviare notifiche push per conto dell'utente, è necessario concedere l'accesso al certificato Apple iOS o alla chiave dell'API del server GCM.
	
1. Passare al portale di Mobile Engagement. Verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante **Engagement** nella parte inferiore della schermata:
	
	![][10]
	
2. Verrà visualizzata la pagina delle impostazioni del portale di Mobile Engagement. Da questa posizione fare clic sulla sezione **Push nativo**:
	
	![][11]

3. Configurare il certificato iOS o la chiave dell'API del server GCM

	**[iOS]**

	a. Selezionare il certificato con estensione p12, caricarlo e digitare la password:
	
	![][12]

	**[Android]**

	a. Fare clic sull'icona di modifica vicino a **Chiave API** nella sezione delle impostazioni GCM, come illustrato di seguito:
		
	![][20]
	
	b. Nella finestra popup incollare la chiave del server GCM, quindi fare clic su **OK**.
	
	![][21]

###Abilitare le notifiche push nell'app Cordova

Modificare **www/js/index.js** in modo da aggiungere la chiamata a Mobile Engagement per richiedere le notifiche push e dichiarare un gestore:

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###Esecuzione dell'app

**[iOS]**

1. Verrà usato XCode per sviluppare e distribuire l'app nel dispositivo per testare le notifiche push, poiché iOS consente solo notifiche push in un dispositivo effettivo. Passare al percorso in cui è stato creato il progetto Cordova, quindi passare al percorso **...\platforms\ios**. Aprire il file nativo con estensione xcodeproj file in XCode. 
	
2. Sviluppare e distribuire l'app Cordova nel dispositivo iOS usando l'account con il profilo di provisioning contenente il certificato appena caricato nel portale di Mobile Engagement e l'ID app corrispondente a quello specificato durante la creazione dell'app Cordova. È possibile verificare l'*identificatore di aggregazione* nel file **Resources*-info.plist** in XCode per individuare una corrispondenza.

3. Nel dispositivo verrà visualizzata la finestra popup standard di iOS, che segnala che l'app richiede l'autorizzazione per l'invio di notifiche. Concedere l'autorizzazione.

**[Android]**

È sufficiente usare l'emulatore per eseguire l'app Android, poiché le notifiche GCM sono supportate nell'emulatore di Android.

	cordova run android

##<a id="send"></a>Inviare una notifica all'app

Si creerà ora una semplice campagna di notifica push per l'invio di un push all'app in esecuzione nel dispositivo.

1. Passare alla scheda Reach nel portale di Mobile Engagement.

2. Fare clic su **Nuovo annuncio** per creare la campagna push.

	![][13]

3. Specificare input per la creazione della campagna:

	![][14]

	- 	Fornire un nome per la campagna. 
	- 	**[Android]** Impostare **Tipo di recapito** su *Notifica di sistema* - *Semplice*.
	- 	Impostare Tipo di recapito su: 
		- 	Per **iOS**: *"Solo all'esterno dell'app".*
		- 	Per **Android**: *"In qualsiasi momento".*
		
		Questo è il tipo semplice di notifica push, che include testo.
	- 	Nel testo della notifica digitare innanzitutto il titolo che sarà la prima riga del push.
	- 	Digitare quindi il messaggio che costituirà la seconda riga.

4. Scorrere verso il basso e nella sezione relativa al contenuto selezionare **Solo notifica**.

	![][15]

5. [Facoltativo] È anche possibile specificare un URL di azione. Assicurarsi che usi uno schema di URL fornito durante la configurazione della variabile **AZME REDIRECT URL** del plug-in, ad esempio *myapp://test*. 

5. L'impostazione della campagna più semplice possibile è stata completata. Scorrere ancora verso il basso e fare clic su **Crea** per la campagna per salvarla.
	
	![][16]

6. Fare infine clic su **Attiva** per la campagna.
	
	![][17]

7. Una notifica push dovrebbe essere visualizzata nel dispositivo o nell'emulatore come parte di questa campagna.

##<a id="next-steps"></a>Passaggi successivi
[Panoramica di tutti i metodi disponibili con Cordova Mobile Engagement SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553

<!-- Images. -->
[1]: ./media/mobile-engagement-cordova-get-started/create-mobile-engagement-app.png
[2]: ./media/mobile-engagement-cordova-get-started/create-azme-popup.png
[3]: ./media/mobile-engagement-cordova-get-started/app-main-page-select-connection-info.png
[4]: ./media/mobile-engagement-cordova-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[7]: ./media/mobile-engagement-cordova-get-started/clic-monitor-tab.png
[8]: ./media/mobile-engagement-cordova-get-started/monitor.png
[10]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[11]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[12]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[13]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[14]: ./media/mobile-engagement-cordova-get-started/campaign-first-params.png
[15]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[16]: ./media/mobile-engagement-cordova-get-started/campaign-create.png
[17]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[18]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[19]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[20]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[21]: ./media/mobile-engagement-cordova-get-started/api-key.png

<!---HONumber=July15_HO4-->