---
title: Invio di notifiche push ad app Android con Hub di notifica di Azure e Firebase Cloud Messaging | Microsoft Docs
description: In questa esercitazione si apprenderà come usare Hub di notifica di Azure e Google Firebase Cloud Messaging per inviare notifiche push ai dispositivi Android.
services: notification-hubs
documentationcenter: android
keywords: notifiche push, notifica push, notifiche push per android, fcm, firebase cloud messaging
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/30/2019
ms.author: jowargo
ms.openlocfilehash: f2efa9b7e1e534f93e4ea01ba52740c8c5ac7b02
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653872"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Esercitazione: Effettuare il push di notifiche ai dispositivi Android con Hub di notifica di Azure e Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Questa esercitazione illustra come usare Hub di notifica di Azure e Firebase Cloud Messaging (FCM) per inviare notifiche push a un'applicazione Android. In questa esercitazione verrà creata un'app Android vuota che riceve notifiche push tramite Firebase Cloud Messaging (FCM).

Il codice completo per questa esercitazione può essere scaricato da [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare un progetto di Android Studio.
> * Creare un progetto Firebase che supporta Firebase Cloud Messaging.
> * Creare un hub.
> * Connettere l'app all'hub.
> * Testare l'app.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/). 

Sono necessari anche gli elementi seguenti: 

* Ultima versione di [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 o versione successiva per Firebase Cloud Messaging
* Google Repository revisione 27 o successiva per Firebase Cloud Messaging
* Google Play Services 9.0.2 o versione successiva per Firebase Cloud Messaging

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni su Hub di notifica per app Android.

## <a name="create-an-android-studio-project"></a>Creare un progetto di Android Studio

1. Avviare Android Studio.
2. Selezionare **File**, scegliere **New** (Nuovo) e quindi **New Project** (Nuovo progetto). 
2. Nella pagina **Choose your project** (Scegliere il progetto) selezionare **Empty Activity** (Attività vuota) e quindi **Next** (Avanti). 
3. Nella pagina **Configure your project** (Configurare il progetto) completare questi passaggi: 
    1. Immettere un nome per l'applicazione.
    2. Specificare un percorso in cui salvare i file del progetto. 
    3. Selezionare **Fine**. 

        ![Configurare il progetto](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Creare un progetto Firebase che supporta FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Configurare un hub

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Configurare le impostazioni di Firebase Cloud Messaging per l'hub

1. Nel riquadro a sinistra in **Settings** (Impostazioni) selezionare **Google (GCM/FCM)** . 
2. Immettere la **chiave del server** per il progetto FCM salvato in precedenza. 
3. Sulla barra degli strumenti selezionare**Save** (Salva). 

    ![Hub di notifica di Azure - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Nella sezione degli avvisi del portale di Azure viene visualizzato un messaggio che indica che l'hub è stato aggiornato. Il pulsante **Save** (Salva) è disabilitato. 

L'hub è ora configurato per l'uso di Firebase Cloud Messaging. Sono anche disponibili le stringhe di connessione necessarie per inviare notifiche a un dispositivo e registrare un'app per la ricezione di notifiche.

## <a id="connecting-app"></a>Connettere l'app all'hub di notifica

### <a name="add-google-play-services-to-the-project"></a>Aggiungere Google Play Services al progetto

1. In Android Studio selezionare **Strumenti** nel menu e quindi **SDK Manager**. 
2. Selezionare la versione di destinazione di Android SDK che viene usata nel progetto. Quindi selezionare **Mostra i dettagli del pacchetto**. 

    ![Android SDK Manager: selezionare la versione di destinazione](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selezionare **API Google**, se non è già installato.

    ![Android SDK Manager: selezione di Google APIs (API Google)](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Passare alla scheda **SDK Tools**. Se lo strumento Google Play Services non è già installato, fare clic su **Google Play Services** come illustrato nell'immagine di seguito. Selezionare **Applica** per installarlo. Prendere nota del percorso dell'SDK per l'uso in un passaggio successivo.

    ![Android SDK Manager: selezione di Google Play Services](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Se viene visualizzata la finestra di dialogo **Confirm Change** (Conferma modifica), selezionare **OK**. Il programma di installazione componenti installa i componenti richiesti. Al termine dell'installazione dei componenti, selezionare **Finish** (Fine).
4. Selezionare **OK** per chiudere la finestra di dialogo **Settings for New Projects** (Impostazioni per nuovi progetti).  
5. Selezionare l'icona **Sincronizza ora** nella barra degli strumenti
1. Aprire il file AndroidManifest.xml e quindi aggiungere il tag seguente al tag dell'*applicazione*.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Aggiungere le librerie di Hub di notifica di Azure

1. Nel file Build.Gradle per l'app aggiungere le righe seguenti nella sezione dependencies.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Aggiungere il repository seguente dopo la sezione dependencies.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Aggiungere il supporto di Google Firebase

1. Nel file Build.Gradle per l'app aggiungere le righe seguenti nella sezione **dependencies** se non sono già presenti. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Aggiungere il plug-in seguente alla fine del file se non è già presente. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Selezionare **Sync Now** (Sincronizza ora) sulla barra degli strumenti.

### <a name="update-the-androidmanifestxml-file"></a>Aggiornare il file AndroidManifest.xml

1. Dopo aver ricevuto il token di registrazione di FCM, usarlo per la [registrazione in Hub di notifica di Azure](notification-hubs-push-notification-registration-management.md). La registrazione viene supportata in background mediante un servizio `IntentService` denominato `RegistrationIntentService`. Il servizio aggiorna anche il token di registrazione di FCM.

    Aggiungere la definizione del servizio seguente al file AndroidManifest.xml, all'interno del tag `<application>` .

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

2. È necessario definire anche un ricevitore per la ricezione di notifiche. Aggiungere la definizione del ricevitore seguente al file AndroidManifest.xml, nel tag `<application>` . 

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

    > [!IMPORTANT]
    > Sostituire il segnaposto `<your package NAME>` con il nome effettivo del pacchetto, presente all'inizio del file AndroidManifest.xml.
3. Aggiungere le autorizzazioni necessarie relative a FCM sotto il tag `</application>`.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Aggiungere codice

1. Nella visualizzazione del progetto espandere **app** > **src** > **main** > **java**. Fare clic con il pulsante destro del mouse sulla cartella del pacchetto in **java**, scegliere **New** (Nuovo), quindi selezionare **Java Class** (Classe Java). Immettere **Notification Settings** come nome e quindi scegliere **OK**.

    Aggiornare questi tre segnaposto nel codice seguente per la classe `NotificationSettings`:

   * **HubListenConnectionString**: la stringa di connessione **DefaultListenAccessSignature** per l'hub. È possibile copiare la stringa di connessione facendo clic su **Criteri di accesso** nell'hub nel [portale di Azure].
   * **HubName**: Usare il nome dell'hub visualizzato nella pagina dell'hub del [portale di Azure].

     `NotificationSettings` :

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Immettere il **nome** e il valore di **DefaultListenSharedAccessSignature** dell'hub prima di continuare. 

3. Aggiungere al progetto un'altra nuova classe denominata `RegistrationIntentService`. La classe implementa l'interfaccia `IntentService`. La classe gestisce anche l'[aggiornamento del token di FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e la [registrazione nell'hub di notifica](notification-hubs-push-notification-registration-management.md).

    Usare il codice seguente per la classe.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

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
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
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
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. Nella classe `MainActivity` aggiungere le istruzioni `import` seguenti sopra la dichiarazione della classe.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

5. Aggiungere i membri seguenti nella parte superiore della classe. Questi campi vengono usati per [verificare la disponibilità di Google Play Services come consigliato da Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

6. Nella classe `MainActivity` aggiungere il metodo seguente per verificare la disponibilità di Google Play Services.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
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

7. Nella classe `MainActivity` aggiungere il codice seguente, che verifica la presenza di Google Play Services prima di chiamare `IntentService` per ottenere il token di registrazione di FCM e registrarlo nell'hub:

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

8. Nel metodo `OnCreate` della classe `MainActivity` aggiungere il codice seguente per avviare il processo di registrazione quando viene creata l'attività:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
    }
    ```

9. Per verificare lo stato dell'app e segnalare lo stato nell'app, aggiungere questi altri metodi a `MainActivity`:

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

10. Il metodo `ToastNotify` usa il controllo *"Hello World"* `TextView` per segnalare lo stato e le notifiche in modo permanente nell'app. Nel layout **res** > **layout** > **activity_main.xml** aggiungere l'ID seguente per questo controllo.

    ```java
    android:id="@+id/text_hello"
    ```

11. Aggiungere quindi una sottoclasse per il ricevitore definito nel file AndroidManifest.xml. Aggiungere al progetto un'altra nuova classe denominata `MyHandler`.

12. Aggiungere le istruzioni import seguenti all'inizio di `MyHandler.java`:

    ```java
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;    
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    ```

13. Aggiungere il codice seguente per la classe `MyHandler`, impostandola come sottoclasse di `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Questo codice ignora il metodo `OnReceive`, quindi il gestore segnala le notifiche ricevute. Il gestore invia anche la notifica push al gestore delle notifiche di Android usando il metodo `sendNotification()` . Chiamare il metodo `sendNotification()` quando l'app non è in esecuzione e viene ricevuta una notifica.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
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
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
                NotificationsManager.handleNotifications(context, "", MyHandler.class);
            }
        }
    }
    ```

14. Sulla barra dei menu di Android Studio fare clic su **Compila** > **Ricompila progetto** per assicurarsi che il codice non contenga errori. Se si riceve un errore riguardo all'icona `ic_launcher`, rimuovere l'istruzione seguente dal file AndroidManifest.xml: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Eseguire l'app nel dispositivo e verificare che venga registrata correttamente nell'hub.

    > [!NOTE]
    > La registrazione può non riuscire durante l'avvio iniziale, fino a quando non viene chiamato il metodo `onTokenRefresh()` del servizio Instance ID. L'aggiornamento deve avviare una registrazione corretta con l'hub di notifica.

    ![Il dispositivo è stato registrato](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Testare la notifica di invio dall'hub di notifica

È possibile inviare notifiche push dal [portale di Azure] eseguendo questi passaggi:

1. Nella pagina Hub di notifica per l'hub nel portale di Azure selezionare **Invio di prova** nella sezione **Risoluzione dei problemi**.
3. Per **Platforms** (Piattaforme) selezionare **Android**.
4. Selezionare **Send** (Invia).  Non verrà visualizzata alcuna notifica nel dispositivo Android, perché nel dispositivo non è stata ancora eseguita l'app per dispositivi mobili. Dopo aver eseguito l'app per dispositivi mobili, selezionare di nuovo il pulsante **Invia** per visualizzare il messaggio di notifica.
5. Osservare i risultati dell'operazione nell'elenco in fondo.

    ![Hub di notifica di Azure: test dell'invio](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Il messaggio di notifica viene visualizzato nel dispositivo. 

    ![Messaggio di notifica nel dispositivo](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Eseguire l'app per dispositivi mobili nell'emulatore
Prima di testare le notifiche push all'interno dell'emulatore, assicurarsi che l'immagine dell'emulatore supporti il livello Google API scelto per l'app. Se l'immagine non supporta le API di Google in modalità nativa, è possibile che venga generata l'eccezione **SERVICE\_NOT\_AVAILABLE**.

Verificare inoltre di avere aggiunto l'account Google all'emulatore in esecuzione in **Impostazioni** > **Account**. In caso contrario, i tentativi di registrazione in FCM possono generare l'eccezione **AUTHENTICATION\_FAILED**.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato Firebase Cloud Messaging per trasmettere notifiche a tutti i dispositivi Android registrati nel servizio. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi specifici, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Esercitazione: Inviare notifiche push a specifici dispositivi Android](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Portale di Azure]: https://portal.azure.com
