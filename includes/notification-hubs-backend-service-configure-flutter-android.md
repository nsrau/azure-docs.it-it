---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156330"
---
### <a name="add-the-google-services-json-file"></a>Aggiungere il file JSON di Google Services

1. Premere **CTRL** + **clic** sulla cartella **Android**, quindi scegliere **Apri in Android Studio**. Passare quindi alla visualizzazione **Progetto**, se non è già stato fatto.

1. Individuare il file *google-services.json* scaricato in precedenza quando è stato configurato il progetto **PushDemo** nella [console Firebase](https://console.firebase.google.com). Trascinarlo quindi nella directory radice del modulo **app** (**android** > **android** > **app**).

### <a name="configure-build-settings-and-permissions"></a>Configurare le impostazioni e le autorizzazioni di compilazione

1. Impostare la visualizzazione**Progetto** su **Android**.

1. Aprire il file **AndroidManifest.xml**, quindi aggiungere le autorizzazioni **INTERNET** e **READ_PHONE_STATE** dopo l'elemento **application** prima del tag di chiusura **</manifest>** .

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Aggiungere gli SDK di Firebase

1. In **Android Studio** aprire il file **build.grade** a livello di progetto (**Gradle Scripts** > **build.gradle (Project: android)** ) e assicurarsi che il nodo ``buildscript`` > **dependencies** contenga il classpath 'com.google.gms:google-services'.

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Assicurarsi di fare riferimento alla versione più recente in base alle istruzioni fornite nella [console Firebase](https://console.firebase.google.com) quando è stato creato il **progetto Android**.

1. Nel file **build.gradle** a livello di app (**Gradle Scripts** > **build.gradle (Module: app)** ) applicare il **plug-in Gradle di Google Services**. Applicare il plug-in direttamente sopra al nodo **Android**.

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. Nello stesso file, nel nodo **dependencies**, aggiungere la dipendenza per la libreria **Cloud Messaging** Android.

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > Assicurarsi di fare riferimento alla versione più recente in base alla [documentazione del client Cloud Messaging Android](https://firebase.google.com/docs/cloud-messaging/android/client).

1. Salvare le modifiche, quindi fare clic sul pulsante **Sincronizza ora** (dal prompt della barra degli strumenti) o su **Sync Project with Gradle Files** (Sincronizza progetto con file Gradle).

### <a name="handle-push-notifications-for-android"></a>Gestire le notifiche push per Android

1. In **Android Studio** premere **CTRL** + **clic** sulla cartella del pacchetto **com.<your_organization>.pushdemo** (**app** > **src** > **main** > **kotlin**), quindi scegliere **Package** (Pacchetto) dal menu **New** (Nuovo). Immettere **services** come nome, quindi premere **INVIO**.

1. Premere **CTRL** + **clic** sulla cartella **services** e scegliere il **file/classe Kotlin** dal menu **New**. Immettere **DeviceInstallationService** come nome, quindi premere **INVIO**.

1. Implementare **DeviceInstallationService** usando il codice seguente.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Questa classe implementa la controparte specifica della piattaforma per il canale `com.<your_organization>.pushdemo/deviceinstallation`. È stata definita nella parte Flutter dell'app all'interno di **DeviceInstallationService.dart**. In questo caso, le chiamate vengono effettuate dal codice comune all'host nativo. Assicurarsi di sostituire **<your_organization>** con il nome della propria organizzazione ogni volta viene usato.
    >
    > Questa classe fornisce un ID univoco (con [Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) come parte del payload di registrazione dell'hub di notifica.

1. Aggiungere alla cartella **services** un altro **file/classe Kotlin** denominato *NotificationRegistrationService*, quindi aggiungere il codice seguente.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Questa classe implementa la controparte specifica della piattaforma per il canale `com.<your_organization>.pushdemo/notificationregistration`. È stata definita nella parte Flutter dell'app all'interno di **NotificationRegistrationService.dart**. In questo caso, le chiamate vengono effettuate dall'host nativo al codice comune. Assicurarsi anche in questo caso di sostituire **<your_organization>** con il nome della propria organizzazione ogni volta viene usato.

1. Aggiungere alla cartella **services** un altro **file/classe Kotlin** denominato *NotificationActionService*, quindi aggiungere il codice seguente.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Questa classe implementa la controparte specifica della piattaforma per il canale `com.<your_organization>.pushdemo/notificationaction`. È stata definita nella parte Flutter dell'app all'interno di **NotificationActionService.dart**. In questo caso le chiamate possono essere effettuate in entrambe le direzioni. Assicurarsi di sostituire **<your_organization>** con il nome della propria organizzazione ogni volta viene usato.

1. Aggiungere nel pacchetto **com.<your_organization>.pushdemo** un nuovo **file/classe Kotlin** denominato *PushNotificationsFirebaseMessagingService*, quindi implementare con il codice seguente.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Questa classe è responsabile della gestione delle notifiche quando l'app è in esecuzione in primo piano. Chiama in modo condizionale **triggerAction** in **NotificationActionService** se è inclusa un'azione nel payload di notifica ricevuto in **onMessageReceived**. Verrà anche effettuata una chiamata a **refreshRegistration** in **NotificationRegistrationService** quando il token **Firebase** viene rigenerato eseguendo l'override della funzione **onNewToken**.
    >
    > Assicurarsi anche in questo caso di sostituire **<your_organization>** con il nome della propria organizzazione ogni volta viene usato.

1. In **AndroidManifest.xml** (**app** > **src** > **main**) aggiungere **PushNotificationsFirebaseMessagingService** alla fine dell'elemento **application** con il filtro di finalità `com.google.firebase.MESSAGING_EVENT`.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. In **DeviceInstallationService** verificare che le istruzioni import seguenti siano presenti all'inizio del file.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Sostituire **<your_organization>** con il nome della propria organizzazione.

1. Aggiornare il testo segnaposto *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* per ottenere il valore del token da **PushNotificationFirebaseMessagingService**.

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. In **MainActivity** verificare che le istruzioni import seguenti siano presenti all'inizio del file.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Sostituire **<your_organization>** con il nome della propria organizzazione.

1. Aggiungere una variabile per archiviare un riferimento a **DeviceInstallationService**.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Aggiungere una funzione denominata **processNotificationActions** per verificare se un determinato elemento **Intent** ha un valore aggiuntivo denominato **action**. Attivare in modo condizionale l'azione o archiviarla per un uso successivo se viene elaborata durante l'avvio dell'app.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Eseguire l'override della funzione **onNewIntent** per chiamare **processNotificationActions**.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Poiché **LaunchMode** per **MainActivity** è impostato su **SingleTop**, verrà inviato un elemento **Intent** all'istanza di **Activity** esistente tramite la funzione **onNewIntent** invece della funzione **onCreate**, per cui è necessario gestire un elemento **Intent** in ingresso nelle due funzioni **onCreate** e **onNewIntent**.

1. Eseguire l'override della funzione **onCreate** e impostare **deviceInstallationService** su una nuova istanza di **DeviceInstallationService**.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Impostare le proprietà **notificationActionService** e **notificationRegistrationService** su **PushNotificationFirebaseMessagingServices**.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. Nella stessa funzione chiamare in modo condizionale **FirebaseInstanceId.getInstance().instanceId**. Implementare **OnCompleteListener** per impostare il valore **token** risultante su **PushNotificationFirebaseMessagingService** prima di chiamare **refreshRegistration**.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. Sempre in **onCreate** chiamare **processNotificationActions** alla fine della funzione. Usare *true* per l'argomento *launchAction* per indicare che l'azione verrà elaborata durante l'avvio dell'app.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> È necessario ripetere la registrazione dell'app ogni volta che la si esegue e arrestarla da una sessione di debug per continuare a ricevere le notifiche push.
