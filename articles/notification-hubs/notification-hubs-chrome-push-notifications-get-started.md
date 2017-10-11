---
title: Inviare notifiche push alle app Chrome con Hub di notifica di Azure | Documentazione di Microsoft
description: Informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'app Chrome.
services: notification-hubs
keywords: notifiche push per dispositivi mobili,notifiche push,notifica push, notifiche push per chrome
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 600b1b7e5f3987c9a0acc33b7049f7118442b931
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Inviare notifiche push alle app Chrome con Hub di notifica di Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Questo argomento illustra come usare Hub di notifica per inviare notifiche push a un'app Chrome, che saranno visualizzate nel contesto del browser Google Chrome. In questa esercitazione si creerà un'app Chrome che riceve notifiche push tramite [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). 

> [!NOTE]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).
> 
> 

Questa esercitazione descrive le operazioni di base per abilitare le notifiche push:

* [Abilitare Google Cloud Messaging](#register)
* [Configurare l'hub di notifica](#configure-hub)
* [Connettere l'app Chrome all'hub di notifica](#connect-app)
* [Inviare una notifica push all'app Chrome](#send)
* [Altre funzionalità e capacità](#next-steps)

> [!NOTE]
> Le notifiche push alle app Chrome non sono notifiche generiche all'interno del browser, ma sono specifiche del modello di estendibilità del browser. Per informazioni dettagliate, vedere l'articolo che fornisce una [panoramica delle app Chrome]. Oltre che in browser desktop, le app Chrome vengono eseguite su dispositivi mobili (iOS e Android) tramite Apache Cordova. Per altre informazioni, vedere [App Chrome su dispositivi mobili].
> 
> 

La configurazione di GCM e di Hub di notifica di Azure è identica alla configurazione per Android, perché [Google Cloud Messaging per Chrome] è stato ritirato e lo stesso GCM ora supporta sia dispositivi Android che istanze di Chrome.

## <a id="register"></a>Abilitare Google Cloud Messaging
1. Passare al sito Web [Google Cloud Console] , accedere con le credenziali dell'account Google e fare clic su **Create Project** . Inserire un valore appropriato nel campo **Nome progetto** e fare clic sul pulsante **Crea**.
   
       ![Google Cloud Console - Create Project][1]
2. Annotare il valore di **Numero progetto** nella pagina **Progetti** per il progetto appena creato. Verrà usato come **GCM Sender ID** nell'app Chrome per la registrazione con GCM.
   
       ![Google Cloud Console - Project Number][2]
3. Nel riquadro sinistro fare clic su **APIs & auth** (API e autenticazione), quindi scorrere verso il basso e fare clic sull'interruttore per abilitare **Google Cloud Messaging per Android**. Non è necessario abilitare **Google Cloud Messaging for Chrome**.
   
       ![Google Cloud Console - Server Key][3]
4. Nel riquadro a sinistra fare clic su **Credenziali** > **Crea nuova chiave** > **Chiave server** > **Crea**.
   
       ![Google Cloud Console - Credentials][4]
5. Prendere nota del valore **API Key**per il server. La chiave verrà successivamente configurata nell'hub di notifica, in modo che possa inviare notifiche push a GCM.
   
       ![Google Cloud Console - API Key][5]

## <a id="configure-hub"></a>Configurare l'hub di notifica
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6.   Nel pannello **Impostazioni** selezionare **Servizi di notifica**, quindi **Google (GCM)**. Immettere la chiave API e salvare.

&emsp;&emsp;![Hub di notifica di Azure - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

## <a id="connect-app"></a>Connettere l'app Chrome all'hub di notifica
A questo punto,l'hub di notifica è configurato per l'uso con GCM e sono disponibili le stringhe di connessione per registrare l'app per l'invio e la ricezione di notifiche push. LK

### <a name="create-a-new-chrome-app"></a>Creare una nuova app Chrome
L'esempio seguente è basato sull' [esempio GCM per app Chrome] e usa il metodo consigliato per creare un'app Chrome. Le sezioni seguenti evidenziano i passaggi relativi all'Hub di notifica di Azure. 

> [!NOTE]
> È consigliabile scaricare l'origine dell'app Chrome dall' [esempio di hub di notifica per l'app Chrome].
> 
> 

L'app Chrome viene creata tramite JavaScript ed è possibile usare il proprio editor di testo preferito a questo scopo. L'app Chrome avrà un aspetto simile al seguente.

![App Google Chrome][15]

1. Creare una cartella e denominarla `ChromePushApp`. Naturalmente, il nome è arbitrario. Se si assegnano nomi diversi, assicurarsi di sostituire il percorso nei segmenti di codice necessari.
2. Scaricare la [libreria crypto-js] nella cartella creata nel secondo passaggio. Questa cartella della libreria contiene due sottocartelle: `components` e `rollups`.
3. Creare un file `manifest.json` . Tutte le app Chrome sono accompagnate da un file manifesto che contiene i metadati dell'app e, più importante, tutte le autorizzazioni concesse all'applicazione quando viene installata dall'utente.
   
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
   
    Si noti l'elemento `permissions` , che specifica che l'app Chrome può ricevere notifiche push da GCM. È anche necessario specificare l'URI di Hub di notifica di Azure in cui l'app Chrome eseguirà una chiamata REST per la registrazione.
    L'esempio usa anche un file di icona, `gcm_128.png`, disponibile nell'origine riutilizzata dall'esempio GCM originale. È possibile sostituirlo con qualsiasi immagine che soddisfa i [criteri relativi alle icone](https://developer.chrome.com/apps/manifest/icons).
4. Creare un file denominato `background.js` con il codice seguente:
   
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
5. Creare un file denominato `register.html` che definisce l'interfaccia utente dell'app Chrome. 
   
   > [!NOTE]
   > Questo esempio usa **CryptoJS v3.1.2**. Se è stata scaricata un'altra versione della libreria, assicurarsi di sostituire correttamente la versione nel percorso `src` .
   > 
   > 
   
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
6. Creare un file denominato `register.js` con il codice seguente. Questo file specifica lo script di base di `register.html`. Le app Chrome non consentono l'esecuzione in linea, quindi è necessario creare uno script separato per l'interfaccia utente.
   
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
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
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
   
    Lo script precedente include i parametri seguenti:
   
   * **window.onload** definisce gli eventi clic dei due pulsanti nell'interfaccia utente. Uno effettua la registrazione con GCM e l'altro usa l'ID della registrazione restituito dopo la registrazione con GCM per effettuare la registrazione all'Hub di notifica di Azure.
   * **updateLog** è la funzione che consente di gestire semplici funzionalità di registrazione.
   * **registerWithGCM** è il primo gestore di eventi clic del pulsante che esegue la chiamata `chrome.gcm.register` a GCM per registrare questa istanza corrente dell'app Chrome.
   * **registerCallback** è la funzione di callback che riceve la chiamata quando viene restituita la chiamata di registrazione GCM.
   * **registerWithNH** è il secondo gestore di eventi clic del pulsante che esegue la registrazione con Hub di notifica. Ottiene `hubName` e `connectionString` specificati dall'utente e crea la chiamata all'API REST per la registrazione con Hub di notifica.
   * **splitConnectionString** e **generateSaSToken** sono helper che rappresentano l'implementazione JavaScript del processo di creazione di un token di firma di accesso condiviso da usare in tutte le chiamate all'API REST. Per altre informazioni, vedere [Concetti comuni](http://msdn.microsoft.com/library/dn495627.aspx).
   * **sendNHRegistrationRequest** è la funzione che esegue una chiamata REST HTTP ad Hub di notifica di Azure.
   * **registrationPayload** definisce il payload XML di registrazione. Per altre informazioni, vedere [Creare una registrazione dell'API REST per l'Hub di notifica]. L'ID della registrazione viene aggiornato con i dati ricevuti da GCM.
   * **client** è un'istanza di **XMLHttpRequest** usata per eseguire la richiesta HTTP POST. L'intestazione `Authorization` viene aggiornata con `sasToken`. Il corretto completamento di questa chiamata registra questa istanza dell'app Chrome in Hub di notifica di Azure.

La struttura di cartelle complessiva per questo progetto sarà analoga alla seguente: ![App di Google Chrome - Struttura cartelle][21]

### <a name="set-up-and-test-your-chrome-app"></a>Configurare e testare l'app Chrome
1. Aprire il browser Chrome. Aprire le **estensioni Chrome** e abilitare la **Modalità sviluppatore**.
   
       ![Google Chrome - Enable Developer Mode][16]
2. Fare clic su **Load unpacked extension** e selezionare la cartella in cui sono stati creati i file. È facoltativamente possibile usare anche lo **strumento per sviluppatori di estensioni e app Chrome**. Questo strumento è a sua volta un'app Chrome (installata da Chrome Web Store) e fornisce capacità di debug avanzate per lo sviluppo di app Chrome.
   
       ![Google Chrome - Load Unpacked Extension][17]
3. Se l'app Chrome viene creata senza errori, verrà visualizzata.
   
       ![Google Chrome - Chrome App Display][18]
4. Immettere il valore **Nome progetto** ottenuto in precedenza da **Google Cloud Console** come ID del mittente e fare clic su **Register with GCM** (Registra con GCM). Verrà visualizzato un messaggio **Registration with GCM succeeded**
   
       ![Google Chrome - Chrome App Customization][19]
5. Immettere i valori **Nome hub di notifica** e **DefaultListenSharedAccessSignature** ottenuti in precedenza dal portale, quindi fare clic su **Register with Azure Notification Hub** (Registra con l'hub di notifica di Azure). Verrà visualizzato un messaggio **Notification Hub Registration successful!** e i dettagli della risposta della registrazione, che contiene l'ID di registrazione di Hub di notifica di Azure.
   
       ![Google Chrome - Specify Notification Hub Details][20]  

## <a name="send"></a>Inviare una notifica all'app Chrome
A scopo di test, verranno inviate notifiche push a Chrome usando un'applicazione console .NET. 

> [!NOTE]
> È possibile inviare notifiche push con Hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a> pubblica. Consultare il [portale di documentazione](https://azure.microsoft.com/documentation/services/notification-hubs/) per altri esempi multipiattaforma.
> 
> 

1. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**. In **Visual C#** fare clic su **Windows** e **Applicazione console**, quindi selezionare **OK**.  Verrà creato un nuovo progetto di applicazione console.
2. Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**. Verrà visualizzata la console di Gestione pacchetti.
3. Nella finestra della console eseguire il comando seguente:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
       This adds a reference to the Azure Service Bus SDK with the <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>.
4. Aprire `Program.cs` e aggiungere l'istruzione `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. Nella classe `Program` aggiungere il metodo seguente.
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
       Make sure to replace the `<hub name>` placeholder with the name of the notification hub that appears in the [portal](https://portal.azure.com) in your Notification Hub blade. Also, replace the connection string placeholder with the connection string called `DefaultFullSharedAccessSignature` that you obtained in the notification hub configuration section.
   
   > [!NOTE]
   > Assicurarsi di usare la stringa di connessione con accesso **Full**, non con accesso **Listen**. La stringa di connessione di accesso di tipo **Listen** non ha le autorizzazioni per l'invio di notifiche push.
   > 
   > 
6. Aggiungere le chiamate seguenti al metodo `Main` :
   
         SendNotificationAsync();
         Console.ReadLine();
7. Assicurarsi che Chrome sia in esecuzione ed eseguire l'applicazione console.
8. Verrà visualizzato il seguente popup di notifica sul desktop.
   
       ![Google Chrome - Notification][13]
9. È anche possibile visualizzare tutte le notifiche tramite la finestra delle notifiche di Chrome sulla barra delle applicazioni (in Windows) quando Chrome è in esecuzione.
   
       ![Google Chrome - Notifications List][14]

> [!NOTE]
> Non è necessario che l'app Chrome sia in esecuzione o aperta nel browser, anche se il browser Chrome deve essere in esecuzione. È inoltre possibile ottenere una vista consolidata di tutte le notifiche nella finestra delle notifiche di Chrome.
> 
> 

## <a name="next-steps"> </a>Passaggi successivi
Per altre informazioni sugli hub di notifica, vedere [Panoramica dell'Hub di notifica].

Per rivolgersi a utenti specifici, vedere l'esercitazione [Uso di Hub di notifica di Azure per inviare notifiche agli utenti] . 

Per segmentare gli utenti in base ai gruppi di interesse, eseguire l'esercitazione [Uso di Hub di notifica per inviare le ultime notizie] .

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
[esempio di hub di notifica per l'app Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Google Cloud Console]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Panoramica dell'Hub di notifica]: notification-hubs-push-notification-overview.md
[panoramica delle app Chrome]: https://developer.chrome.com/apps/about_apps
[esempio GCM per app Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[App Chrome su dispositivi mobili]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Creare una registrazione dell'API REST per l'Hub di notifica]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[libreria crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging per Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Uso di Hub di notifica di Azure per inviare notifiche agli utenti]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Uso di Hub di notifica per inviare le ultime notizie]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
