---
title: Aggiungere notifiche push all'app Apache Cordova con App per dispositivi mobili di Azure | Documentazione Microsoft
description: Informazioni su come usare App per dispositivi mobili di Azure per inviare notifiche push all'app Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
manager: syntaxc4
editor: 
author: ggailey777
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: dc3cab0a6a8b4a56ab0fba1a02e5bba9d0ed1b1f
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Aggiungere notifiche push all'app Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione vengono aggiunte notifiche push al progetto di [avvio rapido di Apache Cordova], in modo che una notifica push venga inviata al dispositivo a ogni inserimento di record.

Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push. Per altre informazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure][1].

## <a name="prerequisites"></a>Prerequisiti
Questa esercitazione illustra un'applicazione Apache Cordova sviluppata in Visual Studio 2015 e in esecuzione nell'emulatore Android di Google, in un dispositivo Android, un dispositivo Windows e un dispositivo iOS.

Per completare questa esercitazione, sono necessari:

* Un computer in cui è installato [Visual Studio Community 2015][2] o versione successiva.
* [Visual Studio Tools per Apache Cordova][4].
* Un [account Azure attivo][3].
* Un progetto di [avvio rapido di Apache Cordova][5] completato.
* (Android) Un [account Google][6] con un indirizzo di posta elettronica verificato.
* (iOS) [Iscrizione all'Apple Developer Program][7] e un dispositivo iOS. Il simulatore iOS non supporta le notifiche push.
* (Windows) Un [account per sviluppatore di Windows Store][8] e un dispositivo Windows 10.

## <a name="configure-hub"></a>Configurare un hub di notifica
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Guardare un video che illustra la procedura disponibile in questa sezione][9]

## <a name="update-the-server-project"></a>Aggiornare il progetto server
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modificare l'app Cordova
Assicurarsi che il progetto app Apache Cordova sia pronto per la gestione delle notifiche push installando il plug-in di push di Cordova ed eventuali servizi push specifici della piattaforma.

#### <a name="update-the-cordova-version-in-your-project"></a>Aggiornare la versione di Cordova nel progetto.
Se il progetto utilizza una versione di Apache Cordova precedente alla v6.1.1, aggiornare il progetto client. Per aggiornare il progetto:

* Fare clic con il pulsante destro su `config.xml` per aprire la finestra di progettazione configurazione.
* Selezionare la scheda Piattaforme.
* Scegliere 6.1.1 nella casella di testo **Cordova CLI**.
* Scegliere **Compila**, quindi **Compila soluzione** per aggiornare il progetto.

#### <a name="install-the-push-plugin"></a>Installare il plug-in di push
Le applicazioni Apache Cordova non gestiscono in modo nativo le funzionalità del dispositivo o della rete.  Queste funzionalità sono incluse nei plug-in che vengono pubblicati in [npm][10] o GitHub.  Il plug-in `phonegap-plugin-push` viene usato per gestire le notifiche push di rete.

È possibile installare il plug-in di push in uno dei modi seguenti:

**Dal prompt dei comandi:**

Eseguire il comando seguente:

    cordova plugin add phonegap-plugin-push

**Da Visual Studio:**

1. In Esplora soluzioni aprire il file `config.xml`, fare clic su **Plug-in**>**Personalizza**, selezionare **Git** come origine dell'installazione e quindi immettere `https://github.com/phonegap/phonegap-plugin-push` come origine.

   ![][img1]

2. Fare clic sulla freccia accanto all'origine dell'installazione.
3. In **SENDER_ID** è possibile aggiungere l'ID numerico del progetto della Console per gli sviluppatori di Google, se l'ID è già disponibile. In caso contrario, immettere un valore segnaposto, ad esempio 777777.  Se la destinazione è Android, sarà possibile aggiornare questo valore nel file config.xml in un secondo momento.
4. Fare clic su **Aggiungi**.

Il plug-in di push è stato installato.

#### <a name="install-the-device-plugin"></a>Installare il plug-in del dispositivo
Seguire la stessa procedura utilizzata per installare il plug-in di push.  Aggiungere il plug-in del dispositivo dall'elenco di plug-in di base (fare clic su **Plug-in** > **Base** per trovarlo). Questo plug-in è necessario per ottenere il nome della piattaforma.

#### <a name="register-your-device-on-application-start-up"></a>Registrare il dispositivo all'avvio dell'applicazione
Viene inizialmente incluso un codice minimo per Android. Modificare quindi l'applicazione per eseguirla su iOS o Windows 10.

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

    Questo esempio illustra la chiamata a **registerForPushNotifications** al termine dell'autenticazione.  È possibile chiamare `registerForPushNotifications()` ogni qualvolta è necessario.

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
3. (Android) Nel codice precedente sostituire `Your_Project_ID` con l'ID progetto numerico dell'app indicato nella [Console per gli sviluppatori di Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Facoltativo) Configurare ed eseguire l'app in Android
Completare questa sezione per abilitare le notifiche push per Android.

#### <a name="enable-gcm"></a>Abilitare Firebase Cloud Messaging
Poiché la destinazione iniziale è la piattaforma di Google Android, è necessario abilitare Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Configurare il back-end dell'app per dispositivi mobili per inviare richieste push usando FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Configurare l'app Cordova per Android
Nell'app Cordova aprire il file config.xml e sostituire `Your_Project_ID` con l'ID progetto numerico dell'app indicato nella [Console per gli sviluppatori di Google][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Aprire index.js e aggiornare il codice per usare l'ID progetto numerico.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Configurare il dispositivo Android per il debug USB
Prima di poter distribuire l'applicazione al dispositivo Android, è necessario abilitare il debug USB.  Sul telefono Android seguire questa procedura:

1. Passare a **Settings** (Impostazioni)>**About phone** (Info sul dispositivo) e toccare **Build number** (Numero build) circa sette volte finché non sarà abilitata la modalità sviluppatore.
2. Tornare in **Settings** (Impostazioni)>**Developer Options** (Opzioni per gli sviluppatori), abilitare **USB debugging** (Debug USB), quindi connettere lo smartphone Android al PC di sviluppo con un cavo USB.

Per questo test è stato usato un dispositivo Google Nexus 5X con Android 6.0 (Marshmallow).  Tuttavia, le tecniche sono comuni a qualsiasi versione moderna di Android.

#### <a name="install-google-play-services"></a>Installare servizi Google Play
Il plug-in di push si basa su servizi Google Play Android per notifiche push.

1. In Visual Studio fare clic su **Strumenti**>**Android**>**Android SDK Manager**, espandere la cartella **Funzionalità aggiuntive** e selezionare la casella per assicurarsi che tutti gli SDK seguenti siano installati.

   * Android 2.3 o versione successiva
   * Google Repository 27 o versione successiva
   * Google Play Services 9.0.2 o versione successiva

2. Fare clic su **Install Packages** (Installa pacchetti) e attendere il completamento dell'installazione.

Le librerie attualmente necessarie sono elencate nella [documentazione relativa all'installazione di phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testare le notifiche push nell'app in Android
A questo punto è possibile testare le notifiche push. Eseguire l'applicazione e inserire elementi nella tabella TodoItem usando lo stesso dispositivo oppure un altro dispositivo, purché il back-end sia lo stesso. Testare l'app Cordova sulla piattaforma Android in uno dei modi seguenti:

* **In un dispositivo fisico:** collegare il dispositivo Android al computer di sviluppo con un cavo USB.  Invece di selezionare **Emulatore Android di Google**, selezionare **Dispositivo**. Visual Studio distribuisce l'applicazione nel dispositivo e quindi la esegue.  Sarà quindi possibile interagire con l'applicazione dal dispositivo.

  Migliorare l'esperienza di sviluppo.  Le applicazioni per la condivisione dello schermo come [Mobizen][20] possono facilitare lo sviluppo di un'applicazione Android.  Mobizen proietta lo schermo Android su un Web browser sul PC.

* **In un emulatore Android:** sono necessari altri passaggi di configurazione per l'esecuzione in un emulatore.

    Assicurarsi di distribuire su un dispositivo virtuale che abbia le API Google impostate come destinazione, come illustrato di seguito nel gestore del dispositivo virtuale Android (AVD).

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Se si vuole usare un emulatore x86 più veloce, [installare il driver HAXM][11] e configurare l'emulatore per usarlo.

    Aggiungere un account Google al dispositivo Android facendo clic su **Apps** (App) > **Settings** (Impostazioni) > **Add account** (Aggiungi account), quindi seguire le istruzioni.

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Eseguire l'app todolist come fatto in precedenza e inserire un nuovo elemento todo. Questa volta, viene visualizzata un'icona di notifica nell'area di notifica. È possibile aprire la cassetta della notifica per visualizzare il testo completo della notifica.

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Facoltativo) Configurare ed eseguire l'app in iOS
Questa sezione illustra l'esecuzione del progetto Cordova in dispositivi iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Installare ed eseguire l'agente remotebuild per iOS in un computer Mac o un servizio cloud
Prima di eseguire un'app Cordova in iOS usando Visual Studio, seguire i passaggi nella [guida alla configurazione per iOS][12] per installare ed eseguire l'agente remotebuild.

Verificare che sia possibile compilare l'app per iOS. I passaggi indicati nella guida alla configurazione sono necessari per la compilazione per iOS da Visual Studio. Se non è disponibile un computer Mac, è possibile eseguire la compilazione per iOS usando l'agente remotebuild in un servizio come MacInCloud. Per altre informazioni, vedere [Eseguire l'app iOS nel cloud][21].

> [!NOTE]
> XCode 7 o versione successiva è necessario per usare il plug-in di push in iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Trovare l'ID da usare come ID app
Prima di registrare l'app per le notifiche push, aprire il file config.xml nell'app Cordova, trovare il valore dell'attributo `id` nell'elemento widget e copiarlo per usarlo in un secondo momento. Nel codice XML seguente, l'ID è `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Questo identificatore verrà usato in un secondo momento, durante la creazione di un ID app nel portale per sviluppatori di Apple. Se si crea un ID app diverso nel portale per sviluppatori, è necessario eseguire alcuni passaggi aggiuntivi più avanti in questa esercitazione. L'ID nell'elemento widget deve corrispondere all'ID app indicato nel portale per sviluppatori.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrare l'app per le notifiche push nel portale per sviluppatori Apple
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Guardare un video che illustra una procedura simile](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Configurare Azure per l'invio di notifiche push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Verificare che l'ID app corrisponda all'app Cordova
Se l'ID app creato nell'account per sviluppatori Apple corrisponde già all'ID dell'elemento widget nel file config.xml, è possibile ignorare questo passaggio. Se tuttavia gli ID non corrispondono, procedere come segue:

1. Eliminare la cartella platforms dal progetto.
2. Eliminare la cartella plugins dal progetto.
3. Eliminare la cartella node_modules dal progetto.
4. Aggiornare l'attributo id dell'elemento widget nel file config.xml per usare l'ID app creato nell'account per sviluppatori Apple.
5. Ricompilare il progetto.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testare le notifiche push nell'app iOS
1. In Visual Studio verificare che sia selezionato **iOS** come destinazione di distribuzione e quindi scegliere **Dispositivo** per l'esecuzione nel dispositivo iOS connesso.

    È possibile eseguire il progetto in un dispositivo iOS connesso al PC usando iTunes. Il simulatore iOS non supporta le notifiche push.

2. Scegliere **Esegui** o premere **F5** per compilare il progetto e avviare l'app in un dispositivo iOS, quindi fare clic su **OK** per accettare le notifiche push.

   > [!NOTE]
   > L'app richiede una conferma per le notifiche push durante la prima esecuzione.

3. Nell'app digitare un'attività e fare clic sull'icona con il segno PIÙ (+).
4. Verificare che venga ricevuta una notifica, quindi fare clic su OK per ignorarla.

## <a name="optional-configure-and-run-on-windows"></a>(Facoltativo) Configurare ed eseguire l'app in Windows
Questa sezione descrive l'esecuzione del progetto di un'app Apache Cordova nei dispositivi Windows 10. Il plug-in di push PhoneGap è supportato in Windows 10. Se non si usano dispositivi Windows, è possibile ignorare questa sezione.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrare l'app di Windows per le notifiche push con WNS
Per usare le opzioni di Store in Visual Studio, selezionare una destinazione Windows dall'elenco Piattaforme soluzione, ad esempio **Windows-x64** o **Windows-x86**. Evitare **Windows-AnyCPU** per le notifiche push.

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Guardare un video che illustra una procedura simile][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurare l'hub di notifica per WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurare l'app Cordova per il supporto delle notifiche push di Windows
Aprire Progettazione configurazione facendo clic con il pulsante destro del mouse sul file config.xml e scegliendo **Visualizza finestra di progettazione**. Selezionare quindi la scheda **Windows** e scegliere **Windows 10** in **Versione Windows di destinazione**.

Per supportare le notifiche push nelle compilazioni predefinite (debug), aprire il file build.json. Copiare la configurazione "release" nella configurazione di debug.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Dopo l'aggiornamento, il file build.json deve contenere il codice seguente:

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

#### <a name="test-push-notifications-in-your-windows-app"></a>Testare le notifiche push nell'app di Windows
In Visual Studio verificare che sia selezionata una piattaforma Windows come destinazione di distribuzione, ad esempio **Windows-x64** o **Windows-x86**. Per eseguire l'app in un PC con Windows 10 che ospita Visual Studio, scegliere **Computer locale**.

Premere il pulsante Esegui per compilare il progetto e avviare l'app.

Nell'app digitare un nome per un nuovo elemento todoitem, quindi fare clic sull'icona del segno più (+) per aggiungerlo.

Assicurarsi di ricevere una notifica quando viene aggiunto l'elemento.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulle notifiche push, vedere [Hub di notifica di Azure][17].
* Se non è già stato fatto, proseguire con l'esercitazione [aggiungendo l'autenticazione][14] all'app Apache Cordova.

Informazioni su come usare gli SDK.

* [Apache Cordova SDK][15]
* [ASP.NET Server SDK][1]
* [Node.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/

