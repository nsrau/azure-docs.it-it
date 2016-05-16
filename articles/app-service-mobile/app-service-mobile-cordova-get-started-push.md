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
	ms.date="05/02/2016"
	ms.author="glenga"/>

# Aggiungere notifiche push all'app Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Panoramica

In questa esercitazione si aggiungeranno notifiche push al progetto di [avvio rapido di Apache Cordova ] per attivare l'invio di una notifica push ogni volta che viene inserito un record. Questa esercitazione è basata sull'esercitazione relativa all'[avvio rapido di Apache Cordova ], che deve essere completata per prima. Se si ha un back-end ASP.NET e non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure].

##<a name="prerequisites"></a>Prerequisiti

Questa esercitazione illustra un'applicazione Apache Cordova in fase di sviluppo in Visual Studio 2015 e in esecuzione nell'emulatore Android di Google. È anche possibile completare questa esercitazione in altri emulatori o dispositivi e su piattaforme di sviluppo diverse.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Account Google] con un indirizzo di posta elettronica verificato.
* Un PC con installato [Visual Studio Community 2015] o versione successiva.
* [Strumenti di Visual Studio per Apache Cordova].
* Un [account Azure attivo](https://azure.microsoft.com/pricing/free-trial/).
* Una progetto di [avvio rapido di Apache Cordova] completato. Altre esercitazioni, ad esempio [autenticazione], possono essere completate prima, ma non è obbligatorio.
* Un dispositivo Android.

Anche se le notifiche push sono supportate su emulatori Android, questi sono risultati instabili e quindi non è consigliabile testare le notifiche push in emulatori.

##<a name="create-hub"></a>Creare un hub di notifica

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a name="enable-gcm"></a>Abilitare Google Cloud Messaging

Poiché verrà usata la piattaforma di Google Android, è necessario abilitare Google Cloud Messaging. Se fosse interessati dispositivi Apple iOS, si dovrà abilitare il supporto del servizio APN. Analogamente, se sono interessati dispositivi Microsoft Windows, si dovrà abilitare il supporto di WNS o MPNS.

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-backend"></a>Configurare il back-end dell'app per dispositivi mobili per l'invio di richieste push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a name="update-service"></a>Aggiornare il progetto server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

##<a name="configure-device"></a>Configurare il dispositivo Android per il debug USB

Prima di poter distribuire l'applicazione al dispositivo Android, è necessario abilitare il debug USB. Sul telefono Android seguire questa procedura:

1. Entrare in **Impostazioni** > **Info dispositivo** e selezionare **Numero build ** circa 7 volte finché non sarà abilitata la modalità sviluppatore.
 
2. Tornare in **Impostazioni** > **Opzioni sviluppatore**, abilitare **Debug USB**, quindi connettere il telefono Android al PC di sviluppo tramite un cavo USB.

Per questo test è stato usato un dispositivo Google Nexus 5X con Android 6.0 (Marshmallow). Tuttavia, le tecniche sono comuni a qualsiasi versione moderna di Android.

##<a name="add-push-to-app"></a>Aggiungere notifiche push all'app

È necessario assicurarsi che il progetto dell'app Apache Cordova sia pronto per gestire le notifiche push. È necessario installare il plug-in di push di Cordova, più eventuali servizi push specifici per la piattaforma.

### Installare il plug-in di push

Le applicazioni Apache Cordova non gestiscono in modo nativo le funzionalità del dispositivo o della rete. Queste funzionalità sono incluse nei plug-in che vengono pubblicati su [npm](https://www.npmjs.com/) o su GitHub. Il plug-in `phonegap-plugin-push` viene usato per gestire le notifiche push di rete.

È possibile installare il plug-in di push in uno dei modi seguenti:

**Dal prompt dei comandi:**

Eseguire il comando seguente:

    cordova plugin add phonegap-plugin-push

**Da Visual Studio:**

1.  In Esplora soluzioni aprire il file `config.xml`, fare clic su **Plug-in** > **Personalizzato**, selezionare **Git** come origine dell'installazione e quindi immettere `https://github.com/phonegap/phonegap-plugin-push` come origine.

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Fare clic sulla freccia accanto all'origine dell'installazione e quindi selezionare **Aggiungi**

Il plug-in di push è stato installato.

### Installare servizi Google Play

Il plug-in di push si basa su servizi Google Play Android per notifiche push.

1.  In **Visual Studio** fare clic su **Strumenti** > **Android** > **Android SDK Manager**, espandere la cartella **Funzionalità aggiuntive** e selezionare la casella per assicurarsi che tutti gli SDK seguenti siano installati.    
    * Android Support Library versione 23 o successiva
    * Android Support Repository versione 20 o successiva
    * Google Play Services versione 27 o successiva
    * Google Repository versione 22 o successiva
     
2.  Fare clic su **Install Packages** (Installa pacchetti) e attendere il completamento dell'installazione.

Le librerie attualmente necessarie sono elencate nella [documentazione relativa all'installazione di phonegap-plugin-push].

### Registrare il dispositivo per il push all'avvio

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

	L'esempio mostra la chiamata a **registerForPushNotifications** dopo il completamento dell'autenticazione, consigliata quando nell'app vengono usate notifiche push e autenticazione.

2. Aggiungere il nuovo metodo `registerForPushNotifications()` come illustrato di seguito:

	    // Register for Push Notifications.
		// Requires that phonegap-plugin-push be installed.
	    var pushRegistration = null;
	    function registerForPushNotifications() {
	        pushRegistration = PushNotification.init({
	            android: {
	                senderID: 'Your_Project_ID'
	            },
	            ios: {
	                alert: 'true',
	                badge: 'true',
	                sound: 'true'
	            },
	            wns: {

	            }
	        });

	        pushRegistration.on('registration', function (data) {
	            client.push.register('gcm', data.registrationId);
	        });

	        pushRegistration.on('notification', function (data, d2) {
	            alert('Push Received: ' + data.message);
	        });

	        pushRegistration.on('error', handleError);
	    }

3. Nel codice riportato sopra sostituire `Your_Project_ID` con l'ID progetto numerico dell'app da [Google Developer Console].

## Testare le notifiche push nell'app 

A questo punto è possibile testare le notifiche push. Eseguire l'applicazione e inserire elementi nella tabella TodoItem usando lo stesso dispositivo oppure un altro dispositivo, purché il back-end sia lo stesso. Testare l'app Cordova sulla piattaforma Android in uno dei modi seguenti:

- **Su un dispositivo fisico:** collegare il dispositivo Android nel computer di sviluppo tramite un cavo USB. Invece di selezionare **Emulatore Android di Google**, scegliere **Dispositivo**. Visual Studio distribuirà l'applicazione nel dispositivo e la eseguirà. Sarà quindi possibile interagire con l'applicazione dal dispositivo. Migliorare l'esperienza di sviluppo. Le applicazioni di condivisione dello schermo come [Mobizen] possono facilitare lo sviluppo di un'applicazione Android proiettando lo schermo Android in un Web browser sul PC.

- **Su un emulatore Android:** prima di ricevere le notifiche push, è necessario aggiungere un account Google nell'emulatore.

##<a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle notifiche push, leggere la pagina [Hub di notifica di Azure].
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
[Account Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google Developer Console]: https://console.developers.google.com/home/dashboard
[documentazione relativa all'installazione di phonegap-plugin-push]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Strumenti di Visual Studio per Apache Cordova]: https://www.visualstudio.com/it-IT/features/cordova-vs.aspx
[Hub di notifica di Azure]: ../notification-hubs/notification-hubs-overview.md
[Apache Cordova SDK]: app-service-mobile-codova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0504_2016-->