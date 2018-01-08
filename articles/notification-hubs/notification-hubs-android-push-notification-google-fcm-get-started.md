---
title: Introduzione a Hub di notifica di Azure per app Android e Firebase Cloud Messaging | Microsoft Docs
description: "In questa esercitazione si apprenderà come usare Hub di notifica di Azure e Firebase Cloud Messaging per inviare notifiche push a dispositivi Android."
services: notification-hubs
documentationcenter: android
keywords: notifiche push, notifica push, notifiche push per android, fcm, firebase cloud messaging
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 2cac554be145c3bb9ec2c71ef893bba947104a2d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-android-apps-and-firebase-cloud-messaging"></a>Introduzione a Hub di notifica di Azure per app Android e Firebase Cloud Messaging
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica
> [!IMPORTANT]
> Questo articolo illustra le notifiche push con Google Firebase Cloud Messaging (FCM). Se si sta ancora usando Google Cloud Messaging (GCM), vedere [Invio di notifiche push ad Android con Hub di notifica di Azure](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

Questa esercitazione illustra come usare Hub di notifica di Azure e Firebase Cloud Messaging per inviare notifiche push a un'applicazione per Android. In questa esercitazione verrà creata un'app Android vuota che riceve notifiche push tramite Firebase Cloud Messaging (FCM).

[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione può essere scaricato da GitHub [qui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).

## <a name="prerequisites"></a>prerequisiti
> [!IMPORTANT]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).
> 
> 

* Oltre a un account Azure attivo come indicato sopra, questa esercitazione richiede la versione più recente di [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3 o versione successiva per Firebase Cloud Messaging.
* Google Repository revision 27 o versione successiva è richiesta per Firebase Cloud Messaging.
* Google Play Services 9.0.2 o versione successiva per Firebase Cloud Messaging.
* Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Android.

## <a name="create-a-new-android-studio-project"></a>Creare un nuovo progetto di Android Studio
1. In Android Studio avviare un nuovo progetto Android Studio.
   
    ![Android Studio: nuovo progetto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. Scegliere il fattore di forma **Phone and Tablet** (Telefono e tablet) e la versione **Minimum SDK** (SDK minimo) che si vuole supportare. Quindi fare clic su **Next**.
   
    ![Android Studio: flusso di lavoro di creazione del progetto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. Scegliere **Empty Activity** (Attività vuota) per l'attività principale, fare clic su **Avanti**, quindi su **Fine**.

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Creare un progetto che supporta Google Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-new-notification-hub"></a>Configurare un nuovo hub di notifica
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. In **Servizi di notifica** selezionare **Google (GCM)**. Immettere la chiave del server FCM copiata in precedenza dalla [console di Firebase](https://firebase.google.com/console/) e fare clic su **Salva**.

&emsp;&emsp;![Hub di notifica di Azure - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

L'hub di notifica è ora configurato per l'uso con Firebase Cloud Messaging e sono disponibili le stringhe di connessione per registrare l'app per la ricezione e l'invio di notifiche push.

## <a id="connecting-app"></a>Connettere l'app all'hub di notifica
### <a name="add-google-play-services-to-the-project"></a>Aggiungere Google Play Services al progetto
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Aggiunta di librerie dell'Hub di notifica di Azure
1. Nel file `Build.Gradle` relativo all'**app** aggiungere le righe seguenti alla sezione delle **dipendenze**.
   
    ```java
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Aggiungere l'archivio seguente dopo la sezione **dependencies** .
   
    ```java
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }
    ```

### <a name="updating-the-androidmanifestxml"></a>Aggiornamento del file AndroidManifest.xml
1. Per supportare FCM è necessario implementare un servizio listener Instance ID nel codice, usato per [ottenere token di registrazione](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) usando l'[API FirebaseInstanceId di Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). In questa esercitazione, il nome della classe è `MyInstanceIDService`. 
   
    Aggiungere la definizione del servizio seguente al file AndroidManifest.xml, all'interno del tag `<application>` . 
   
    ```xml
        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>
    ```

2. Dopo la ricezione del token di registrazione di FCM dall'API FirebaseInstanceId, lo si userà per la [registrazione con l'hub di notifica di Azure](notification-hubs-push-notification-registration-management.md). La registrazione verrà supportata in background tramite un `IntentService` denominato `RegistrationIntentService`. Il servizio sarà anche responsabile dell'aggiornamento del token di registrazione di FCM.
   
    Aggiungere la definizione del servizio seguente al file AndroidManifest.xml, all'interno del tag `<application>` . 
   
    ```xml
        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>
    ```

3. È anche necessario definire un ricevitore per la ricezione delle notifiche. Aggiungere la definizione del ricevitore seguente al file AndroidManifest.xml, nel tag `<application>` . Sostituire il segnaposto `<your package>` con il nome effettivo del pacchetto visualizzato all'inizio del file `AndroidManifest.xml`.

    ```xml
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
    ```

4. Aggiungere le autorizzazioni necessarie seguenti relative a FCM sotto il tag `</application>`. Sostituire `<your package>` con il nome del pacchetto visualizzato all'inizio del file `AndroidManifest.xml`.
   
    Per altre informazioni su queste autorizzazioni, vedere [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/android/client#manifest) (Configurare un'app client FCM per Android) e [Migrate a GCM Client App for Android to Firebase Cloud Messaging ](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm) (Eseguire la migrazione di un'app client GCM per Android a Firebase Cloud Messaging).
   
    ```xml
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Aggiunta di codice
1. Nella visualizzazione del progetto espandere **app** > **src** > **main** > **java**. Fare clic con il pulsante destro del mouse sulla cartella del pacchetto in **java**, scegliere **Nuovo**, quindi selezionare **Java Class** (Classe Java). Aggiungere una nuova classe denominata `NotificationSettings`. 
   
    ![Android Studio: nuova classe Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    Aggiornare questi tre segnaposto nel codice seguente per la classe `NotificationSettings`:
   
   * **SenderId**: ID mittente ottenuto in precedenza nella scheda **Cloud Messaging** delle impostazioni di progetto nella [console di Firebase](https://firebase.google.com/console/).
   * **HubListenConnectionString**: stringa di connessione **DefaultListenAccessSignature** per l'hub. È possibile copiare la stringa di connessione facendo clic su **Criteri di accesso** nell'hub nel [portale di Azure].
   * **HubName**: usare il nome dell'hub di notifica visualizzato nel pannello dell'hub del [portale di Azure].
     
     `NotificationSettings` :
     
    ```java
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
    ```

2. Usando la procedura precedente, aggiungere un'altra nuova classe denominata `MyInstanceIDService`. Questa sarà l'implementazione del servizio listener Instance ID.
   
    Il codice di questa classe chiama `IntentService` per [aggiornare il token di FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) in background.
   
    ```java
        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;

        public class MyInstanceIDService extends FirebaseInstanceIdService {

            private static final String TAG = "MyInstanceIDService";

            @Override
            public void onTokenRefresh() {

                Log.d(TAG, "Refreshing GCM Registration Token");

                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };
    ```

1. Aggiungere al progetto un'altra nuova classe denominata `RegistrationIntentService`. Questa sarà l'implementazione di `IntentService` che gestirà l'[aggiornamento del token di FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e la [registrazione nell'hub di notifica](notification-hubs-push-notification-registration-management.md).
   
    Usare il codice seguente per la classe.
   
    ```java
        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class RegistrationIntentService extends IntentService {
   
            private static final String TAG = "RegIntentService";
   
            private NotificationHub hub;
   
            public RegistrationIntentService() {
                super(TAG);
            }
   
            @Override
            protected void onHandleIntent(Intent intent) {
   
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
   
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
   
                    // Storing the registration ID that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
   
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }
    ```

2. Nella classe `MainActivity` aggiungere le istruzioni `import` seguenti sopra la dichiarazione della classe.
   
    ```java
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
    ```

3. Aggiungere i seguenti membri privati nella parte superiore della classe. Verranno usati per [verificare la disponibilità di Google Play Services come consigliato da Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).
   
    ```java
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

4. Nella classe `MainActivity` aggiungere il metodo seguente alla disponibilità di Google Play Services. 
   
    ```java
        /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }
    ```

5. Nella classe `MainActivity` aggiungere il codice seguente che cercherà Google Play Services prima di chiamare `IntentService` per ottenere il token di registrazione di FCM e registrarlo nell'hub di notifica.
   
    ```java
        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
    ```

6. Nel metodo `OnCreate` della classe `MainActivity` aggiungere il codice seguente per avviare il processo di registrazione quando viene creata l'attività.
   
    ```java
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
    ```

7. Aggiungere questi altri metodi a `MainActivity` per verificare lo stato dell'app e segnalare lo stato nell'app.
   
    ```java
        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
   
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
   
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
   
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
   
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }
    ```

8. Il metodo `ToastNotify` usa il controllo *"Hello World"* `TextView` per segnalare lo stato e le notifiche in modo permanente nell'app. Nel layout di activity_main.xml aggiungere l'ID seguente per il controllo.
   
    ```java
       android:id="@+id/text_hello"
    ```

9. Verrà quindi aggiunta una sottoclasse per il ricevitore definito nel file AndroidManifest.xml. Aggiungere al progetto un'altra nuova classe denominata `MyHandler`.
10. Aggiungere le istruzioni import seguenti all'inizio di `MyHandler.java`:
    
    ```java
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
    ```

11. Aggiungere il codice seguente per la classe `MyHandler` impostandola come sottoclasse di `com.microsoft.windowsazure.notifications.NotificationsHandler`.
    
    Questo codice ignora il metodo `OnReceive`, quindi il gestore segnala le notifiche ricevute. Il gestore invia anche la notifica push al gestore delle notifiche di Android usando il metodo `sendNotification()` . Il metodo `sendNotification()` deve essere eseguito quando l'app non è in esecuzione e si riceve una notifica.
    
    ```java
        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
    
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
    
            private void sendNotification(String msg) {
    
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
    
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
    
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }
    ```

12. Sulla barra dei menu in Android Studio fare clic su **Compila** > **Ricompila il progetto** per assicurarsi che non ci siano errori nel codice.
13. Eseguire l'app sul dispositivo e verificare che si registri correttamente con l'hub di notifica. 
    
    > [!NOTE]
    > La registrazione potrebbe non riuscire all'avvio iniziale, fino a quando non viene chiamato il metodo `onTokenRefresh()` del servizio Instance ID. L'aggiornamento deve avviare una registrazione corretta con l'hub di notifica.
    > 
    > 

## <a name="sending-push-notifications"></a>Invio di notifiche push
È possibile testare la ricezione delle notifiche push nell'app inviandole tramite il [portale di Azure]. Cercare la sezione **Risoluzione dei problemi** nell'hub, come illustrato di seguito.

![Hub di notifica di Azure: test dell'invio](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="testing-your-app"></a>Testare l'app
#### <a name="push-notifications-in-the-emulator"></a>Notifiche push nell'emulatore
Per testare le notifiche push all'interno dell'emulatore, assicurarsi che l'immagine dell'emulatore supporti il livello Google API scelto per l'app. Se l'immagine non supporta le API di Google in modalità nativa verrà generata l'eccezione **SERVICE\_NOT\_AVAILABLE**.

Verificare anche di avere aggiunto l'account Google all'emulatore in esecuzione in **Impostazioni** > **Account**. In caso contrario, i tentativi di registrazione con GCM potrebbero generare l'eccezione **AUTHENTICATION\_FAILED**.

#### <a name="running-the-application"></a>Esecuzione dell'applicazione
1. Eseguire l'app e notare l'ID registrazione segnalato per una registrazione riuscita.
   
    ![Test in Android: registrazione di canali](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. Immettere un messaggio di notifica da inviare a tutti i dispositivi Android registrati con l'hub.
   
    ![Test in Android: invio di un messaggio](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. Premere **Send Notification**. Su tutti i dispositivi che eseguono l'app verrà visualizzata un'istanza di `AlertDialog` con il messaggio di notifica push. I dispositivi che non eseguono l'app, ma che sono stati registrati in precedenza per le notifiche push, riceveranno una notifica in Android Notification Manager. Per visualizzarle, scorrere verso il basso dall'angolo superiore sinistro.
   
    ![Test in Android: notifiche](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## <a name="next-steps"></a>Passaggi successivi
Come passaggio successivo, è consigliabile vedere l'esercitazione [Uso di Hub di notifica di Azure per inviare notifiche agli utenti] . L'esercitazione illustra come inviare notifiche da un back-end ASP.NET usando tag per indicare come destinazione utenti specifici.


Per segmentare gli utenti in base ai gruppi di interesse, vedere l'esercitazione [Usare Hub di notifica per inviare le ultime notizie] .

Per altre informazioni generali sull'uso di Hub di notifica, vedere [Hub di notifica di Azure].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Hub di notifica di Azure]: notification-hubs-push-notification-overview.md
[Uso di Hub di notifica di Azure per inviare notifiche agli utenti]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Usare Hub di notifica per inviare le ultime notizie]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[portale di Azure]: https://portal.azure.com
