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
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Introduzione a Azure Mobile Engagement per app Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'uso dell'app e sull'invio di notifiche push a utenti segmentati di un'applicazione per Android. Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app per Android vuota che raccoglie dati di base e riceve notifiche push tramite il servizio Google Cloud Messaging (GCM).

Per completare questa esercitazione, è necessario disporre di:

+ Android SDK (si presuppone che verrà usato Android Studio), che è possibile scaricare [qui](http://go.microsoft.com/fwlink/?LinkId=389797)
+ [Mobile Engagement SDK per Android]

> [AZURE.IMPORTANT]Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni di Mobile Engagement per app per Android e richiede un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app iOS

[AZURE.INCLUDE [Creare l'app Mobile Engagement nel portale](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione relativa all'integrazione completa è disponibile nelle [procedure di integrazione di Mobile Engagement SDK per Android](../mobile-engagement-android-sdk-overview/).

Si creerà un'app di base con Android Studio per illustrare l'integrazione.

###Creare un nuovo progetto Android

1. Avviare **Android Studio** e nella finestra popup selezionare **Start a new Android Studio project**.

    ![][1]

2. Specificare il nome dell'app e il dominio aziendale. Prendere nota delle informazioni specificate perché saranno necessarie successivamente. Fare clic su **Avanti**.

    ![][2]

3. Selezionare il fattore di forma di destinazione e il livello di API e quindi fare clic su **Next**.
	
	>[AZURE.NOTE]Mobile Engagement richiede almeno un livello API 10 (Android 2.3.3).

    ![][3]

4. Selezionare **Blank Activity**, che sarà l'unica schermata disponibile per l'app, e fare clic su **Next**.

    ![][4]

5. Lasciare quindi invariati i valori predefiniti e fare clic su **Finish**.

    ![][5]

Android Studio crea l'app dimostrativa in cui si integrerà Mobile Engagement.

###Includere nel progetto la libreria SDK

Scaricare e integrare la libreria SDK

1. Scaricare [Mobile Engagement SDK per Android].
2. Estrarre il file di archivio in una cartella nel computer.
3. Identificare la libreria con estensione jar per la versione corrente di questo SDK e copiarla negli Appunti.

	  ![][6]

4. Passare alla sezione **Project** (1) e incollare il file con estensione jar nella cartella delle librerie (2).

	  ![][7]

5. Sincronizzare il progetto per caricare la libreria.

	  ![][8]

###Connettere l'app al back-end di Mobile Engagement usando la stringa di connessione

1. Copiare le righe di codice seguenti nella creazione dell'attività. L'operazione deve essere eseguita in una sola posizione dell'applicazione, in genere nell'attività principale. Per questa app di esempio, aprire MainActivity nella cartella src -> main -> java e aggiungere quanto segue:

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Risolvere i riferimenti premendo ALT+INVIO o aggiungendo le istruzioni import seguenti:

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. Tornare al portale di Azure nella pagina **Informazioni di connessione** dell'app e copiare la **stringa di connessione**.

	  ![][9]

4. Incollare il valore nel parametro `setConnectionString` per sostituire l'esempio fornito come illustrato di seguito:

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###Aggiungere le autorizzazioni e una dichiarazione del servizio

1. Aggiungere queste autorizzazioni al file Manifest.xml del progetto immediatamente prima o dopo il tag `<application>`:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Aggiungere quanto indicato sotto tra i tag `<application>` e `</application>` per dichiarare il servizio agente:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. Nel codice appena incollato sostituire `"<Your application name>"` nell'etichetta. Questo è il valore visualizzato nel menu **Impostazioni**, che mostra all'utente i servizi in esecuzione nel dispositivo. È possibile aggiungere la parola "Service" all'etichetta, ad esempio.

###Inviare una schermata a Mobile Engagement

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement.

Accedere a **MainActivity.java** e aggiungere quanto segue per sostituire la classe di base di **MainActivity** passando da **ActionBarActivity** a **EngagementActivity**:

	public class MainActivity extends EngagementActivity {

##<a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connettere l'app con monitoraggio in tempo reale](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e COINVOLGERLI tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

### Abilitare la messaggistica in-app

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

		 ![][13]

	2. Tornare ad Android Studio, selezionare la directory 'main' dei file del progetto e incollarla per aggiungere le risorse al progetto.

		 ![][14]

###Specificare un'icona per le notifiche

Incollare il frammento di codice XML seguente nel file Manifest.xml tra i tag `<application>` e `</application>`.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

In questo modo si definisce l'icona che viene visualizzata sia nelle notifiche di sistema sia nelle notifiche in-app. Essa è facoltativa per le notifiche in-app, ma obbligatoria per le notifiche di sistema. Android rifiuterà le notifiche di sistema con icone non valide.

Assicurarsi di usare un'icona esistente in una delle cartelle **drawable** (ad esempio ``engagement_close.png``). La cartella **mipmap** non è supportata.

>[AZURE.NOTE]È consigliabile non usare l'icona di **avvio** poiché ha una risoluzione diversa e si trova in genere nelle cartelle mipmap, che non sono supportate.

Per le app reali, è possibile usare un'icona adatta alle notifiche in base alle [linee guida per la progettazione per Android](http://developer.android.com/design/patterns/notifications.html).

>[AZURE.TIP]Per assicurarsi di usare la risoluzione corretta per l'icona, vedere [questi esempi](https://www.google.com/design/icons). Scorrere verso il basso fino alla sezione **Notification**, fare clic su un'icona e quindi su `PNGS` per scaricare il set di icone di tipo drawable. È possibile scegliere le cartelle drawable da usare con una risoluzione specifica per ogni versione dell'icona.

###Abilitare l'app per la ricezione delle notifiche push GCM

1. Incollare quanto segue nel file Manifest.xml tra i tag `<application>` e `</application>` dopo aver sostituito il `project number` ottenuto dalla console Google Play. L'uso di \\n è intenzionale. Assicurarsi di inserirlo alla fine del numero di progetto.

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

3. Aggiungere l'ultimo set di autorizzazioni evidenziate prima del tag `<application>`. Sostituire `<Your package name>` con il nome effettivo del pacchetto dell'applicazione.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Concedere a Mobile Engagement l'accesso alla chiave API GCM

Per consentire a Mobile Engagement di inviare notifiche push per conto dell'utente, è necessario concedere l'accesso alla chiave API. A tale scopo, è necessario configurare la chiave e immetterla nel portale di Mobile Engagement.

1. Passare al portale di Mobile Engagement

	Dal portale di Azure verificare che l'app usata per questo progetto sia aperta e fare clic sul pulsante **Attiva** nella parte inferiore della schermata.

	![][15]

2. Fare quindi clic sulla sezione **Impostazioni** -> **Push nativo** per immettere la chiave GCM:
	  
	![][16]

3. Fare clic sull'icona **Modifica** vicino a **Chiave API** nella sezione delle impostazioni **GCM**, come illustrato di seguito:
	  
	![][17]

4. Nella finestra popup incollare la chiave del server GCM ottenuta nella sezione di [abilitazione di Google Cloud Messaging](#register) e quindi fare clic su **OK**.

	![][18]

##<a id="send"></a>Inviare una notifica all'app

A questo punto si crea una campagna di notifica push semplice che invia una notifica push all'app.

1. Passare alla scheda **REACH** nel portale di Mobile Engagement.
	 
2. Fare clic su **Nuovo annuncio** per creare una campagna di notifica push.
	 
	![][20]

3. Impostare il primo campo della campagna seguendo questa procedura:
	 
	![][21]

	a. Assegnare un nome alla campagna.

	b. Impostare **Tipo di recapito** su *Notifica di sistema -> Semplice*: si tratta di un tipo di notifica push Android semplice con un titolo e una breve riga di testo.

	c. Per **Ora di recapito** selezionare *In qualsiasi momento* per consentire all'app di ricevere una notifica anche se non è stata avviata.

	d. Nel testo della notifica digitare il **titolo** che apparirà in grassetto nel push.

	e. Digitare quindi il messaggio nel campo **Messaggio**.

4. Scorrere verso il basso e nella sezione **Contenuto** selezionare **Solo notifica**.

	![][22]

5. L'impostazione della campagna più semplice possibile è stata completata. Ora scorrere nuovamente verso il basso e fare clic sul pulsante **Crea** per salvare la campagna.

6. Come ultimo passaggio, fare clic su **Attiva** per attivare la campagna e inviare le notifiche push.
    
	![][24]

<!-- URLs. -->
[Mobile Engagement SDK per Android]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-android-get-started/copy-resources.png
[14]: ./media/mobile-engagement-android-get-started/paste-resources.png
[15]: ./media/mobile-engagement-android-get-started/engage-button.png
[16]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[17]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[18]: ./media/mobile-engagement-android-get-started/api-key.png
[20]: ./media/mobile-engagement-android-get-started/new-announcement.png
[21]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[22]: ./media/mobile-engagement-android-get-started/campaign-content.png
[24]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=Oct15_HO3-->