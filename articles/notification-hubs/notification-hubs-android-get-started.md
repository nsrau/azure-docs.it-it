<properties
	pageTitle="Introduzione ad Hub di notifica di Azure | Microsoft Azure"
	description="In questa esercitazione, si apprenderà come usare Hub di notifica di Azure per inviare notifiche push a dispositivi Android."
	services="notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="09/01/2015"
	ms.author="wesmc"/>

# Introduzione ad Hub di notifica

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Panoramica

Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Android. In questa esercitazione verrà creata un'app per Android vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM). Al termine, sarà possibile utilizzare l’hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app.

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica. Seguire anche l'esercitazione successiva per imparare a usare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici.


##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

+ Android Studio, che è possibile scaricare dal <a href="http://go.microsoft.com/fwlink/?LinkId=389797">sito Android</a>.
+ Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).


Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Android.


##Creare un progetto che supporta Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##Configurare un nuovo hub di notifica

[AZURE.INCLUDE [notification-hubs-android-configure-push](../../includes/notification-hubs-android-configure-push.md)]

##<a id="connecting-app"></a>Connettere l'app all'hub di notifica

###Creare un nuovo progetto Android

1. In Android Studio avviare un nuovo progetto Android Studio.

   	![][13]

2. Scegliere il fattore di forma **Phone and Tablet** e la versione **Minimum SDK** che si vuole supportare. Quindi fare clic su **Next**.

   	![][14]

3. Scegliere **Blank Activity** per l'attività principale, fare clic su **Avanti**, quindi fare clic su **Fine**.

###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Aggiungere Play Services](../../includes/mobile-services-add-google-play-services.md)]

###Aggiungere codice

1. Scaricare <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">Notification Hubs Android SDK</a>. Estrarre il file ZIP e copiare **notificationhubs\\notification-hubs-0.4.jar** e **notifications\\notifications-1.0.1.jar** nella directory **app\\libs** del progetto. A questo scopo è possibile trascinare i file direttamente nella cartella **libs** nella finestra Project View di Android Studio. Aggiornare la cartella **libs**.



	La documentazione di riferimento per questi due pacchetti è accessibile tramite i collegamenti seguenti: * [com.microsoft.windowsazure.messaging](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/messaging/package-summary.html) * [com.microsoft.windowsazure.notifications](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/notifications/package-summary.html)


    > [AZURE.NOTE]È possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive dell'SDK.

2. Configurare l'applicazione per ottenere un *registrationId* da GCM e usare questo ID per registrare l'istanza dell'app nell'hub di notifica.

	Nel file AndroidManifest.xml aggiungere le autorizzazioni seguenti sotto il tag `</application>`. Assicurarsi di sostituire `<your package>` con il nome del pacchetto visualizzato all'inizio del file AndroidManifest.xml file (`com.example.testnotificationhubs` in questo esempio).

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


4. Aggiungere i seguenti membri privati nella parte superiore della classe.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";
	    private static Boolean isVisible = false;


	Assicurarsi di aggiornare i tre segnaposto: * **SENDER\_ID**: impostare `SENDER_ID` sul numero di progetto ottenuto in precedenza dal progetto creato in [Google Cloud Console](http://cloud.google.com/console). * **HubListenConnectionString**: Impostare `HubListenConnectionString` sulla stringa di connessione **DefaultListenAccessSignature** per l'hub. Per copiare la stinga di connessione, è possibile fare clic su **Visualizza stringa di connessione** nella scheda **Dashboard** dell'hub nel [portale di Azure]. * **HubName**: usare il nome dell'hub di notifica visualizzato nella parte superiore della pagina in Azure per l'hub (**non** l'URL completo). Ad esempio, usare `"myhub"`.



5. Nel metodo **OnCreate** cella classe **MainActivity** aggiungere il codice seguente per eseguire la registrazione al momento della creazione dell'attività.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. In **MainActivity.java**, aggiungere il codice seguente per il metodo **registerWithNotificationHubs()**. Questo metodo segnala che l'operazione è riuscita dopo la registrazione con Google Cloud Messaging e l'hub di notifica.

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    DialogNotify("Registered Successfully","RegId : " +
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	DialogNotify("Exception",e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}


7. Aggiungere il metodo **DialogNotify** all’attività per visualizzare la notifica quando l'applicazione è in esecuzione e visibile. Sostituire anche **onStart** e **onStop** per determinare se l'attività è visibile per visualizzare la finestra di dialogo.

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }


		/**
		  * A modal AlertDialog for displaying a message on the UI thread
		  * when there's an exception or message to report.
		  *
		  * @param title   Title for the AlertDialog box.
		  * @param message The message displayed for the AlertDialog box.
		  */
    	public void DialogNotify(final String title,final String message)
    	{
	        if (isVisible == false)
	            return;

        	final AlertDialog.Builder dlg;
        	dlg = new AlertDialog.Builder(this);

        	runOnUiThread(new Runnable() {
            	@Override
            	public void run() {
                	AlertDialog dlgAlert = dlg.create();
                	dlgAlert.setTitle(title);
                	dlgAlert.setButton(DialogInterface.BUTTON_POSITIVE,
						(CharSequence) "OK",
						new DialogInterface.OnClickListener() {
                            public void onClick(DialogInterface dialog, int which) {
                                dialog.dismiss();
                            }
                        });
                	dlgAlert.setMessage(message);
                	dlgAlert.setCancelable(false);
                	dlgAlert.show();
            	}
        	});
    	}

8. Poiché il sistema operativo Android non consente la visualizzazione di notifiche, è necessario scrivere un apposito ricevitore. In **AndroidManifest.xml** aggiungere l'elemento seguente all'interno dell'elemento `<application>`.

	> [AZURE.NOTE]Sostituire il segnaposto con il nome del pacchetto.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


9. Nella visualizzazione del progetto espandere **app** -> **src** -> **main** -> **java**. Fare clic con il pulsante destro del mouse sulla cartella del pacchetto in **java**, fare clic su **New** e quindi su **Java Class**.

	![][6]

10. Nel campo **Name** digitare **MyHandler** per la nuova classe, quindi fare clic su **OK**.


11. Aggiungere le istruzioni di importazione seguenti all'inizio di **MyHandler.java**:

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

	Questo codice ignora il metodo `OnReceive` in modo che il gestore visualizzi `AlertDialog` per mostrare le notifiche ricevute. Il gestore invia la notifica anche al gestore delle notifiche di Android usando il metodo `sendNotification()`.

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
        	mainActivity.DialogNotify("Received Notification",nhMessage);
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

14. In Android Studio, sulla barra dei menu, fare clic su **Build** -> **Rebuild Project** per assicurarsi che non vengano rilevati errori.

##Inviare notifiche



È possibile testare la ricezione delle notifiche nell'app mediante l'invio di notifiche nel portale di Azure, usando la scheda Debug nell'hub di notifica, come illustrato nella schermata seguente.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. Nella visualizzazione del progetto in Android Studio espandere **App** -> **src** -> **main** -> **res** -> **layout**. Aprire il file di layout **activity\_main.xml** e fare clic sulla scheda **Text** per aggiornare il contenuto di testo del file. Aggiornarlo con il codice seguente che aggiunge i nuovi controlli `Button` e `EditText` per l'invio di messaggi di notifica all'hub di notifica. Aggiungere questo codice alla fine, subito prima di `</RelativeLayout>`.

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

2. Nella visualizzazione del progetto in Android Studio espandere **App** > **src** > **main** > **res** > **values**. Aprire il file **strings.xml** e aggiungere i valori di stringa a cui fanno riferimento i nuovi controlli `Button` e `EditText`. Aggiungerli alla fine del file, subito prima di `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. Nel file **MainActivity.java** aggiungere le istruzioni `import` seguenti sopra la classe `MainActivity`.

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


3. Nel file **MainActivity.java** aggiungere i membri seguenti all'inizio della classe `MainActivity`.

	Immettere il nome dell'hub per `HubName`, non lo spazio dei nomi. Ad esempio, immettere "myhub". Immettere anche la stringa di connessione **DefaultFullSharedAccessSignature**. Questa stringa di connessione può essere copiata dal [portale di Azure] facendo clic su **Visualizza stringa di connessione** nella scheda **Dashboard** dell'hub di notifica.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. L'attività include il nome dell'hub e l'intera stringa di connessione di accesso condiviso per l'hub. È necessario creare un token SaS (Software Access Signature) per autenticare una richiesta POST per l'invio di messaggi all'hub di notifica. Per eseguire questa operazione, analizzare i dati della chiave dalla stringa di connessione e quindi creare il token SaS come indicato nelle informazioni di riferimento sull'API REST in [Concetti comuni](http://msdn.microsoft.com/library/azure/dn495627.aspx).

	In **MainActivity.java** aggiungere il metodo seguente alla classe `MainActivity` per analizzare la stringa di connessione.

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

5. In **MainActivity.java** aggiungere il metodo seguente alla classe `MainActivity` per creare un token di autenticazione SaS.

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


6. In **MainActivity.java** aggiungere il metodo seguente alla classe `MainActivity` per gestire il clic del pulsante **Send Notification** e inviare il messaggio di notifica all'hub mediante l'API REST.

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



##Test dell'app

####Test dell'emulatore

Per testare un emulatore, assicurarsi che l'immagine dell'emulatore supporti il livello Google API scelto per l'app. Se l'immagine non supporta Google APIs, verrà generata l'eccezione **SERVICE\_NOT\_AVAILABLE**.

Accertarsi anche di avere aggiunto l'account Google all'emulatore in esecuzione in **Settings** > **Accounts**. In caso contrario, i tentativi di registrazione con GCM potrebbero generare l'eccezione **AUTHENTICATION\_FAILED**.

####Testare l'app

1. Eseguire l'app e notare l'ID registrazione segnalato per una registrazione riuscita.

   	![][18]

2. Immettere un messaggio di notifica da inviare a tutti i dispositivi Android registrati con l'hub.

   	![][19]

3. Premere **Send Notification**. Tutti i dispositivi con l'app in esecuzione visualizzeranno una `AlertDialog` con il messaggio di notifica. I dispositivi in cui l'app non è in esecuzione , ma che sono stati registrati in precedenza per le notifiche, riceveranno una notifica che sarà aggiunta all'utilità di gestione delle notifiche. Per visualizzare le notifiche, scorrere verso il basso dall'angolo superiore sinistro.

   	![][21]

##Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Android. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti]. Se si desidera segmentare gli utenti per gruppi di interesse, consultare [Uso di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica].


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
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
[portale di Azure]: https://manage.windowsazure.com/
[linee guida su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Usare Hub di notifica per inviare notifiche push agli utenti]: notification-hubs-aspnet-backend-android-notify-users.md
[Uso di Hub di notifica per inviare le ultime notizie]: notification-hubs-aspnet-backend-android-breaking-news.md

<!---HONumber=September15_HO1-->