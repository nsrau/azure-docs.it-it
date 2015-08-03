<properties
	pageTitle="Introduzione a Azure Mobile Engagement"
	description="Informazioni sull'uso di Azure Mobile Engagement con funzionalità di analisi e notifiche push per le app per Android."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="get-started-article" 
	ms.date="05/01/2015"
	ms.author="piyushjo" />

# Introduzione a Azure Mobile Engagement per app per Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione per Android. Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app per Android vuota che raccoglie dati di base e riceve notifiche push tramite il servizio Google Cloud Messaging (GCM). Al termine, si sarà in grado di trasmettere notifiche push a tutti i dispositivi o a utenti specifici in base alle proprietà dei loro dispositivi. Seguire anche l'esercitazione successiva per imparare a usare Mobile Engagement per rivolgersi a gruppi di dispositivi e a utenti specifici.


Per completare questa esercitazione, è necessario disporre di:

+ Android SDK (si presuppone che verrà usato Android Studio), che è possibile scaricare [qui](http://go.microsoft.com/fwlink/?LinkId=389797)
+ [Mobile Engagement SDK per Android]

> [AZURE.IMPORTANT]Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni di Mobile Engagement per app per Android e richiede un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) e fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, quindi su **Mobile Engagement** e infine su **Crea**.

   	![][7]

3. Nella finestra popup che viene visualizzata, immettere le informazioni seguenti:

   	![][8]

	1. **Nome dell'applicazione**: digitare il nome dell'applicazione. È possibile usare qualsiasi carattere.
	2. **Piattaforma**: selezionare la piattaforma di destinazione per l'app. Se l'app è destinata a più piattaforme, ripetere questa esercitazione per ogni piattaforma.
	3. **Nome della risorsa dell'applicazione**: nome usato per rendere accessibile l'applicazione mediante API e URL. È consigliabile usare solo caratteri di URL convenzionali: il nome generato automaticamente dovrebbe fornire una buona base di partenza. È anche consigliabile aggiungere il nome della piattaforma per evitare conflitti in quanto questo nome deve essere univoco.
	4. **Percorso**: selezionare il data center in cui verrà ospitata l'app (e soprattutto la relativa raccolta, come illustrato di seguito).
	5. **Raccolta**: se è già stata creata un'applicazione, selezionare una raccolta creata in precedenza, in caso contrario selezionare Nuova raccolta.
	6. **Nome della raccolta**: nome che identifica il gruppo di applicazioni. Garantisce, inoltre, che tutte le app siano incluse in un gruppo in modo da consentire calcoli aggregati. È consigliabile usare il nome della società o del reparto.


	Al termine, fare clic sul pulsante di controllo per completare la creazione dell'app.

4. Selezionare o fare clic sull'app appena creata nella scheda **Applicazione**.

   	![][9]

5. Fare quindi clic su **Informazioni di connessione** per visualizzare le impostazioni di connessione da inserire nell'integrazione dell'SDK.

   	![][10]

6. Infine, annotare la **stringa di connessione**, che consentirà di identificare l'app dal codice dell'applicazione.

   	![][11]

	>[AZURE.TIP]È possibile usare l'icona "Copia" a destra della stringa di connessione per copiare la stringa negli Appunti, per comodità.

##<a id="connecting-app"></a>Connessione dell'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione relativa all'integrazione completa è disponibile nella [documentazione di Mobile Engagement SDK per Android].

Si creerà un'app di base con Android Studio per illustrare l'integrazione.

###Creare un nuovo progetto Android

Se si ha già un'app e si ha familiarità con lo sviluppo Android, è possibile ignorare questo passaggio.

1. Avviare Android Studio e, nella finestra popup, selezionare **Start a new Android Studio project**.

   	![][12]

2. Specificare il nome dell'app e il dominio aziendale. Annotare questi valori poiché saranno necessari in seguito e quindi fare clic su **Next**.

   	![][13]

3. Selezionare il fattore di forma di destinazione e il livello API e quindi fare clic su **Next**.
	>[AZURE.NOTE]Mobile Engagement richiede almeno un livello API 10 (Android 2.3.3).

   	![][14]

4. Alla semplice app si aggiungerà ora un'attività, che costituirà la schermata principale, e unica, dell'app. Assicurarsi che sia selezionata l'opzione **Blank Activity** e fare clic su **Next**.

   	![][15]

5. Nella schermata finale della procedura guidata è possibile lasciare invariati tutti i valori ai fini di questa esercitazione e quindi fare clic su **Finish**.

   	![][16]

Android Studio creerà l'app demo in cui si integrerà Mobile Engagement.

###Includere nel progetto la libreria SDK

Scaricare e integrare la libreria SDK

1. Scaricare [Mobile Engagement SDK per Android].
2. Estrarre il file di archivio in una cartella nel computer.
3. Identificare la libreria JAR per la versione corrente di questo SDK e copiarla negli Appunti.

	![][17]

4. Passare alla sezione Project (1) e incollare il file JAR nella cartella libs (2).

	![][18]

5. Sincronizzare il progetto per caricare la libreria.

	![][19]


###Connettere l'app al back-end di Mobile Engagement usando la stringa di connessione

1. Copiare le righe di codice seguenti nella creazione dell'attività. L'operazione deve essere eseguita in una sola posizione dell'applicazione, in genere nell'attività principale.

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Tornare al portale di Azure nella pagina **Informazioni di connessione** dell'app e copiare la **stringa di connessione**.

	![][11]

3. Incollare il valore nel parametro `setConnectionString` per sostituire l'esempio fornito come illustrato di seguito (i valori di AppId e Sdkkey sono stati nascosti).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. Le classi EngagementConfiguration ed EngagementAgent verranno probabilmente mostrate come non risolte (in rosso nel codice). Fare clic su ognuna delle classi non risolte e premere ALT+INVIO per risolverle automaticamente.

	![][20]

###Aggiungere autorizzazioni e la dichiarazione del servizio

1. Aggiungere queste autorizzazioni al file Manifest.xml del progetto immediatamente prima o dopo il tag `<application>`:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

	Si otterrà un risultato come quello illustrato di seguito:

	![][21]

2. Aggiungere quanto indicato sotto tra i tag `<application>` e `</application>` per dichiarare il servizio agente:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. Nel codice appena incollato sostituire `"<Your application name>"` nell'etichetta. Questo è il valore visualizzato nel menu delle impostazioni, che mostra all'utente i servizi in esecuzione nel dispositivo. È possibile aggiungere la parola "Service" all'etichetta, ad esempio.

###Inviare una schermata a Mobile Engagement

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Attività) al back-end di Mobile Engagement. Per farlo, si creerà una sottoclasse di Activity con la classe EngagementActivity disponibile nell'SDK. A tale scopo, sostituire la superclasse di MainActivity, in precedenza ActionBarActivity, con EngagementActivity, come illustrato di seguito:

![][22]

>[AZURE.NOTE]Non dimenticarsi di risolvere la classe se viene visualizzata in rosso facendo clic su di essa e premendo ALT+INVIO.

##<a id="monitor"></a>Come controllare che l'app sia connessa con il monitoraggio in tempo reale

Questa sezione descrive come assicurarsi che l'app si connetta al back-end di Mobile Engagement usando la funzionalità di monitoraggio in tempo reale di Mobile Engagement.

1. Passare al portale di Mobile Engagement.

	Dal portale di Azure verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante di **Engagement** nella parte inferiore della schermata.

	![][26]

2. Verrà visualizzata la pagina delle impostazioni del portale di Engagement per l'app. Da questa posizione fare clic sulla scheda **Monitor**, come illustrato di seguito. ![][30]

3. Verranno visualizzati, in tempo reale, tutti i dispositivi in cui l'app verrà avviata. ![][31]

4. Tornare in Android Studio e avviare l'app nella finestra di monitoraggio o in un dispositivo connesso facendo clic sul triangolo verde e quindi selezionando il dispositivo. ![][32]

5. A questo punto dovrebbe essere possibile visualizzare una sessione nel monitor. ![][33]

**Congratulazioni**. È stato completato il primo passaggio dell'esercitazione, con un'app che si connette al back-end di Mobile Engagement, che sta già inviando dati.


##<a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

### Abilitazione della messaggistica in-app

1. Copiare le risorse di messaggistica in-app seguenti nel file Manifest.xml tra i tag `<application>` e `</application>`.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

2. Copiare le risorse nel progetto eseguendo la procedura seguente:
	1. Tornare al contenuto scaricato dell'SDK e copiare la cartella 'res'.

		![][23]

	2. Tornare ad Android Studio, selezionare la directory 'main' dei file del progetto e incollarla per aggiungere le risorse al progetto.

		![][24]

###Specificare un'icona per le notifiche

Il codice seguente definisce l'icona usata per visualizzare le notifiche in-app e del sistema.

Benché sia facoltativa per le notifiche in-app, è obbligatoria per le notifiche di sistema. Android respinge le notifiche di sistema con icone non valide.

Questo frammento XML deve essere incollato nel file Manifest.xml tra i tag `<application>` e `</application>`.

Assicurarsi di usare un'icona esistente in una delle cartelle **drawable** (ad esempio ``engagement_close.png``). Le cartelle **mipmap** non sono supportate.
	
		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Questo è solo un esempio per illustrare la sintassi. È ovviamente consigliabile usare un'icona adatta alle notifiche, in base alle [linee guida per la progettazione per Android](http://developer.android.com/design/patterns/notifications.html).

È consigliabile non usare l'icona di avvio, poiché ha una risoluzione diversa e si trova in genere nelle cartelle mipmap, che non sono supportate.

>[AZURE.TIP]Per assicurarsi di usare la risoluzione corretta per l'icona, vedere [questi esempi](https://www.google.com/design/icons). Scorrere verso il basso fino alla sezione *Notification*, fare clic su un'icona e quindi su `PNGS` per scaricare il set di icone di tipo drawable. È possibile scegliere le cartelle drawable da usare con una risoluzione specifica per ogni versione dell'icona.

###Abilitare l'app per la ricezione delle notifiche push GCM

1. Immettere i metadati gcm:sender copiando e incollando il codice seguente nel file Manifest.xml tra i tag `<application>` e `</application>`. Il valore nascosto nel codice seguente (rappresentato da asterischi) è il valore di `project number` ottenuto dalla console Google Play. L'uso di \n è intenzionale. Assicurarsi di inserirlo alla fine del numero di progetto.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Incollare il codice seguente nel file Manifest.xml tra i tag `<application>` e `</application>`. Sostituire il nome del pacchetto <Your package name>.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. Aggiungere l'ultimo set di autorizzazioni evidenziato prima del tag `<application>`. Sostituire `<Your package name>` con il nome effettivo del pacchetto dell'applicazione.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Concedere a Mobile Engagement l'accesso alla chiave API GCM

Per consentire a Mobile Engagement di inviare notifiche push per conto dell'utente, è necessario concedere l'accesso alla chiave API. A tale scopo, è necessario configurare la chiave e immetterla nel portale di Mobile Engagement.

1. Passare al portale di Mobile Engagement

	Dal portale di Azure verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante di **Engagement** nella parte inferiore della schermata:

	![][26]

2. Verrà visualizzata la pagina delle impostazioni nel portale di Mobile Engagement. Da questa posizione fare clic sulla sezione **Push nativo** per immettere la chiave GCM: ![][27]

3. Fare clic sull'icona di modifica vicino a **Chiave API** nella sezione delle impostazioni GCM come illustrato di seguito: ![][28]

4. Nella finestra popup incollare la chiave del server GCM ottenuta nella sezione [Abilitare Google Cloud Messaging](#register) e quindi fare clic su **OK**.

	![][29]

A questo punto, viene verificato che tutti i passaggi di questa integrazione di base siano stati effettuati correttamente.

> [AZURE.IMPORTANT]Compilare, avviare con il nuovo codice, chiudere l'app e attendere circa un minuto prima di eseguire le operazioni seguenti

##<a id="send"></a>Come inviare una notifica all'app

A questo punto si creerà una campagna di notifica push semplice che invierà una notifica push all'app.

1. Passare alla scheda **REACH** nel portale di Mobile Engagement. ![][34]

2. Fare clic su **Nuovo annuncio** per creare la campagna di push. ![][35]

3. Impostare il primo campo della campagna completando i passaggi seguenti: ![][36]

	1. Assegnare un nome alla campagna.
	2. Selezionare **Tipo di recapito** come *Notifica di sistema / Semplice*: si tratta di un tipo di notifica push Android semplice con un titolo e una breve riga di testo.
	3. Per **Tempo di recapito** selezionare *In qualsiasi momento* per consentire all'app di ricevere una notifica anche se non è stata avviata.
	4. Nel testo della notifica, digitare il titolo, che apparirà in grassetto nel push.
	5. Digitare quindi il messaggio.

4. Scorrere verso il basso e nella sezione relativa al contenuto selezionare **Solo notifica**. ![][37]

5. L'impostazione della campagna più semplice possibile è stata completata. Scorrere ancora verso il basso e creare la campagna per salvarla. ![][38]

6. Come ultimo passaggio, attivare la campagna. ![][39]


<!-- URLs. -->
[Mobile Engagement SDK per Android]: http://go.microsoft.com/?linkid=9863935
[documentazione di Mobile Engagement SDK per Android]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[7]: ./media/mobile-engagement-android-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-android-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-android-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[21]: ./media/mobile-engagement-android-get-started/permissions.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-android-get-started/engage-button.png
[27]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-android-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-android-get-started/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-android-get-started/reach-tab.png
[35]: ./media/mobile-engagement-android-get-started/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-android-get-started/campaign-content.png
[38]: ./media/mobile-engagement-android-get-started/campaign-create.png
[39]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=July15_HO4-->