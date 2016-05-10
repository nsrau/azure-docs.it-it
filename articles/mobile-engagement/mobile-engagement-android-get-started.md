<properties
	pageTitle="Introduzione a Azure Mobile Engagement"
	description="Informazioni sull'uso di Azure Mobile Engagement con funzionalità di analisi e notifiche push per le app per Android."
	services="mobile-engagement"
	documentationCenter="android"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="piyushjo;ricksal" />

# Introduzione a Azure Mobile Engagement per app Android

[AZURE.INCLUDE [Banner per la selezione del tipo di esercitazione](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'uso dell'app e sull'invio di notifiche push a utenti segmentati di un'applicazione per Android. Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app per Android vuota che raccoglie dati di base e riceve notifiche push tramite il servizio Google Cloud Messaging (GCM).

## Prerequisiti

Per completare questa esercitazione, è necessario disporre di [Android Developer Tools](https://developer.android.com/sdk/index.html) che include l'ambiente di sviluppo integrato di Android Studio e la piattaforma Android più recente.

È necessario anche [Mobile Engagement Android SDK](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Per completare l'esercitazione, è necessario un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## Configurare Mobile Engagement per l'app Android

[AZURE.INCLUDE [Creare l'app Mobile Engagement nel portale](../../includes/mobile-engagement-create-app-in-portal.md)]

## Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione relativa all'integrazione completa è disponibile nelle [procedure di integrazione di Mobile Engagement SDK per Android](mobile-engagement-android-sdk-overview.md).

Si creerà un'app di base con Android Studio per illustrare l'integrazione.

### Creare un nuovo progetto Android

1. Avviare **Android Studio** e nella finestra popup selezionare **Start a new Android Studio project**.

    ![][1]

2. Specificare il nome dell'app e il dominio aziendale. Prendere nota delle informazioni specificate perché saranno necessarie successivamente. Fare clic su **Avanti**.

    ![][2]

3. Selezionare il fattore di forma di destinazione e il livello di API e quindi fare clic su **Next**.

	>[AZURE.NOTE] Mobile Engagement richiede almeno un livello API 10 (Android 2.3.3).

    ![][3]

4. Selezionare **Blank Activity**, che sarà l'unica schermata disponibile per l'app, e fare clic su **Next**.

    ![][4]

5. Lasciare quindi invariati i valori predefiniti e fare clic su **Finish**.

    ![][5]

Android Studio crea l'app dimostrativa in cui si integrerà Mobile Engagement.

### Includere nel progetto la libreria SDK

1. Scaricare [Mobile Engagement Android SDK].
2. Estrarre il file di archivio in una cartella nel computer.
3. Identificare la libreria con estensione jar per la versione corrente di questo SDK e copiarla negli Appunti.

	  ![][6]

4. Passare alla sezione **Project** (1) e incollare il file con estensione jar nella cartella delle librerie (2).

	  ![][7]

5. Sincronizzare il progetto per caricare la libreria.

	  ![][8]

### Connettere l'app al back-end di Mobile Engagement usando la stringa di connessione

1. Copiare le righe di codice seguenti nella creazione dell'attività. L'operazione deve essere eseguita in una sola posizione dell'applicazione, in genere nell'attività principale. Per questa app di esempio, aprire MainActivity nella cartella src -> main -> java e aggiungere quanto segue:

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Risolvere i riferimenti premendo ALT+INVIO o aggiungendo le istruzioni import seguenti:

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. Tornare al portale di Azure classico, nella pagina **Informazioni di connessione** dell'app, e copiare la **stringa di connessione**.

	  ![][9]

4. Incollare il valore nel parametro `setConnectionString` per sostituire l'esempio fornito come illustrato di seguito:

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### Aggiungere le autorizzazioni e una dichiarazione del servizio

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

### Inviare una schermata a Mobile Engagement

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement.

Accedere a **MainActivity.java** e aggiungere quanto segue per sostituire la classe di base di **MainActivity** a **EngagementActivity**:

	public class MainActivity extends EngagementActivity {

È consigliabile impostare come commento (escludere) la riga seguente per questo semplice scenario di esempio:

    // setSupportActionBar(toolbar);

Se si vuole mantenerla, vedere lo scenario "Segnalazione di base" in [Come integrare Engagement in Android](mobile-engagement-android-integrate-engagement.md/#basic-reporting)

## Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connettere l'app con monitoraggio in tempo reale](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## Abilitare le notifiche push e la messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e COINVOLGERLI tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

### Copiare le risorse dell'SDK nel progetto

1. Tornare al contenuto scaricato dell'SDK e copiare la cartella **res**.

	![][10]

2. Tornare ad Android Studio, selezionare la directory **main** dei file del progetto e incollarla per aggiungere le risorse al progetto.

	![][11]

[AZURE.INCLUDE [Abilitare Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Abilitare la messaggistica in-app](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Inviare notifiche dal portale](../../includes/mobile-engagement-android-send-push-from-portal.md)]

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
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png

<!---HONumber=AcomDC_0504_2016-->