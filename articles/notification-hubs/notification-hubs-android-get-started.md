<properties
	pageTitle="Invio di notifiche push ad Android con Hub di notifica di Azure | Microsoft Azure"
	description="In questa esercitazione, si apprenderà come usare Hub di notifica di Azure per inviare notifiche push a dispositivi Android."
	services="notification-hubs"
	documentationCenter="android"
	keywords="notifiche push, notifica push, notifiche push per android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="03/15/2016"
	ms.author="wesmc"/>

# Invio di notifiche push ad Android con Hub di notifica di Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Panoramica

> [AZURE.IMPORTANT] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Android. In questa esercitazione verrà creata un'app per Android vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM).

Per informazioni sull'uso di Hub di notifica per l'invio di notifiche push mirate, vedere anche l'[esercitazione relativa all'aggiunta di tag](./notification-hubs-routing-tag-expressions.md).


## Prima di iniziare

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione è disponibile su GitHub [qui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).


##Prerequisiti

Oltre a un account Azure attivo come indicato sopra, questa esercitazione richiede solo la versione più recente di [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Android.

##Creare un progetto che supporta Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##Configurare un nuovo hub di notifica


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;7. Nel pannello **Impostazioni** selezionare **Servizi di notifica** e quindi **Google (GCM)**. Immettere la chiave API e salvare.

&emsp;&emsp;![Hub di notifica di Azure - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

A questo punto, l'hub di notifica è configurato per l'uso con GCM e sono disponibili le stringhe di connessione per registrare l'app per l'invio e la ricezione di notifiche push.

##<a id="connecting-app"></a>Connettere l'app all'hub di notifica

###Creare un nuovo progetto Android

1. In Android Studio avviare un nuovo progetto Android Studio.

   	![Android Studio: nuovo progetto][13]

2. Scegliere il fattore di forma **Phone and Tablet** e la versione **Minimum SDK** che si vuole supportare. Quindi fare clic su **Next**.

   	![Android Studio: flusso di lavoro di creazione del progetto][14]

3. Scegliere **Blank Activity** per l'attività principale, fare clic su **Next**, quindi fare clic su **Finish**.

###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Aggiungere Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Aggiungere codice

1. Scaricare il file `notification-hubs-0.4.jar` dalla scheda **Files** di [Notification-Hubs-Android-SDK in Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Trascinare il file direttamente nella cartella **libs** nella finestra Project View di Android Studio. Quindi fare clic con il pulsante destro del mouse sul file e scegliere **Add as Library**.

2. Nel file `Build.Gradle` relativo all'**app**, aggiungere la riga seguente alla sezione **dependencies**.

	    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

	Aggiungere l'archivio seguente dopo la sezione **dipendenze**.

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

3. Configurare l'applicazione per ottenere un *registrationId* da GCM e usare questo ID per registrare l'istanza dell'app nell'hub di notifica.

	Nel file `AndroidManifest.xml` aggiungere le autorizzazioni seguenti sotto il tag `</application>`. Assicurarsi di sostituire `<your package>` con il nome del pacchetto visualizzato all'inizio del file `AndroidManifest.xml`, in questo caso `com.example.testnotificationhubs`.

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. Nella classe **MainActivity** aggiungere le istruzioni `import` seguenti sopra la dichiarazione di classe.

		import android.app.AlertDialog;
		import android.content.DialogInterface;
		import android.os.AsyncTask;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.widget.Toast;



4. Aggiungere i seguenti membri privati nella parte superiore della classe. Verranno usati per configurare il canale di notifica push tra l'app e i servizi cloud.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";
	    private static Boolean isVisible = false;


	Assicurarsi di aggiornare i tre segnaposto:
	* **SENDER\_ID**: impostare `SENDER_ID` sul numero di progetto ottenuto in precedenza in [Google Cloud Console](http://cloud.google.com/console).
	* **HubListenConnectionString**: impostare `HubListenConnectionString` sulla stringa di connessione **DefaultListenAccessSignature** per l'hub. È possibile copiare la stringa di connessione facendo clic su **Criteri di accesso** nel pannello **Impostazioni** dell'hub nel [portale di Azure].
	* **HubName**: usare il nome dell'hub di notifica visualizzato nel pannello dell'hub nel [portale di Azure].


5. Nel metodo `OnCreate` della classe `MainActivity` aggiungere il codice seguente per registrarsi con l'hub di notifica quando si crea l'attività.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. In `MainActivity.java` aggiungere il codice seguente per il metodo `registerWithNotificationHubs()`. Questo metodo segnala che l'operazione è riuscita dopo la registrazione con Google Cloud Messaging e l'hub di notifica.

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    ToastNotify("Registered Successfully - RegId : " +
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	ToastNotify("Registration Exception Message - " + e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}


7. Aggiungere il metodo `ToastNotify` all'attività per visualizzare la notifica quando l'applicazione è in esecuzione e visibile. Eseguire anche l'override di `onStart`, `onPause`, `onResume` e `onStop` per determinare se l'attività è visibile per visualizzare un avviso popup.

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

	    public void ToastNotify(final String notificationMessage)
	    {
	        if (isVisible == true)
	            runOnUiThread(new Runnable() {
	                @Override
	                public void run() {
	                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
	                }
	            });
	    }

8. Poiché per impostazione predefinita Android non gestisce la visualizzazione delle notifiche, è necessario scrivere un ricevitore apposito. In `AndroidManifest.xml` aggiungere l'elemento seguente all'interno dell'elemento `<application>`.

	> [AZURE.NOTE] Sostituire il segnaposto con il nome del pacchetto.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


9. Nella visualizzazione del progetto espandere **app** > **src** > **main** > **java**. Fare clic con il pulsante destro del mouse sulla cartella del pacchetto in **java**, fare clic su **New** e quindi su **Java Class**.

	![Android Studio: nuova classe Java][6]

10. Nel campo **Name** digitare **MyHandler** per la nuova classe, quindi fare clic su **OK**.


11. Aggiungere le istruzioni import seguenti all'inizio di `MyHandler.java`:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;


12. Aggiornare la dichiarazione di classe come segue per impostare `MyHandler` come sottoclasse di `com.microsoft.windowsazure.notifications.NotificationsHandler`, come illustrato di seguito.

		public class MyHandler extends NotificationsHandler {


13. Aggiungere il codice seguente per la classe `MyHandler`.

	Questo codice ignora il metodo `OnReceive` in modo che il gestore visualizzi un avviso popup per mostrare le notifiche ricevute. Il gestore invia anche la notifica push ad Android Notification Manager usando il metodo `sendNotification()`.

    	public static final int NOTIFICATION_ID = 1;
    	private NotificationManager mNotificationManager;
    	NotificationCompat.Builder builder;
    	Context ctx;

    	static public MainActivity mainActivity;

    	@Override
    	public void onReceive(Context context, Bundle bundle) {
        	ctx = context;
        	String nhMessage = bundle.getString("message");

        	sendNotification(nhMessage);
	        mainActivity.ToastNotify(nhMessage);
    	}

    	private void sendNotification(String msg) {
        	mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

			NotificationCompat.Builder mBuilder =
				new NotificationCompat.Builder(ctx)
					.setSmallIcon(R.mipmap.ic_launcher)
					.setContentTitle("Notification Hub Demo")
					.setStyle(new NotificationCompat.BigTextStyle()
					.bigText(msg))
					.setContentText(msg);

			mBuilder.setContentIntent(contentIntent);
			mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}

14. Sulla barra dei menu in Android Studio fare clic su **Build** > **Rebuild Project** per assicurarsi che non ci siano errori nel codice.

##Invio di notifiche push

È possibile testare la ricezione delle notifiche push nell'app inviandole tramite il [portale di Azure]. Cercare la sezione **Risoluzione dei problemi** nel pannello dell'hub, come illustrato di seguito.

![Hub di notifica di Azure: test dell'invio](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (Facoltativo) Inviare notifiche push direttamente dall'app

Per la maggior parte dei test case può essere necessario inviare notifiche push direttamente dall'applicazione che si sta sviluppando. Questa sezione illustra come implementare correttamente questo scenario.

1. Nella visualizzazione del progetto in Android Studio espandere **App** > **src** > **main** > **res** > **layout**. Aprire il file di layout `activity_main.xml` e fare clic sulla scheda **Text** per aggiornare il contenuto di testo del file. Aggiornarlo con il codice seguente che aggiunge i nuovi controlli `Button` e `EditText` per l'invio di messaggi di notifica push all'hub di notifica. Aggiungere questo codice alla fine, subito prima di `</RelativeLayout>`.

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

2. Aggiungere questa riga al file `build.gradle` sotto `android`

		useLibrary 'org.apache.http.legacy'

3. Nella finestra Project View di Android Studio espandere **App** > **src** > **main** > **res** > **values**. Aprire il file `strings.xml` e aggiungere i valori di stringa a cui fanno riferimento i nuovi controlli `Button` e `EditText`. Aggiungerli alla fine del file, subito prima di `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


4. Nel file `MainActivity.java` aggiungere le istruzioni `import` seguenti sopra la classe `MainActivity`.

		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;

		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

		import org.apache.http.HttpResponse;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;


5. Nel file `MainActivity.java` aggiungere i membri seguenti sopra la classe `MainActivity`.

	Aggiornare `HubFullAccess` con la stringa di connessione **DefaultFullSharedAccessSignature** per l'hub. Questa stringa di connessione può essere copiata dal [portale di Azure] facendo clic su **Criteri di accesso** nel pannello **Impostazioni** dell'hub di notifica.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

6. L'attività include il nome dell'hub e l'intera stringa di connessione di accesso condiviso per l'hub. È necessario creare un token SaS (Software Access Signature) per autenticare una richiesta POST per l'invio di messaggi all'hub di notifica. Per eseguire questa operazione, analizzare i dati della chiave dalla stringa di connessione e quindi creare il token di firma di accesso condiviso come indicato in [Concetti comuni](http://msdn.microsoft.com/library/azure/dn495627.aspx) nelle informazioni di riferimento sull'API REST.

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

7. In `MainActivity.java` aggiungere il metodo seguente alla classe `MainActivity` per creare un token di autenticazione della firma di accesso condiviso.

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
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }

            return null;
        }


8. In `MainActivity.java` aggiungere il metodo seguente alla classe `MainActivity` per gestire il clic del pulsante **Send Notification** e inviare il messaggio di notifica push all'hub usando l'API REST.

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
                        HttpClient client = new DefaultHttpClient();

                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(HubFullAccess);
                        String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                        HttpPost post = new HttpPost(url);

                        // Authenticate the POST request with the SaS token
                        post.setHeader("Authorization", generateSasToken(url));

                        // JSON content for GCM
                        post.setHeader("Content-Type", "application/json;charset=utf-8");

                        // Notification format should be GCM
                        post.setHeader("ServiceBusNotification-Format", "gcm");
                        post.setEntity(new StringEntity(json));

                        HttpResponse response = client.execute(post);
                    }
                    catch(Exception e)
                    {
                        DialogNotify("Exception",e.getMessage().toString());
                    }
                }
            }.start();
        }



##Testare l'app

####Notifiche push nell'emulatore

Per testare le notifiche push all'interno dell'emulatore, assicurarsi che l'immagine dell'emulatore supporti il livello Google API scelto per l'app. Se l'immagine non supporta Google APIs in modalità nativa, verrà generata l'eccezione **SERVICE\_NOT\_AVAILABLE**.

Verificare anche di avere aggiunto l'account Google all'emulatore in esecuzione in **Settings** > **Accounts**. In caso contrario, i tentativi di registrazione con GCM potrebbero generare l'eccezione **AUTHENTICATION\_FAILED**.

####Esecuzione dell'applicazione

1. Eseguire l'app e notare l'ID registrazione segnalato per una registrazione riuscita.

   	![Test in Android: registrazione di canali][18]

2. Immettere un messaggio di notifica da inviare a tutti i dispositivi Android registrati con l'hub.

   	![Test in Android: invio di un messaggio][19]

3. Premere **Send Notification**. Su tutti i dispositivi che eseguono l'app verrà visualizzata un'istanza di `AlertDialog` con il messaggio di notifica push. I dispositivi che non eseguono l'app, ma che sono stati registrati in precedenza per le notifiche push, riceveranno una notifica in Android Notification Manager. Per visualizzarle, scorrere verso il basso dall'angolo superiore sinistro.

   	![Test in Android: notifiche][21]

##Passaggi successivi

Come passaggio successivo, è consigliabile vedere l'esercitazione [Uso di Hub di notifica di Azure per inviare notifiche agli utenti]. Verrà illustrato come inviare notifiche da un back-end ASP.NET destinate a utenti specifici.

Per segmentare gli utenti per gruppi di interesse, vedere l'esercitazione [Usare Hub di notifica per inviare le ultime notizie].

Per altre informazioni generali sull'uso di Hub di notifica, vedere [Panoramica dell'Hub di notifica].

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
[Panoramica dell'Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Uso di Hub di notifica di Azure per inviare notifiche agli utenti]: notification-hubs-aspnet-backend-android-notify-users.md
[Usare Hub di notifica per inviare le ultime notizie]: notification-hubs-aspnet-backend-android-breaking-news.md
[portale di Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0316_2016-->