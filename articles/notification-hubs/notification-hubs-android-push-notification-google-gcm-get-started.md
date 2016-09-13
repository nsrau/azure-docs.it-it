<properties
	pageTitle="Invio di notifiche push ad Android con Hub di notifica di Azure | Microsoft Azure"
	description="In questa esercitazione, si apprenderà come usare Hub di notifica di Azure per inviare notifiche push a dispositivi Android."
	services="notification-hubs"
	documentationCenter="android"
	keywords="notifiche push, notifica push, notifiche push per android"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="07/05/2016"
	ms.author="wesmc"/>  

# Invio di notifiche push ad Android con Hub di notifica di Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Overview

> [AZURE.IMPORTANT] Questo argomento illustra le notifiche push con Google Cloud Messaging (GCM). Se si sta ancora usando Google Firebase Messaging (FCM), vedere [Invio di notifiche push ad Android con Hub di notifica di Azure e Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md).

Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Android. In questa esercitazione verrà creata un'app Android vuota che riceve notifiche push usando il servizio Google Cloud Messaging (GCM).

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione può essere scaricato da GitHub [qui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).


##Prerequisiti

> [AZURE.IMPORTANT] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

Oltre a un account Azure attivo come indicato sopra, questa esercitazione richiede solo la versione più recente di [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app Android.

##Creazione di un progetto che supporta Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##Configurare un nuovo hub di notifica


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. Nel pannello **Impostazioni** selezionare **Servizi di notifica** e quindi **Google (GCM)**. Immettere la chiave API e fare clic su **Salva**.

&emsp;&emsp;![Hub di notifica di Azure - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

A questo punto, l'hub di notifica è configurato per l'uso con GCM e sono disponibili le stringhe di connessione per registrare l'app per l'invio e la ricezione di notifiche push.

##<a id="connecting-app"></a>Connettere l'app all'hub di notifica

###Creare un nuovo progetto Android

1. In Android Studio avviare un nuovo progetto Android Studio.

   	![Android Studio: nuovo progetto][13]  

2. Scegliere il fattore di forma **Phone and Tablet** (Telefono e tablet) e la versione **Minimum SDK** (SDK minimo) che si vuole supportare. Quindi fare clic su **Next**.

   	![Android Studio: flusso di lavoro di creazione del progetto][14]  

3. Scegliere **Empty Activity** (Attività vuota) per l'attività principale, fare clic su **Next** (Avanti) e quindi su **Finish** (Fine).

###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Aggiungere Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Aggiunta di librerie dell'Hub di notifica di Azure


1. Nel file `Build.Gradle` relativo all'**app** aggiungere le righe seguenti alla sezione **dependencies**.

		compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
		compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Aggiungere l'archivio seguente dopo la sezione **dependencies**.

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

### Aggiornamento del file AndroidManifest.xml


1. Per supportare GCM è necessario implementare un servizio listener Instance ID nel codice, che consente di [ottenere token di registrazione](https://developers.google.com/cloud-messaging/android/client#sample-register) usando l'[API Instance ID di Google](https://developers.google.com/instance-id/). In questa esercitazione verrà assegnato il nome `MyInstanceIDService` alla classe.
 
	Aggiungere la definizione del servizio seguente al file AndroidManifest.xml, nel tag `<application>`. Sostituire il segnaposto `<your package>` con il nome effettivo del pacchetto visualizzato all'inizio del file `AndroidManifest.xml`.

		<service android:name="<your package>.MyInstanceIDService" android:exported="false">
		    <intent-filter>
		        <action android:name="com.google.android.gms.iid.InstanceID"/>
		    </intent-filter>
		</service>


2. Dopo la ricezione del token di registrazione di GCM dall'API Instance ID, lo si userà per la [registrazione con Hub di notifica di Azure](notification-hubs-push-notification-registration-management.md). La registrazione verrà supportata in background mediante un `IntentService` denominato `RegistrationIntentService`. Il servizio sarà anche responsabile dell'[aggiornamento del token di registrazione di GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).
 
	Aggiungere la definizione del servizio seguente al file AndroidManifest.xml, all'interno del tag `<application>`. Sostituire il segnaposto `<your package>` con il nome effettivo del pacchetto visualizzato all'inizio del file `AndroidManifest.xml`.

        <service
            android:name="<your package>.RegistrationIntentService"
            android:exported="false">
        </service>



3. Verrà definito anche un ricevitore per la ricezione di notifiche. Aggiungere la definizione del ricevitore seguente al file AndroidManifest.xml, nel tag `<application>`. Sostituire il segnaposto `<your package>` con il nome effettivo del pacchetto visualizzato all'inizio del file `AndroidManifest.xml`.

		<receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
		    android:permission="com.google.android.c2dm.permission.SEND">
		    <intent-filter>
		        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
		        <category android:name="<your package name>" />
		    </intent-filter>
		</receiver>



4. Aggiungere le necessarie autorizzazioni correlate a GCM riportate di seguito sotto il tag `</application>`. Sostituire `<your package>` con il nome del pacchetto visualizzato all'inizio del file `AndroidManifest.xml`.

	Per altre informazioni su queste autorizzazioni, vedere [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/android/client#manifest) (Configurare un'app client di GCM per Android).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>


### Aggiunta di codice


1. Nella visualizzazione del progetto espandere **app** > **src** > **main** > **java**. Fare clic con il pulsante destro del mouse sulla cartella del pacchetto in **java**, scegliere **Nuovo** e quindi **Java Class** (Classe Java). Aggiungere una nuova classe denominata `NotificationSettings`.

	![Android Studio: nuova classe Java][6]

	Aggiornare questi tre segnaposto nel codice seguente per la classe `NotificationSettings`:
	* **SenderId**: numero di progetto ottenuto in precedenza in [Google Cloud Console](http://cloud.google.com/console) (Console Cloud di Google).
	* **HubListenConnectionString**: stringa di connessione **DefaultListenAccessSignature** per l'hub. È possibile copiare la stringa di connessione facendo clic su **Criteri di accesso** nel pannello **Impostazioni** dell'hub nel [portale di Azure].
	* **HubName**: usare il nome dell'Hub di notifica visualizzato nel pannello dell'hub nel [portale di Azure].

	Codice di `NotificationSettings`:

		public class NotificationSettings {
		    public static String SenderId = "<Your project number>";
		    public static String HubName = "<Your HubName>";
		    public static String HubListenConnectionString = "<Your default listen connection string>";
		}

2. Usando la procedura precedente, aggiungere un'altra nuova classe denominata `MyInstanceIDService`. Questa sarà l'implementazione del servizio listener Instance ID.

	Il codice per questa classe chiamerà `IntentService` per [aggiornare il token di GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) in background.

		import android.content.Intent;
		import android.util.Log;
		import com.google.android.gms.iid.InstanceIDListenerService;
		
		
		public class MyInstanceIDService extends InstanceIDListenerService {
		
		    private static final String TAG = "MyInstanceIDService";
		
		    @Override
		    public void onTokenRefresh() {
		
		        Log.i(TAG, "Refreshing GCM Registration Token");
		
		        Intent intent = new Intent(this, RegistrationIntentService.class);
		        startService(intent);
		    }
		};


3. Aggiungere al progetto un'altra nuova classe denominata `RegistrationIntentService`. Questa sarà l'implementazione di `IntentService` che gestirà l'[aggiornamento del token di GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e la [registrazione nell'hub di notifica](notification-hubs-push-notification-registration-management.md).

	Usare il codice seguente per la classe.

		import android.app.IntentService;
		import android.content.Intent;
		import android.content.SharedPreferences;
		import android.preference.PreferenceManager;
		import android.util.Log;
		
		import com.google.android.gms.gcm.GoogleCloudMessaging;
		import com.google.android.gms.iid.InstanceID;
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
		
		        try {
		            InstanceID instanceID = InstanceID.getInstance(this);
		            String token = instanceID.getToken(NotificationSettings.SenderId,
		                    GoogleCloudMessaging.INSTANCE_ID_SCOPE);		
		            Log.i(TAG, "Got GCM Registration Token: " + token);
		
		            // Storing the registration id that indicates whether the generated token has been
		            // sent to your server. If it is not stored, send the token to your server,
		            // otherwise your server should have already received the token.
		            if ((regID=sharedPreferences.getString("registrationID", null)) == null) {		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.i(TAG, "Attempting to register with NH using token : " + token);

		                regID = hub.register(token).getRegistrationId();

		                // If you want to use tags...
						// Refer to : https://azure.microsoft.com/it-IT/documentation/articles/notification-hubs-routing-tag-expressions/
		                // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

		                resultString = "Registered Successfully - RegId : " + regID;
		                Log.i(TAG, resultString);		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		            } else {
		                resultString = "Previously Registered Successfully - RegId : " + regID;
		            }
		        } catch (Exception e) {
		            Log.e(TAG, resultString="Failed to complete token refresh", e);
		            // If an exception happens while fetching the new token or updating our registration data
		            // on a third-party server, this ensures that we'll attempt the update at a later time.
		        }
		
		        // Notify UI that registration has completed.
		        if (MainActivity.isVisible) {
		            MainActivity.mainActivity.ToastNotify(resultString);
		        }
		    }
		}


		
4. Nella classe `MainActivity` aggiungere le istruzioni `import` seguenti sopra la dichiarazione della classe.

		import com.google.android.gms.common.ConnectionResult;
		import com.google.android.gms.common.GoogleApiAvailability;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.util.Log;
		import android.widget.TextView;
		import android.widget.Toast;

5. Aggiungere i seguenti membri privati nella parte superiore della classe. Verranno usati per [verificare la disponibilità di Google Play Services come consigliato da Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

	    public static MainActivity mainActivity;
    	public static Boolean isVisible = false;	
		private GoogleCloudMessaging gcm;
	    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. Nella classe `MainActivity` aggiungere il metodo seguente alla disponibilità di Google Play Services.

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


7. Nella classe `MainActivity` aggiungere il codice seguente che cercherà Google Play Services prima di chiamare `IntentService` per ottenere il token di registrazione di GCM e registrarlo nell'hub di notifica.

	    public void registerWithNotificationHubs()
	    {
	        Log.i(TAG, " Registering with Notification Hubs");
	
	        if (checkPlayServices()) {
	            // Start IntentService to register this application with GCM.
	            Intent intent = new Intent(this, RegistrationIntentService.class);
	            startService(intent);
	        }
	    }


8. Nel metodo `OnCreate` della classe `MainActivity` aggiungere il codice seguente per avviare il processo di registrazione quando viene creata l'attività.

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        mainActivity = this;
	        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
	        registerWithNotificationHubs();
	    }


9. Aggiungere questi altri metodi a `MainActivity` per verificare lo stato dell'app e segnalare lo stato nell'app.

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


10. Il metodo `ToastNotify` usa il controllo `TextView`*"Hello World"* per segnalare lo stato e le notifiche in modo permanente nell'app. Nel layout di activity\_main.xml aggiungere l'ID seguente per il controllo.

        android:id="@+id/text_hello"

11. Verrà quindi aggiunta una sottoclasse per il ricevitore definita nel file AndroidManifest.xml. Aggiungere al progetto un'altra nuova classe denominata `MyHandler`.

12. Aggiungere le istruzioni import seguenti all'inizio di `MyHandler.java`:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Aggiungere il codice seguente per la classe `MyHandler` impostandola come sottoclasse di `com.microsoft.windowsazure.notifications.NotificationsHandler`.

	Questo codice ignora il metodo `OnReceive`, in modo che il gestore segnali le notifiche ricevute. Il gestore invia anche la notifica push al gestore delle notifiche di Android usando il metodo `sendNotification()`. Il metodo `sendNotification()` deve essere eseguito quando l'app non è in esecuzione e si riceve una notifica.

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


14. Sulla barra dei menu in Android Studio fare clic su **Build** (Compila) > **Rebuild Project** (Ricompila progetto) per assicurarsi che non ci siano errori nel codice.

##Invio di notifiche push

È possibile testare la ricezione delle notifiche push nell'app inviandole tramite il [portale di Azure]. Cercare la sezione **Risoluzione dei problemi** nel pannello dell'hub, come illustrato di seguito.

![Hub di notifica di Azure: test dell'invio](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)  

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (Facoltativo) Inviare notifiche push direttamente dall'app

In genere, le notifiche vengono inviate tramite un server back-end. In alcuni casi può essere necessario inviare notifiche push direttamente dall'applicazione client. Questa sezione illustra come inviare notifiche dal client usando l'[API REST dell'Hub di notifica di Azure](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. Nella visualizzazione del progetto in Android Studio espandere **App** > **src** > **main** > **res** > **layout**. Aprire il file di layout `activity_main.xml` e fare clic sulla scheda **Text** (Testo) per aggiornare il contenuto di testo del file. Aggiornarlo con il codice seguente che aggiunge i nuovi controlli `Button` e `EditText` per l'invio di messaggi di notifica push all'hub di notifica. Aggiungere questo codice alla fine, subito prima di `</RelativeLayout>`.

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Nella visualizzazione del progetto in Android Studio espandere **App** > **src** > **main** > **res** > **values**. Aprire il file `strings.xml` e aggiungere i valori di stringa a cui fanno riferimento i nuovi controlli `Button` e `EditText`. Aggiungerli alla fine del file, subito prima di `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. Nel file `NotificationSetting.java` aggiungere l'impostazione seguente alla classe `NotificationSettings`.

	Aggiornare `HubFullAccess` con la stringa di connessione **DefaultFullSharedAccessSignature** per l'hub. Questa stringa di connessione può essere copiata dal [portale di Azure] facendo clic su **Criteri di accesso** nel pannello **Impostazioni** dell'hub di notifica.

		public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. Nel file `MainActivity.java` aggiungere le istruzioni `import` seguenti sopra la classe `MainActivity`.

		import java.io.BufferedOutputStream;
		import java.io.BufferedReader;
		import java.io.InputStreamReader;
		import java.io.OutputStream;
		import java.net.HttpURLConnection;
		import java.net.URL;
		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

6. Nel file `MainActivity.java` aggiungere i membri seguenti all'inizio della classe `MainActivity`.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;

6. È necessario creare un token SaS (Software Access Signature) per autenticare una richiesta POST per l'invio di messaggi all'hub di notifica. Per eseguire questa operazione, analizzare i dati della chiave dalla stringa di connessione e quindi creare il token di firma di accesso condiviso come indicato in [Concetti comuni](http://msdn.microsoft.com/library/azure/dn495627.aspx) nelle informazioni di riferimento sull'API REST. Il codice seguente è un esempio di implementazione.

	In `MainActivity.java` aggiungere il metodo seguente alla classe `MainActivity` per analizzare la stringa di connessione.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
	     * to parse the connection string so a SaS authentication token can be
	     * constructed.
	     *
	     * @param connectionString This must be the DefaultFullSharedAccess connection
	     *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);
	
	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }


7. In `MainActivity.java` aggiungere il metodo seguente alla classe `MainActivity` per creare un token di autenticazione di firma di accesso condiviso.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
	     * construct a SaS token from the access key to authenticate a request.
	     *
	     * @param uri The unencoded resource URI string for this operation. The resource
	     *            URI is the full URI of the Service Bus resource to which access is
	     *            claimed. For example,
	     *            "http://<namespace>.servicebus.windows.net/<hubName>"
	     */
	    private String generateSasToken(String uri) {
	
	        String targetUri;
	        String token = null;
	        try {
	            targetUri = URLEncoder
	                    .encode(uri.toString().toLowerCase(), "UTF-8")
	                    .toLowerCase();
	
	            long expiresOnDate = System.currentTimeMillis();
	            int expiresInMins = 60; // 1 hour
	            expiresOnDate += expiresInMins * 60 * 1000;
	            long expires = expiresOnDate / 1000;
	            String toSign = targetUri + "\n" + expires;
	
	            // Get an hmac_sha1 key from the raw key bytes
	            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
	            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
	
	            // Get an hmac_sha1 Mac instance and initialize with the signing key
	            Mac mac = Mac.getInstance("HmacSHA256");
	            mac.init(signingKey);
	
	            // Compute the hmac on input data bytes
	            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
	
	            // Using android.util.Base64 for Android Studio instead of
	            // Apache commons codec
	            String signature = URLEncoder.encode(
	                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
	
	            // Construct authorization string
	            token = "SharedAccessSignature sr=" + targetUri + "&sig="
	                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
	        } catch (Exception e) {
	            if (isVisible) {
	                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
	            }
	        }
	
	        return token;
	    }



8. In `MainActivity.java` aggiungere il metodo seguente alla classe `MainActivity` per gestire il clic del pulsante **Invia notifica** e inviare il messaggio di notifica push all'hub usando l'API REST predefinita.

	    /**
	     * Send Notification button click handler. This method parses the
	     * DefaultFullSharedAccess connection string and generates a SaS token. The
	     * token is added to the Authorization header on the POST request to the
	     * notification hub. The text in the editTextNotificationMessage control
	     * is added as the JSON body for the request to add a GCM message to the hub.
	     *
	     * @param v
	     */
	    public void sendNotificationButtonOnClick(View v) {
	        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
	        final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";
	
	        new Thread()
	        {
	            public void run()
	            {
	                try
	                {
	                    // Based on reference documentation...
	                    // http://msdn.microsoft.com/library/azure/dn223273.aspx
	                    ParseConnectionString(NotificationSettings.HubFullAccess);
	                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
	                            "/messages/?api-version=2015-01");
	
	                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
	
	                    try {
	                        // POST request
	                        urlConnection.setDoOutput(true);
	
	                        // Authenticate the POST request with the SaS token
	                        urlConnection.setRequestProperty("Authorization", 
								generateSasToken(url.toString()));
	
	                        // Notification format should be GCM
	                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
	
	                        // Include any tags
	                        // Example below targets 3 specific tags
	                        // Refer to : https://azure.microsoft.com/it-IT/documentation/articles/notification-hubs-routing-tag-expressions/
	                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
							//		"tag1 || tag2 || tag3");
	
	                        // Send notification message
	                        urlConnection.setFixedLengthStreamingMode(json.length());
	                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
	                        bodyStream.write(json.getBytes());
	                        bodyStream.close();
	
	                        // Get reponse
	                        urlConnection.connect();
	                        int responseCode = urlConnection.getResponseCode();
	                        if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
	                        }
	                    } finally {
	                        urlConnection.disconnect();
	                    }
	                }
	                catch(Exception e)
	                {
	                    if (isVisible) {
	                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
	                    }
	                }
	            }
	        }.start();
	    }



##Testare l'app

####Notifiche push nell'emulatore

Per testare le notifiche push all'interno dell'emulatore, assicurarsi che l'immagine dell'emulatore supporti il livello Google API scelto per l'app. Se l'immagine non supporta Google APIs in modalità nativa verrà generata l'eccezione **SERVICE\_NOT\_AVAILABLE**.

Verificare anche di avere aggiunto l'account Google all'emulatore in esecuzione in **Impostazioni** > **Account**. In caso contrario, i tentativi di registrazione con GCM potrebbero generare l'eccezione **AUTHENTICATION\_FAILED**.

####Esecuzione dell'applicazione

1. Eseguire l'app e notare l'ID registrazione segnalato per una registrazione riuscita.

   	![Test in Android: registrazione di canali][18]

2. Immettere un messaggio di notifica da inviare a tutti i dispositivi Android registrati con l'hub.

   	![Test in Android: invio di un messaggio][19]  

3. Premere **Send Notification**. Su tutti i dispositivi che eseguono l'app verrà visualizzata un'istanza di `AlertDialog` con il messaggio di notifica push. I dispositivi che non eseguono l'app, ma che sono stati registrati in precedenza per le notifiche push, riceveranno una notifica in Android Notification Manager. Per visualizzarle, scorrere verso il basso dall'angolo superiore sinistro.

   	![Test in Android: notifiche][21]  

##Passaggi successivi

Come passaggio successivo, è consigliabile vedere l'esercitazione [Uso di Hub di notifica di Azure per inviare notifiche agli utenti]. Verrà illustrato come inviare notifiche da un back-end ASP.NET destinate a utenti specifici.

Per segmentare gli utenti in base ai gruppi di interesse, vedere l'esercitazione [Usare Hub di notifica per inviare le ultime notizie].

Per altre informazioni generali sull'uso di Hub di notifica, vedere [Hub di notifica di Azure].

<!-- Images. -->  
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->  
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Hub di notifica di Azure]: http://msdn.microsoft.com/library/jj927170.aspx
[Uso di Hub di notifica di Azure per inviare notifiche agli utenti]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Usare Hub di notifica per inviare le ultime notizie]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[portale di Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0907_2016-->