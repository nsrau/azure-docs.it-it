<properties
	pageTitle="Introduzione ad Hub di notifica di Azure per le app per Chrome | Microsoft Azure"
	description="In questa esercitazione si apprende come usare Hub di notifica di Azure per inviare notifiche push a un'app Chrome."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-chrome"
	ms.devlang="JavaScript"
	ms.topic="hero-article"
	ms.date="09/03/2015"
	ms.author="wesmc"/>

# Introduzione ad Hub di notifica per le app per Chrome

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Questo argomento illustra come usare Hub di notifica di Azure per inviare notifiche push a un'app Chrome.

Uno dei vantaggi principali dell'uso delle notifiche relative all'app Chrome consiste nel fatto che le notifiche vengono visualizzate nel contesto del browser Google Chrome. Non è necessario che l'app Chrome sia in esecuzione o aperta nel browser, anche se il browser Chrome deve essere in esecuzione. È inoltre possibile ottenere una vista consolidata di tutte le notifiche nella finestra delle notifiche di Chrome.

>[AZURE.NOTE]Non si tratta di una notifica push generica basata sul browser ed è specifica delle app Chrome. Per informazioni dettagliate, vedere [Informazioni generali sulle app Chrome]. Le app Chrome erano precedentemente note come "app in pacchetto" e sono diverse dalle più semplici "app ospitate". Per comprendere la differenza, vedere [App Web installabili]. Le app Chrome possono essere eseguite anche in dispositivi mobili (Android e iOS) tramite Apache Cordova. Per altre informazioni, vedere [App Chrome su dispositivi mobili].

In questa esercitazione si creerà un'app Chrome che riceve notifiche push tramite Google Cloud Messaging (GCM). Al termine dell'esercitazione sarà possibile trasmettere le notifiche push a tutti gli utenti Chrome che hanno installato questa app Chrome.

Questa esercitazione descrive le operazioni di base per abilitare le notifiche push:

* [Abilitare Google Cloud Messaging](#register)
* [Configurare l'hub di notifica](#configure-hub)
* [Connettere l'app Chrome all'hub di notifica](#connect-app)
* [Inviare una notifica all'app Chrome](#send)
* [Passaggi successivi](#next-steps)

Questa esercitazione illustra uno scenario di trasmissione semplice tramite hub di notifica. La configurazione di GCM e di Hub di notifica di Azure è identica alla configurazione per Android, perché [Google Cloud Messaging per Chrome] è stato ritirato e lo stesso GCM ora supporta sia dispositivi Android che istanze di Chrome.

Seguire anche le esercitazioni nella sezione "Passaggi successivi" per imparare a usare gli hub di notifica per rivolgersi a gruppi di dispositivi e utenti specifici.

>[AZURE.NOTE]Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

##<a id="register"></a>Abilitare Google Cloud Messaging

1. Passare al sito Web [Google Cloud Console], accedere con le credenziali dell'account Google e fare clic su **Create Project**. Inserire un valore appropriato nel campo **Project Name** e fare clic sul pulsante **Create**.

   	![][1]

2. Annotare il valore di **Project Number** nella pagina **Projects** per il progetto appena creato. Verrà usato come **GCM Sender ID** nell'app Chrome per la registrazione con GCM.

   	![][2]

3. Nel riquadro sinistro fare clic su **APIs & auth**, quindi scorrere verso il basso e fare clic sull'interruttore per abilitare **Google Cloud Messaging for Android**. Non è necessario abilitare **Google Cloud Messaging for Chrome**.

   	![][3]

4. Nel riquadro sinistro fare clic su **Credentials** > **Create New Key** > **Server Key** > **Create**.

   	![][4]

5. Prendere nota del valore **API Key** per il server. La chiave verrà successivamente configurata nell'hub di notifica, in modo che possa inviare notifiche push a GCM.

   	![][5]

##<a id="configure-hub"></a>Configurare l'hub di notifica

1. Accedere al [portale di Azure], quindi fare clic su **+ NUOVO** nella parte inferiore dello schermo.

2. Fare clic su **Servizi app** > **Bus di servizio** > **Hub di notifica** > **Creazione rapida**. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub di notifica**.

   	![][6]

4. Passare all'hub di notifica appena creato. Fare clic sullo spazio dei nomi in cui si trova l'hub di notifica, in genere ***nome hub di notifica*-ns**.

   	![][7]

5. Fare clic sulla scheda **Hub di notifica** in alto.

   	![][8]

6. Fare clic sulla scheda **Configura** in alto.

   	![][9]

7. Nella scheda **Configura** scorrere verso il basso fino a **Impostazioni di Google Cloud Messaging**, immettere il valore **Chiave API** ottenuto nella sezione precedente e fare clic su **Salva**.

   	![][10]

8. Selezionare la scheda **Dashboard** nella parte superiore, quindi fare clic su **Informazioni di connessione**.

   	![][11]

9. Prendere nota dei valori **DefaultListenSharedAccessSignature** (necessario nell'app Chrome per la registrazione nell'hub di notifica) e **DefaultFullSharedAccessSignature** (necessario per inviare notifiche).

   	![][12]

L'hub di notifica è ora configurato per l'uso con GCM e si dispone delle stringhe di connessione necessarie per procedere ulteriormente nella configurazione.

##<a id="connect-app"></a>Connettere l'app Chrome all'hub di notifica

###Creare una nuova app Chrome

L'esempio seguente è basato sul [modello GCM per app Chrome] e usa il metodo consigliato per creare un'app Chrome. Nelle sezioni seguenti verranno evidenziati i passaggi relativi all'Hub di notifica di Azure. È consigliabile scaricare l'origine dell'app Chrome dall'[esempio di hub di notifica per l'app Chrome].

L'app Chrome viene creata tramite JavaScript ed è possibile usare il proprio editor di testo preferito a questo scopo. L'app Chrome avrà un aspetto simile al seguente.

   	![][15]

2. Creare una cartella e denominarla **ChromePushApp** o scegliere un nome diverso.

3. Scaricare la **libreria cryto-js** dalla [libreria crypto-js] in questa cartella. Questa cartella della libreria contiene due sottocartelle: **components** e **rollups**.

4. Creare un file **manifest.json**. Tutte le app Chrome sono accompagnate da un file manifest che descrive i metadati dell'app e in particolare le autorizzazioni disponibili.

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}

	Notare l'elemento **permissions**, che specifica che l'app Chrome è in grado di ricevere notifiche push da GCM. È anche necessario specificare l'URI di Hub di notifica di Azure in cui l'app Chrome eseguirà una chiamata REST per la registrazione. Verrà usato il file di icona gcm\_128.png, disponibile nell'origine riutilizzata dal campione GCM originale. È possibile usare un'immagine qualsiasi.

5. Creare un file denominato **background.js** con il codice seguente:

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}

		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.

		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);

		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}

		var registerWindowCreated = false;

		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {

		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}

		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);

		// Set up listeners to trigger the first-time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Si tratta del file che visualizza il codice HTML della finestra dell'app Chrome (**register.html**) e che definisce anche il gestore **messageReceived** per la gestione della notifica push in arrivo.

6. Creare un file denominato **register.html** che definisce l'interfaccia utente dell'app Chrome. Si noti che questo esempio usa *CryptoJS v3.1.2*. Se è stata scaricata un'altra versione, correggere il percorso di script src.

		<html>

		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>

		<body>

		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>

		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

		<br/>

		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

		<br/>
		<br/>

		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

		</body>

		</html>

7. Creare un file denominato **register.js** con il codice seguente. Questo file specifica lo script alla base di **register.html**. Le app Chrome non consentono l'esecuzione in linea, quindi è necessario creare uno script separato per l'interfaccia utente.

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

		window.onload = function() {
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH;
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
		}

		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }

		  document.getElementById("console").innerHTML = currentStatus  + status;
		}

		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);

		  // Prevent register button from being clicked again before the registration finishes.
		  document.getElementById("registerWithGCM").disabled = true;
		}

		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;

		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }

		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;

		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}

		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}

		// From http://msdn.microsoft.com/library/dn495627.aspx
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";

		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });

		  originalUri = endpoint + hubName;
		}

		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration

		  // Set expiration in seconds.
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;

		  // Using CryptoJS.
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);

		  // Construct authorization string.
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}

		function sendNHRegistrationRequest()
		{
		  var registrationPayload =
		  "<?xml version="1.0" encoding="utf-8"?>" +
		  "<entry xmlns="http://www.w3.org/2005/Atom">" +
		      "<content type="application/xml">" +
		          "<GcmRegistrationDescription xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";

		  // Update the payload with the registration ID obtained earlier.
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();

		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };

		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }

		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");

		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	Nello script sopra riportato si notano i seguenti elementi:
	- *window.onload* definisce gli eventi avviati dai due pulsanti nell'interfaccia utente: uno esegue la registrazione con GCM e l'altro usa l'ID di registrazione restituito dopo la registrazione con GCM per effettuare la registrazione con Hub di notifica di Azure.
	- La funzione *updateLog* definisce una semplice funzione di registrazione.
	- *registerWithGCM* è il primo gestore degli eventi Click del pulsante che effettua la chiamata *chrome.gcm.register* a GCM per registrare questa istanza dell'app Chrome.
	- *registerCallback* è la funzione di richiamata che riceve la chiamata quando viene restituita la chiamata di registrazione GCM sopra menzionata.
	- *registerWithNH* è il secondo gestore degli eventi Click del pulsante che esegue la registrazione con Hub di notifica. Ottiene *hubName* e *connectionString* specificati dall'utente e avvia la chiamata all'API REST per la registrazione con Hub di notifica.
	- *splitConnectionString* e *generateSaSToken* sono un'implementazione Javascript della creazione di un token SaS da inviare in tutte le chiamate all'API REST. Altre informazioni sono disponibili alla pagina - http://msdn.microsoft.com/library/dn495627.aspx
	- *sendNHRegistrationRequest* è la funzione che effettua una chiamata HTTP REST.
	- *registrationPayload* definisce la registrazione del payload xml. Altre informazioni sono disponibili alla pagina - [Creare una registrazione - API REST di Hub di notifica]. L'ID della registrazione viene aggiornato con i dati ricevuti da GCM.
	- *client* è un'istanza di *XMLHttpRequest* usata per eseguire la richiesta POST HTTP. L'intestazione *Authorization* viene aggiornata con il token di firma di accesso condiviso. Il corretto completamento di questa chiamata registra questa istanza dell'app Chrome in Hub di notifica di Azure.


Al termine, per la cartella dovrebbe essere visualizzata la seguente vista: ![][21]

###Configurare e testare l'app Chrome

1. Aprire il browser Chrome. Aprire le **estensioni Chrome** e abilitare la **Modalità sviluppatore**.

   	![][16]

2. Fare clic su **Load unpacked extension** e selezionare la cartella in cui sono stati creati i file. È facoltativamente possibile usare anche lo **strumento per sviluppatori di estensioni e app Chrome**. Questo strumento è a sua volta un'app Chrome (installata da Chrome Web Store) e fornisce capacità di debug avanzate per lo sviluppo di app Chrome.

   	![][17]

3. Se l'app Chrome viene creata senza errori, verrà visualizzata.

   	![][18]

4. Immettere il valore **Project Number** ottenuto in precedenza da **Google Cloud Console** come ID del mittente e fare clic su **Register with GCM**. Verrà visualizzato un messaggio **Registration with GCM succeeded**.

   	![][19]

5. Immettere i valori **Notification Hub Name** e **DefaultListenSharedAccessSignature** ottenuti in precedenza dal portale di Azure, quindi fare clic su **Register with Azure Notification Hub**. Verranno visualizzati il messaggio **Notification Hub Registration successful!** e i dettagli della risposta della registrazione, che contiene l'ID di registrazione di Hub di notifica di Azure.

   	![][20]

##<a name="send"></a>Inviare una notifica all'app Chrome

In questa esercitazione vengono inviate notifiche con un'applicazione console .NET. È tuttavia possibile inviare notifiche usando Hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a>.

Per un esempio di invio di notifiche da un back-end di Servizi mobili di Azure integrato con Hub di notifica, vedere "Introduzione alle notifiche push in Servizi mobili" ([Back-end .NET](../mobile-services-javascript-backend-android-get-started-push.md) | [Back-end JavaScript](../mobile-services-javascript-backend-android-get-started-push.md)). Per un esempio relativo all'invio di notifiche tramite le API REST, vedere "Come usare Hub di notifica da Java/PHP/Python" ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md)).

1. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**. In **Visual C#** fare clic su **Windows** e **Applicazione console**, quindi su **OK**. Verrà creato un nuovo progetto di applicazione console.

2. Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**. Verrà visualizzata la console di Gestione pacchetti.

3. Nella finestra della console eseguire il comando seguente:

        Install-Package WindowsAzure.ServiceBus

   	Viene aggiunto un riferimento ad Azure Service Bus SDK con il <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">pacchetto NuGet WindowsAzure.ServiceBus</a>.

4. Aprire il file **Program.cs** e aggiungere l'istruzione `using` seguente:

        using Microsoft.ServiceBus.Notifications;

5. Nella classe **Program** aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{"data":{"message":"Hello Chrome from Azure Notification Hubs"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Assicurarsi di sostituire il segnaposto relativo al *nome dell'hub* con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale. Sostituire anche il segnaposto relativo alla stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** ottenuta nella sezione "Configurazione dell'hub di notifica".

	>[AZURE.NOTE]Assicurarsi di usare la stringa di connessione con accesso **Full**, non con accesso **Listen**. La stringa con accesso di tipo **Attesa** non ha infatti le autorizzazioni per l'invio di notifiche.

5. Aggiungere le righe seguenti nel metodo **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

6. Assicurarsi che il browser Chrome sia aperto. Non è necessario che l'app Chrome sia aperta. Verrà visualizzato il seguente popup di notifica sul desktop.

   	![][13]

7. È anche possibile visualizzare tutte le notifiche tramite la finestra delle notifiche di Chrome dalla barra delle applicazioni (in Windows) quando Chrome è in esecuzione.

   	![][14]

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse all'app Chrome. Per altre informazioni sugli hub di notifica, vedere [Panoramica dell'Hub di notifica]. Per rivolgersi a utenti specifici, vedere l'esercitazione [Uso di Hub di notifica di Azure per inviare notifiche agli utenti]. Per segmentare gli utenti in base ai gruppi di interesse, vedere [Uso di Hub di notifica per inviare le ultime notizie].

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[esempio di hub di notifica per l'app Chrome]: http://google.com
[Google Cloud Console]: http://cloud.google.com/console
[portale di Azure]: https://manage.windowsazure.com/
[Panoramica dell'Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Informazioni generali sulle app Chrome]: https://developer.chrome.com/apps/about_apps
[modello GCM per app Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[App Web installabili]: https://developers.google.com/chrome/apps/docs/
[App Chrome su dispositivi mobili]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Creare una registrazione dell'API REST per l'Hub di notifica]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[libreria crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging per Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Uso di Hub di notifica di Azure per inviare notifiche agli utenti]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Uso di Hub di notifica per inviare le ultime notizie]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!---HONumber=Sept15_HO2-->