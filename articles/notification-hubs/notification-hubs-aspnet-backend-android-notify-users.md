<properties
	pageTitle="Uso di Hub di notifica di Azure per inviare notifiche agli utenti per Android con back-end .NET"
	description="Informazioni su come inviare notifiche push agli utenti in Azure. Esempi di codice scritti in Java per Android"
	documentationCenter="android"
	services="notification-hubs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="wesmc"/>

#Uso di Hub di notifica di Azure per inviare notifiche agli utenti per Android con back-end .NET


[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

##Panoramica

Il supporto per le notifiche push in Azure consente di accedere a un'infrastruttura push facile da usare, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili. In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Per autenticare i client e generare le notifiche viene usato un back-end di API Web ASP.NET, come illustrato nell'argomento [Registrazione dal back-end dell'app](notification-hubs-registration-management.md#registration-management-from-a-backend). Questa esercitazione si basa sull'hub di notifica creato nell'esercitazione [Introduzione ad Hub di notifica (Android)](notification-hubs-android-get-started.md).

> [AZURE.NOTE] In questa esercitazione si presuppone che l'utente abbia creato e configurato l'hub di notifica come descritto in [Introduzione ad Hub di notifica (Android)](notification-hubs-android-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Creare il progetto Android

Il passaggio successivo consiste nella creazione dell'applicazione per Android.

1. Seguire l'esercitazione [Introduzione ad Hub di notifica (Android)](notification-hubs-android-get-started.md) per creare e configurare l'app per la ricezione di notifiche push da GCM.

2. Aprire il file **res/layout/activity\_main.xml** e sostituire il contenuto con le definizioni seguenti.

    Verranno aggiunti nuovi controlli EditText per l'accesso come utente. Viene aggiunto anche un campo per un tag username che farà parte delle notifiche inviate:

		<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
            android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

        <EditText
            android:id="@+id/usernameText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/usernameHint"
            android:layout_above="@+id/passwordText"
            android:layout_alignParentEnd="true" />
        <EditText
            android:id="@+id/passwordText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/passwordHint"
            android:inputType="textPassword"
            android:layout_above="@+id/buttonLogin"
            android:layout_alignParentEnd="true" />
        <Button
            android:id="@+id/buttonLogin"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/loginButton"
            android:onClick="login"
            android:layout_above="@+id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:layout_marginBottom="24dp" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="WNS on"
            android:textOff="WNS off"
            android:id="@+id/toggleButtonWNS"
            android:layout_toLeftOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="GCM on"
            android:textOff="GCM off"
            android:id="@+id/toggleButtonGCM"
            android:checked="true"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="APNS on"
            android:textOff="APNS off"
            android:id="@+id/toggleButtonAPNS"
            android:layout_toRightOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessageTag"
            android:layout_below="@id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_tag_hint" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessage"
            android:layout_below="@+id/editTextNotificationMessageTag"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_hint" />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/send_button"
            android:id="@+id/sendbutton"
            android:onClick="sendNotificationButtonOnClick"
            android:layout_below="@+id/editTextNotificationMessage"
            android:layout_centerHorizontal="true" />
        </RelativeLayout>



3. Aprire il file **res/values/strings.xml** e sostituire la definizione `send_button` con le righe seguenti per ridefinire la stringa per `send_button` e aggiungere stringhe per gli altri controlli:

        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="send_button">2. Send Notification</string>
        <string name="notification_message_tag_hint">
			Recipient username tag
		</string>

	A questo punto il layout grafico del file main\_activity.xml dovrebbe essere simile al seguente:

	![][A1]

4. Creare una nuova classe denominata **RegisterClient** nello stesso pacchetto della classe `MainActivity`MainActivity. Usare il codice seguente per il nuovo file di classe.

		import java.io.IOException;
        import java.io.UnsupportedEncodingException;
        import java.util.Set;

        import org.apache.http.HttpResponse;
        import org.apache.http.HttpStatus;
        import org.apache.http.client.ClientProtocolException;
        import org.apache.http.client.HttpClient;
        import org.apache.http.client.methods.HttpPost;
        import org.apache.http.client.methods.HttpPut;
        import org.apache.http.client.methods.HttpUriRequest;
        import org.apache.http.entity.StringEntity;
        import org.apache.http.impl.client.DefaultHttpClient;
        import org.apache.http.util.EntityUtils;
        import org.json.JSONArray;
        import org.json.JSONException;
        import org.json.JSONObject;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.util.Log;

        public class RegisterClient {
            private static final String PREFS_NAME = "ANHSettings";
            private static final String REGID_SETTING_NAME = "ANHRegistrationId";
            private String Backend_Endpoint;
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;

            public RegisterClient(Context context, String backendEnpoint) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
                Backend_Endpoint = backendEnpoint + "/api/register";
            }

            public String getAuthorizationHeader() {
                return authorizationHeader;
            }

            public void setAuthorizationHeader(String authorizationHeader) {
                this.authorizationHeader = authorizationHeader;
            }

            public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
                String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

                JSONObject deviceInfo = new JSONObject();
                deviceInfo.put("Platform", "gcm");
                deviceInfo.put("Handle", handle);
                deviceInfo.put("Tags", new JSONArray(tags));

                int statusCode = upsertRegistration(registrationId, deviceInfo);

                if (statusCode == HttpStatus.SC_OK) {
                    return;
                } else if (statusCode == HttpStatus.SC_GONE){
                    settings.edit().remove(REGID_SETTING_NAME).commit();
                    registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                    statusCode = upsertRegistration(registrationId, deviceInfo);
                    if (statusCode != HttpStatus.SC_OK) {
                        Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                        throw new RuntimeException("Error upserting registration");
                    }
                } else {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            }

            private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                    throws UnsupportedEncodingException, IOException,
                    ClientProtocolException {
                HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
                request.setEntity(new StringEntity(deviceInfo.toString()));
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                request.addHeader("Content-Type", "application/json");
                HttpResponse response = httpClient.execute(request);
                int statusCode = response.getStatusLine().getStatusCode();
                return statusCode;
            }

            private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
                if (settings.contains(REGID_SETTING_NAME))
                    return settings.getString(REGID_SETTING_NAME, null);

                HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                HttpResponse response = httpClient.execute(request);
                if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                    throw new RuntimeException("Error creating Notification Hubs registrationId");
                }
                String registrationId = EntityUtils.toString(response.getEntity());
                registrationId = registrationId.substring(1, registrationId.length()-1);

                settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

                return registrationId;
            }
        }

	Questo componente implementa le chiamate REST necessarie per contattare il back-end dell'app allo scopo di effettuare la registrazione per le notifiche push. Archivia inoltre in locale i *registrationId* creati dall'hub di notifica, come illustrato in [Registrazione dal back-end dell'app](notification-hubs-registration-management.md#registration-management-from-a-backend). Si noti che usa un token di autorizzazione memorizzato nell'archivio locale quando si fa clic sul pulsante **Log in**.

5. Nella classe `MainActivity` rimuovere o impostare come commento il campo privato per `NotificationHub` e aggiungere un campo per la classe `RegisterClient` e una stringa per l'endpoint del back-end ASP.NET. Assicurarsi di sostituire `<Enter Your Backend Endpoint>` con l'endpoint back-end effettivo ottenuto in precedenza: Ad esempio: `http://mybackend.azurewebsites.net`.


		//private NotificationHub hub;
		private RegisterClient registerClient;
	    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";


6. Nella classe `MainActivity`, all'interno del metodo `onCreate`, rimuovere o impostare come commento l'inizializzazione del campo `hub` e la chiamata al metodo `registerWithNotificationHubs`. Aggiungere quindi il codice per inizializzare un'istanza della classe `RegisterClient`. Il metodo deve contenere le seguenti righe:

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            MyHandler.mainActivity = this;
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);

            //hub = new NotificationHub(HubName, HubListenConnectionString, this);
            //registerWithNotificationHubs();

	        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

            setContentView(R.layout.activity_main);
        }

7. Nella classe `MainActivity` eliminare o impostare come commento tutto il metodo `registerWithNotificationHubs`. Non verrà usato in questa esercitazione.

8. Aggiungere le istruzioni `import` seguenti al file **MainActivity.java**.

		import android.widget.Button;
		import java.io.UnsupportedEncodingException;
		import android.content.Context;
		import java.util.HashSet;
		import android.widget.Toast;
		import org.apache.http.client.ClientProtocolException;
		import java.io.IOException;
		import org.apache.http.HttpStatus;


9. Aggiungere quindi i metodi seguenti per gestire l'evento clic del pulsante **Log in** e inviare le notifiche push.

	    @Override
	    protected void onStart() {
	    	super.onStart();
        	Button sendPush = (Button) findViewById(R.id.sendbutton);
	        sendPush.setEnabled(false);
	    }

		public void login(View view) throws UnsupportedEncodingException {
	    	this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

	    	final Context context = this;
	    	new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						String regid = gcm.register(SENDER_ID);
				        registerClient.register(regid, new HashSet<String>());
					} catch (Exception e) {
	                    DialogNotify("MainActivity - Failed to register", e.getMessage());
						return e;
					}
					return null;
				}

				protected void onPostExecute(Object result) {
                	Button sendPush = (Button) findViewById(R.id.sendbutton);
			        sendPush.setEnabled(true);
					Toast.makeText(context, "Logged in and registered.",
							Toast.LENGTH_LONG).show();
				}
			}.execute(null, null, null);
	    }

		private String getAuthorizationHeader() throws UnsupportedEncodingException {
			EditText username = (EditText) findViewById(R.id.usernameText);
	    	EditText password = (EditText) findViewById(R.id.passwordText);
	    	String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
	    	basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
	    	return basicAuthHeader;
		}

        /**
         * This method calls the ASP.NET WebAPI backend to send the notification message
         * to the platform notification service based on the pns parameter.
         *
         * @param pns     The platform notification service to send the notification message to. Must
         *                be one of the following ("wns", "gcm", "apns").
         * @param userTag The tag for the user who will receive the notification message. This string
         *                must not contain spaces or special characters.
         * @param message The notification message string. This string must include the double quotes
         *                to be used as JSON content.
         */
        public void sendPush(final String pns, final String userTag, final String message)
                throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {

                        String uri = BACKEND_ENDPOINT + "/api/notifications";
                        uri += "?pns=" + pns;
                        uri += "&to_tag=" + userTag;

                        HttpPost request = new HttpPost(uri);
                        request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                        request.setEntity(new StringEntity(message));
                        request.addHeader("Content-Type", "application/json");

                        HttpResponse response = new DefaultHttpClient().execute(request);

                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            DialogNotify("MainActivity - Error sending " + pns + " notification",
								response.getStatusLine().toString());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                        return e;
                    }

                    return null;
                }
            }.execute(null, null, null);
        }


	Il gestore `login` per il pulsante **Log in** genera un token di autenticazione di base usando il nome utente e la password di input (si noti che rappresenta qualsiasi token usato dallo schema di autenticazione), quindi usa `RegisterClient` per chiamare il back-end per la registrazione.

	Il metodo `sendPush` chiama il back-end per attivare una notifica sicura per l'utente in base al tag user. Il servizio di notifica della piattaforma a cui è destinato `sendPush` dipende dalla stringa `pns` passata.

10. Nella classe `MainActivity` aggiornare il metodo`sendNotificationButtonOnClick` per chiamare il metodo `sendPush` con i servizi di notifica della piattaforma selezionati dell'utente, come indicato di seguito.

        /**
         * Send Notification button click handler. This method sends the push notification
         * message to each platform selected.
         *
         * @param v The view
         */
        public void sendNotificationButtonOnClick(View v)
                throws ClientProtocolException, IOException {

            String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                    .getText().toString();
            String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                    .getText().toString();

            // JSON String
            nhMessage = """ + nhMessage + """;

            if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
            {
                sendPush("wns", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
            {
                sendPush("gcm", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
            {
                sendPush("apns", nhMessageTag, nhMessage);
            }
        }



## Eseguire l'applicazione


1. Eseguire l'applicazione su un dispositivo o un emulatore tramite Android Studio.

2. Nell'app per Android immettere un nome utente e una password. Devono avere lo stesso valore di stringa e non devono contenere spazi o caratteri speciali.

3. Nell'app per Android fare clic su **Log in**. Attendere un avviso popup che indica **Logged in and registered**. Verrà abilitato il pulsante **Send Notification**.

	![][A2]

4. Fare clic sugli interruttori per abilitare tutte le piattaforme in cui è stata eseguita l'app ed è stato registrato un utente.
5. Immettere il nome dell'utente che riceverà il messaggio di notifica. L'utente dovrà essere registrato per le notifiche nei dispositivi di destinazione.

6. Immettere un messaggio che l'utente riceverà come messaggio di notifica push.
7. Fare clic su **Send Notification**. Ogni dispositivo che ha una registrazione con il tag username corrispondente riceverà la notifica push.


[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png

<!---HONumber=AcomDC_0330_2016-->