<properties
	pageTitle="Aggiungere notifiche push all'app Apache Cordova con App per dispositivi mobili di Azure | Servizio app di Azure"
	description="Informazioni su come usare App per dispositivi mobili di Azure per inviare notifiche push all'app Apache Cordova."
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="glenga"/>

# Aggiungere notifiche push all'app Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Overview

In questa esercitazione si aggiungeranno notifiche push al progetto di [avvio rapido di Apache Cordova ] per attivare l'invio di una notifica push ogni volta che viene inserito un record. Questa esercitazione è basata sull'esercitazione relativa all'[avvio rapido di Apache Cordova ], che deve essere completata per prima. Se si ha un back-end ASP.NET e non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure].

##<a name="prerequisites"></a>Prerequisiti

Questa esercitazione illustra lo sviluppo di un'applicazione Apache Cordova in Visual Studio 2015 e la sua esecuzione nell'emulatore Android di Google e in un dispositivo Android, un dispositivo Windows e un dispositivo iOS.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un PC con installato [Visual Studio Community 2015] o versione successiva.
* [Visual Studio Tools per Apache Cordova].
* Un [account Azure attivo](https://azure.microsoft.com/pricing/free-trial/).
* Una progetto di [avvio rapido di Apache Cordova] completato. Altre esercitazioni, ad esempio l'[autenticazione], possono essere completate prima, ma non è obbligatorio.
* (Android) Un [account Google] con un indirizzo e-mail verificato e un dispositivo Android.
* (iOS) Iscrizione all'Apple Developer Program e un dispositivo iOS. Il simulatore iOS non supporta le notifiche push
* (Windows) Un account per sviluppatore di Windows Store e un dispositivo Windows 10

Anche se le notifiche push sono supportate su emulatori Android, questi sono risultati instabili e quindi non è consigliabile testare le notifiche push in emulatori.

##<a name="create-hub"></a>Creare un hub di notifica

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

[Guardare un video che illustra una procedura simile](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##Aggiornare il progetto server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>Modificare l'app Cordova per ricevere notifiche push

È necessario assicurarsi che il progetto dell'app Apache Cordova sia pronto per gestire le notifiche push. È necessario installare il plug-in di push di Cordova, più eventuali servizi push specifici per la piattaforma.

#### Aggiornare la versione di Cordova nel progetto.

È consigliabile aggiornare il progetto client a Cordova 6.1.1 se è stato configurato con una versione precedente. Per aggiornare il progetto, fare clic con il pulsante destro del mouse sul file config.xml per aprire Progettazione configurazione. Selezionare la scheda Piattaforme e scegliere 6.1.1 nella casella di testo **Cordova CLI**.

Scegliere **Compila**, quindi **Compila soluzione** per aggiornare il progetto.

#### Installare il plug-in di push

Le applicazioni Apache Cordova non gestiscono in modo nativo le funzionalità del dispositivo o della rete. Queste funzionalità sono incluse nei plug-in che vengono pubblicati in [npm](https://www.npmjs.com/) o GitHub. Il plug-in `phonegap-plugin-push` viene usato per gestire le notifiche push di rete.

È possibile installare il plug-in di push in uno dei modi seguenti:

**Dal prompt dei comandi:**

Eseguire il comando seguente:

    cordova plugin add phonegap-plugin-push

**Da Visual Studio:**

1.  In Esplora soluzioni aprire il file `config.xml`, fare clic su **Plug-in** > **Personalizza**, selezionare **Git** come origine dell'installazione e quindi immettere `https://github.com/phonegap/phonegap-plugin-push` come origine.

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Fare clic sulla freccia accanto all'origine dell'installazione.

3. In **SENDER\_ID** è possibile aggiungere l'ID numerico del progetto della Console per gli sviluppatori di Google, se l'ID è già disponibile. In caso contrario immettere un valore segnaposto, ad esempio 777777; se la destinazione è Android sarà possibile aggiornare questo valore nel file config.xml in un secondo momento.

4. Fare clic su **Aggiungi**.

Il plug-in di push è stato installato.

####Installare il plug-in del dispositivo

Seguire la stessa procedura usata per installare il plug-in di push. Il plug-in del dispositivo si trova tuttavia nell'elenco di plug-in di base. Fare clic su **Plug-in** > **Base** per trovarlo. Questo plug-in è necessario per ottenere il nome della piattaforma, `device.platform`.

#### Registrare il dispositivo per il push all'avvio

Verrà inizialmente incluso un codice minimo per Android. Verranno successivamente apportate alcune piccole modifiche per l'esecuzione in iOS o Windows 10.

1. Aggiungere una chiamata a **registerForPushNotifications** durante il callback per il processo di accesso o nella parte inferiore del metodo **onDeviceReady**:

		// Login to the service.
		client.login('google')
		    .then(function () {
		        // Create a table reference
		        todoItemTable = client.getTable('todoitem');

		        // Refresh the todoItems
		        refreshDisplay();

		        // Wire up the UI Event Handler for the Add Item
		        $('#add-item').submit(addItemHandler);
		        $('#refresh').on('click', refreshDisplay);

				    // Added to register for push notifications.
		        registerForPushNotifications();

		    }, handleError);

	L'esempio mostra la chiamata a **registerForPushNotifications** al termine dell'autenticazione, consigliabile quando nell'app vengono usate sia le notifiche push che l'autenticazione.

2. Aggiungere il nuovo metodo **registerForPushNotifications** come indicato di seguito:

		// Register for Push Notifications. Requires that phonegap-plugin-push be installed.
		var pushRegistration = null;
		function registerForPushNotifications() {
		  pushRegistration = PushNotification.init({
		      android: { senderID: 'Your_Project_ID' },
		      ios: { alert: 'true', badge: 'true', sound: 'true' },
		      wns: {}
		  });

		// Handle the registration event.
		pushRegistration.on('registration', function (data) {
		  // Get the native platform of the device.
		  var platform = device.platform;
		  // Get the handle returned during registration.
		  var handle = data.registrationId;
		  // Set the device-specific message template.
		  if (platform == 'android' || platform == 'Android') {
		      // Register for GCM notifications.
		      client.push.register('gcm', handle, {
		          mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'iOS') {
		      // Register for notifications.            
		      client.push.register('apns', handle, {
		          mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'windows') {
		      // Register for WNS notifications.
		      client.push.register('wns', handle, {
		          myTemplate: {
		              body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
		              headers: { 'X-WNS-Type': 'wns/toast' } }
		      });
		  }
		});

		pushRegistration.on('notification', function (data, d2) {
		  alert('Push Received: ' + data.message);
		});

		pushRegistration.on('error', handleError);
		}

3. (Android) Nel codice precedente sostituire `Your_Project_ID` con l'ID progetto numerico dell'app indicato nella [Console per gli sviluppatori di Google].

## (Facoltativo) Configurare ed eseguire l'app in Android

Completare questa sezione per abilitare le notifiche push per Android.

####<a name="enable-gcm"></a>Abilitare Google Cloud Messaging

Poiché la destinazione iniziale è la piattaforma di Google Android, è necessario abilitare Google Cloud Messaging. Analogamente, se fossero specificati dispositivi Microsoft Windows come destinazione sarebbe necessario abilitare il supporto di WNS.

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

[Guardare un video che illustra una procedura simile](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-4-Set-up-gcm-for-push)

####<a name="configure-backend"></a>Configurare il back-end dell'app per dispositivi mobili per l'invio di richieste push con GCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####Configurare l'app Cordova per Android

Nell'app Cordova aprire il file config.xml e sostituire `Your_Project_ID` con l'ID progetto numerico dell'app indicato nella [Console per gli sviluppatori di Google].

		<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
			<variable name="SENDER_ID" value="Your_Project_ID" />
		</plugin>

Aprire index.js e aggiornare il codice per usare l'ID progetto numerico.

		pushRegistration = PushNotification.init({
			android: { senderID: 'Your_Project_ID' },
			ios: { alert: 'true', badge: 'true', sound: 'true' },
			wns: {}
		});

####<a name="configure-device"></a>Configurare il dispositivo Android per il debug USB

Prima di poter distribuire l'applicazione al dispositivo Android, è necessario abilitare il debug USB. Sul telefono Android seguire questa procedura:

1. Passare a **Impostazioni** > **Info sul dispositivo** e toccare **Numero build** circa 7 volte finché non sarà abilitata la modalità sviluppatore.

2. Tornare in **Impostazioni** > **Opzioni per gli sviluppatori**, abilitare **Debug USB**, quindi connettere il telefono Android al PC di sviluppo con un cavo USB.

Per questo test è stato usato un dispositivo Google Nexus 5X con Android 6.0 (Marshmallow). Tuttavia, le tecniche sono comuni a qualsiasi versione moderna di Android.

#### Installare servizi Google Play

Il plug-in di push si basa su servizi Google Play Android per notifiche push.

1.  In **Visual Studio** fare clic su **Strumenti** > **Android** > **Android SDK Manager** (Gestore SDK Android), espandere la cartella **Extras** (Funzionalità aggiuntive) e selezionare la casella per assicurarsi che tutti gli SDK seguenti siano installati.
    * Android Support Repository versione 20 o successiva
    * Google Play Services versione 27 o successiva
    * Google Repository versione 22 o successiva

2.  Fare clic su **Install Packages** (Installa pacchetti) e attendere il completamento dell'installazione.

Le librerie attualmente necessarie sono elencate nella [documentazione relativa all'installazione di phonegap-plugin-push].

#### Testare le notifiche push nell'app in Android

A questo punto è possibile testare le notifiche push. Eseguire l'applicazione e inserire elementi nella tabella TodoItem usando lo stesso dispositivo oppure un altro dispositivo, purché il back-end sia lo stesso. Testare l'app Cordova sulla piattaforma Android in uno dei modi seguenti:

- **In un dispositivo fisico:** collegare il dispositivo Android al computer di sviluppo con un cavo USB. Invece di selezionare **Emulatore Android di Google**, scegliere **Dispositivo**. Visual Studio distribuirà l'applicazione nel dispositivo e la eseguirà. Sarà quindi possibile interagire con l'applicazione dal dispositivo. Migliorare l'esperienza di sviluppo. Le applicazioni di condivisione dello schermo come [Mobizen] possono semplificare lo sviluppo di un'applicazione Android proiettando lo schermo Android in un Web browser sul PC.

- **In un emulatore Android**: sono necessari altri passaggi di configurazione per l'esecuzione in un emulatore.

	Assicurarsi di distribuire o eseguire il debug su un dispositivo virtuale che ha le API Google API impostate come destinazione, come illustrato di seguito nel gestore del dispositivo virtuale Android (AVD).

	![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

	Se si vuole usare un emulatore x86 più veloce, [installare il driver HAXM](https://taco.visualstudio.com/it-IT/docs/run-app-apache/#HAXM) e configurare l'emulatore per usarlo.

	Aggiungere un account Google nel dispositivo Android facendo clic su **App** > **Impostazioni** > **Aggiungi account**, quindi seguire le istruzioni per aggiungere un account Google esistente nel dispositivo. È consigliabile usare un account esistente piuttosto che crearne uno nuovo.

	![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

	Eseguire l'app todolist come fatto in precedenza e inserire un nuovo elemento todo. Questa volta, viene visualizzata un'icona di notifica nell'area di notifica. È possibile aprire la cassetta della notifica per visualizzare il testo completo della notifica.

	![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##(Facoltativo) Configurare ed eseguire l'app in iOS

Questa sezione illustra l'esecuzione del progetto Cordova in dispositivi iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

####Installare ed eseguire l'agente remotebuild per iOS in un computer Mac o un servizio cloud

Prima di eseguire un'app Cordova in iOS usando Visual Studio, seguire i passaggi nella [guida alla configurazione per iOS](http://taco.visualstudio.com/it-IT/docs/ios-guide/) per installare ed eseguire l'agente remotebuild.

Verificare che sia possibile compilare l'app per iOS. I passaggi indicati nella guida alla configurazione sono necessari per la compilazione per iOS da Visual Studio. Se non è disponibile un computer Mac, è possibile eseguire la compilazione per iOS usando l'agente remotebuild in un servizio come MacInCloud. Per altre informazioni, vedere [Build and simulate a Cordova iOS app in the cloud](http://taco.visualstudio.com/it-IT/docs/build_ios_cloud/) (Compilare e simulare un'app Cordova per iOS nel cloud).

>[AZURE.NOTE] XCode 7 o versione successiva è necessario per usare il plug-in di push in iOS.

####Trovare l'ID da usare come ID app

Prima di registrare l'app per le notifiche push, aprire il file config.xml nell'app Cordova, trovare il valore dell'attributo `id` nell'elemento widget e copiarlo per usarlo in un secondo momento. Nel codice XML seguente, l'ID è `io.cordova.myapp7777777`.

		<widget defaultlocale="it-IT" id="io.cordova.myapp7777777"
  		version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
			xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Questo identificatore verrà usato in un secondo momento, durante la creazione di un ID app nel portale per sviluppatori di Apple. Se si crea un ID app diverso nel portale per sviluppatori e si vuole usare quell'ID, è necessario eseguire alcuni passaggi aggiuntivi più avanti in questa esercitazione per modificare l'ID nel file config.xml. L'ID nell'elemento widget deve corrispondere all'ID app indicato nel portale per sviluppatori.

####Registrare l'app per le notifiche push nel portale per sviluppatori Apple

[AZURE.INCLUDE [notification-hubs-xamarin-enable-apple-push-notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

[Guardare un video che illustra una procedura simile](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####Configurare Azure per l'invio di notifiche push

1. Accedere al [Portale di Azure](https://portal.azure.com/). Fare clic su **Sfoglia** > **App per dispositivi mobili** > App per dispositivi mobili > **Impostazioni** > **Push** > **Apple (APNS)** > **Carica certificato**. Caricare il file del certificato push (con estensione p12) esportato in precedenza. Assicurarsi di selezionare **Sandbox** se è stato creato un certificato push di sviluppo per le fasi di sviluppo e test. In caso contrario, scegliere **Produzione**. Il servizio è ora configurato per l'uso delle notifiche push per iOS.

	![](./media/app-service-mobile-cordova-get-started-push/mobile-app-upload-apns-cert.png)

####Verificare che l'ID app corrisponda all'app Cordova

Se l'ID app creato nell'account per sviluppatori Apple corrisponde già all'ID dell'elemento widget nel file config.xml, è possibile ignorare questo passaggio. Se tuttavia gli ID non corrispondono, procedere come segue:

1. Eliminare la cartella platforms dal progetto.

2. Eliminare la cartella plugins dal progetto.

3. Eliminare la cartella node\_modules dal progetto.

4. Aggiornare l'attributo id dell'elemento widget nel file config.xml per usare l'ID app creato nell'account per sviluppatori Apple.

5. Ricompilare il progetto.

#####Testare le notifiche push nell'app iOS

1. In Visual Studio verificare che sia selezionato **iOS** come destinazione di distribuzione e quindi scegliere **Dispositivo** per l'esecuzione nel dispositivo iOS connesso.

	È possibile eseguire il progetto in un dispositivo iOS connesso al PC usando iTunes. Il simulatore iOS non supporta le notifiche push.

2. Scegliere **Esegui** o premere **F5** in Visual Studio per compilare il progetto e avviare l'app in un dispositivo iOS, quindi fare clic su **OK** per accettare le notifiche push.

	>[AZURE.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

3. Nell'app digitare un'attività e fare clic sull'icona con il segno PIÙ (+).

4. Verificare che venga ricevuta una notifica, quindi fare clic su OK per ignorarla.

##(Facoltativo) Configurare ed eseguire l'app in Windows

Questa sezione descrive l'esecuzione del progetto di un'app Apache Cordova nei dispositivi Windows 10. Il plug-in di push PhoneGap è supportato in Windows 10. Se non si usano dispositivi Windows, è possibile ignorare questa sezione.

####Registrare l'app di Windows per le notifiche push con WNS

Per usare le opzioni di Store in Visual Studio, selezionare una destinazione Windows dall'elenco Piattaforme soluzione, ad esempio **Windows-x64** o **Windows-x86**. Evitare **Windows-AnyCPU** per le notifiche push.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Guardare un video che illustra una procedura simile](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####Configurare l'hub di notifica per WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####Configurare l'app Cordova per il supporto delle notifiche push di Windows

Aprire Progettazione configurazione facendo clic con il pulsante destro del mouse sul file config.xml e scegliendo **Visualizza finestra di progettazione**. Selezionare quindi la scheda **Windows** e scegliere **Windows 10** in **Versione Windows di destinazione**.

>[AZURE.NOTE] Se si usa una versione di Cordova precedente a Cordova 5.1.1 (6.1.1 consigliata), è necessario impostare anche il flag Popup supportati su true nel file config.xml.

Per supportare le notifiche push nelle compilazioni predefinite (debug), aprire il file build.json. Copiare la configurazione "release" nella configurazione di debug.

		"windows": {
			"release": {
				"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
				"publisherId": "CN=yourpublisherID"
			}
		}

Dopo l'aggiornamento, il codice precedente sarà simile al seguente.

	"windows": {
		"release": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			},
		"debug": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			}
		}

Compilare l'app e verificare che non siano presenti errori. A questo punto l'app client dovrebbe eseguire la registrazione per le notifiche dal back-end dell'app per dispositivi mobili. Ripetere questa sezione per ogni progetto Windows nella soluzione.

####Testare le notifiche push nell'app di Windows

In Visual Studio verificare che sia selezionata una piattaforma Windows come destinazione di distribuzione, ad esempio **Windows-x64** o **Windows-x86**. Per eseguire l'app in un PC con Windows 10 che ospita Visual Studio, scegliere **Computer locale**.

Premere il pulsante Esegui per compilare il progetto e avviare l'app.

Nell'app digitare un nome per un nuovo elemento todoitem, quindi fare clic sull'icona del segno più (+) per aggiungerlo.

Assicurarsi di ricevere una notifica quando viene aggiunto l'elemento.

##<a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle notifiche push, vedere [Hub di notifica di Azure].
* Se non è già stato fatto, proseguire con l'esercitazione [aggiungendo l'autenticazione] all'app Apache Cordova.

Informazioni su come usare gli SDK.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs -->
[aggiungendo l'autenticazione]: app-service-mobile-cordova-get-started-users.md
[avvio rapido di Apache Cordova]: app-service-mobile-cordova-get-started.md
[avvio rapido di Apache Cordova ]: app-service-mobile-cordova-get-started.md
[autenticazione]: app-service-mobile-cordova-get-started-users.md
[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[account Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Console per gli sviluppatori di Google]: https://console.developers.google.com/home/dashboard
[documentazione relativa all'installazione di phonegap-plugin-push]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools per Apache Cordova]: https://www.visualstudio.com/it-IT/features/cordova-vs.aspx
[Hub di notifica di Azure]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0907_2016-->