<properties title="Azure Notification Hubs Notify Users" pageTitle="Hub di notifica di Azure - Notifiche agli utenti" metaKeywords="notifiche push di Azure, hub di notifica Azure" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="elioda" manager="timlt" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda" />

#Hub di notifica di Azure - Notifiche agli utenti

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/it-it/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android" class="current">Android</a>
</div>

Il supporto per le notifiche push in Azure consente di accedere a un'infrastruttura push facile da usare, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili. Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Per autenticare i client e generare le notifiche viene usato un back-end di API Web ASP.NET, come illustrato nell'argomento [Registrazione dal back-end dell'app](http://msdn.microsoft.com/it-it/library/dn743807.aspx). Questa esercitazione si basa sull'hub di notifica creato nell'esercitazione **Introduzione ad Hub di notifica**.

> [AZURE.NOTE] In questa esercitazione si presuppone che l'utente abbia creato e configurato l'hub di notifica come descritto in [Introduzione ad Hub di notifica (Android)](/it-it/documentation/articles/notification-hubs-android-get-started/).

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Creare il progetto Android

Il passaggio successivo consiste nella creazione dell'applicazione per Android.

1. Seguire l'esercitazione [Introduzione ad Hub di notifica (Android)](/it-it/documentation/articles/notification-hubs-android-get-started/) per creare e configurare l'app per la ricezione di notifiche push da GCM.

2. Aprire il file res/layout/activity_main.xml e sostituire il contenuto con il seguente:
			
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    tools:context="com.example.notifyusers.MainActivity"
		    android:orientation="vertical">
		    <EditText
		        android:id="@+id/usernameText"
		        android:layout_width="match_parent"
		        android:layout_height="wrap_content"
		        android:ems="10"
		        android:hint="@string/usernameHint" >
		    </EditText>
		    <EditText
		        android:id="@+id/passwordText"
		        android:layout_width="match_parent"
		        android:layout_height="wrap_content"
		        android:ems="10"
		        android:hint="@string/passwordHint"
		        android:inputType="textPassword" />
		    <Button
		        android:id="@+id/buttonLogin"
		        android:layout_width="wrap_content"
		        android:layout_height="wrap_content"
		        android:text="@string/loginButton"
		        android:onClick="login" />
		    <Button
		        android:id="@+id/buttonSend"
		        android:layout_width="wrap_content"
		        android:layout_height="wrap_content"
		        android:text="@string/sendButton"
		        android:onClick="sendPush" />
		</LinearLayout>

2. Aprire il file res/values/strings.xml e aggiungere le righe seguenti:

		<string name="usernameHint">Username</string>
	    <string name="passwordHint">Password</string>
	    <string name="loginButton">1. Log in</string>
	    <string name="sendButton">2. Send push</string>

	A questo punto il layout grafico del file main_activity.xml dovrebbe essere simile al seguente:

	![][A1]

3. Creare una classe **RegisterClient** nello stesso pacchetto della classe **MainActivity**. Sostituire `{backend endpoint}` con l'endpoint back-end ottenuto nella sezione precedente.

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
			private static final String BACKEND_ENDPOINT = "{backend endpoint}/api/register";
			SharedPreferences settings;
			protected HttpClient httpClient;
			private String authorizationHeader;
		
			public RegisterClient(Context context) {
				super();
				this.settings = context.getSharedPreferences(PREFS_NAME, 0);
				httpClient =  new DefaultHttpClient();
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
				HttpPut request = new HttpPut(BACKEND_ENDPOINT+"/"+registrationId);
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
				
				HttpUriRequest request = new HttpPost(BACKEND_ENDPOINT+"?handle="+handle);
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

	Questo componente implementa le chiamate REST necessarie per contattare il back-end dell'app allo scopo di effettuare la registrazione per le notifiche push. Archivia inoltre in locale i *registrationId* creati dall'hub di notifica, come illustrato in [Registrazione dal back-end dell'app](http://msdn.microsoft.com/it-it/library/dn743807.aspx). Si noti che usa un token di autorizzazione memorizzato nell'archivio locale quando si fa clic sul pulsante **Esegui accesso e registrazione**.

4. Nella classe **MainActivity** rimuovere i campi privati per **NotificationHub** e aggiungere un campo per **RegisterClient**:

		//private NotificationHub hub;
		private RegisterClient registerClient;
 
5. A questo punto, nel metodo **onCreate** rimuovere l'inizializzazione del campo **hub** e il metodo **registerWithNotificationHubs**. Aggiungere quindi le righe seguenti, che inizializzano un'istanza della classe **RegisterClient**. Il metodo deve contenere le righe seguenti:

		@Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        
	        NotificationsManager.handleNotifications(this, SENDER_ID,
					MyHandler.class);
	        gcm = GoogleCloudMessaging.getInstance(this);
	        
	        registerClient = new RegisterClient(this);
	        
	        setContentView(R.layout.activity_main);
	    }

6. Aggiungere quindi i metodi seguenti, assicurandosi di sostituire "{backend endpoint}" con l'endpoint back-end ottenuto nella sezione precedente.

	    @Override
	    protected void onStart() {
	    	super.onStart();
	    	Button sendPush = (Button) findViewById(R.id.buttonSend);
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
						Log.e("MainActivity", "Failed to register - " + e.getMessage());
						return e;
					}
					return null;
				}
	
				protected void onPostExecute(Object result) {
					Button sendPush = (Button) findViewById(R.id.buttonSend);
			        sendPush.setEnabled(true);
					Toast.makeText(context, "Logged in and registered.",
							Toast.LENGTH_LONG).show();
				}
			}.execute(null, null, null);
	    }
	    
	    public void sendPush(View view) throws ClientProtocolException, IOException {
	    	new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						HttpUriRequest request = new HttpPost("{backend endpoint}/api/notifications");
						request.addHeader("Authorization", "Basic "+getAuthorizationHeader());
						HttpResponse response = new DefaultHttpClient().execute(request);
						if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
							Log.e("MainActivity", "Error sending notification" + response.getStatusLine().getStatusCode());
							throw new RuntimeException("Error sending notification");
						}
					} catch (Exception e) {
						Log.e("MainActivity", "Failed to send notification - " + e.getMessage());
						return e;
					}
					return null;
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

	Il callback per **Log in** genera un token di autenticazione basato sul nome utente e la password di input (si noti che rappresenta qualsiasi token usato dallo schema di autenticazione), quindi usa `RegisterClient` per chiamare il back-end. Il callback per **Send push** chiama il back-end per attivare una notifica sicura a tutti i dispositivi dell'utente. 

## Esecuzione dell'applicazione

Per eseguire l'applicazione, eseguire le operazioni seguenti:

1. In Eclipse eseguire l'app su un dispositivo Android fisico o sull'emulatore.

2. Nell'interfaccia utente dell'app per Android immettere un nome utente e una password. Può trattarsi di qualsiasi stringa, ma devono avere lo stesso valore.

3. Nell'interfaccia utente dell'app per Android fare clic su **Log in**. Fare clic su **Invia notifica push**.


[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users1.PNG
