<properties urlDisplayName="Get Started" pageTitle="Introduzione ad Hub di notifica di Azure" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="10/03/2014" ms.author="piyushjo" />

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu" class="current">Baidu</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Baidu è un servizio push cloud cinese che è possibile usare per inviare notifiche push ai dispositivi mobili. Questo servizio è particolarmente utile in Cina, dove il recapito delle notifiche push ad Android è un'operazione complessa a causa della presenza di diversi app store e servizi push, nonché della disponibilità di dispositivi Android che in genere non sono connessi a GCM (Google Cloud Messaging). 

Per completare questa esercitazione, è necessario disporre di:

+ Android SDK (si presuppone che verrà usato Eclipse), che è possibile scaricare facendo clic su <a href="http://go.microsoft.com/fwlink/?LinkId=389797">questo collegamento</a>
+ [Mobile Services Android SDK]
+ [Android SDK del servizio push Baidu]

>[WACOM.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

* [Creare un account Baidu](#createBaiduAccount)
* [Registrarsi come sviluppatore Baidu](#registerBaiduDeveloper)
* [Creare un progetto push cloud Baidu](#createBaiduPushProject)
* [Configurare l'hub di notifica](#configure-hub)
* [Connettere l'app all'hub di notifica](#connecting-app)
* [Inviare notifiche all'app](#send)

##<a id="createBaiduAccount"></a>Creare un account Baidu

Per usare Baidu, è necessario creare un account. Se si ha già uno account, accedere al [portale di Baidu] con l'account Baidu e andare al passaggio successivo; diversamente, vedere le istruzioni seguenti su come creare un nuovo account Baidu.  

1. Passare al [portale di Baidu] e fare clic sul link 登录 (Accedi). Fare clic su 立即注册 per avviare il processo di registrazione di un nuovo account. 

   	![][1]

2. Immettere i dettagli richiesti: telefono/indirizzo e-mail, password e codice di verifica, quindi fare clic sul pulsante di iscrizione.

   	![][2]

3. All'indirizzo e-mail specificato verrà inviato un messaggio un'e-mail contenente un collegamento per attivare l'account Baidu. 

   	![][3]

4. Accedere all'account e-mail, aprire il messaggio di attivazione di Baidu e fare clic sul collegamento di attivazione per attivare l'account Baidu. 

   	![][4]

Dopo aver attivato l'account Baidu, accedere al [portale di Baidu] con l'account. 

##<a id="registerBaiduDeveloper"></a>Registrarsi come sviluppatore Baidu

1. Dopo aver eseguito l'accesso al [portale di Baidu], fare clic su **更多>> (Altro)**.

  	![][5]

2. Scorrere la sezione **站长与开发者服务 (Servizi per webmaster e sviluppatori)** e fare clic su **百度开放云平台 (Piattaforma cloud aperta Baidu)**. 

  	![][6]

3. Nella pagina successiva fare clic su **开发者服务 (Servizi per sviluppatori)** nell'angolo superiore destro. 

  	![][7]

4. Nella pagina successiva fare clic su **注册开发者 (Sviluppatori registrati)** dal menu nell'angolo superiore destro. 

  	![][8]

5. Immettere il proprio nome, la descrizione e un numero di telefono cellulare per ricevere un messaggio di testo di verifica e quindi fare clic su **送验证码 (Invia codice di verifica)**. Si noti che per i numeri di telefono internazionali è necessario racchiudono il codice paese tra parentesi; per un numero degli Stati Uniti, ad esempio, il codice sarà **(1)1234567890**.

  	![][9]

6. Si riceverà quindi un messaggio di testo con un numero di verifica, come mostrato nell'esempio seguente:

  	![][10] 

7. Immettere il numero di verifica del messaggio in **验证码 (Codice di conferma)**. 

8. Infine, completare la registrazione come sviluppatore accettando il contratto Baidu e facendo clic su **提交 (Invia)**. Al termine della registrazione, viene visualizzata la pagina seguente:

  	![][11] 

##<a id="createBaiduPushProject"></a>Creare un progetto push cloud Baidu

Quando si crea un progetto push cloud Baidu, viene visualizzato l'ID dell'app, la chiave API e la chiave segreta.

1. Dopo aver eseguito l'accesso al [portale di Baidu], fare clic su **更多>> (Altro)**.

  	![][5]

2. Scorrere la sezione **站长与开发者服务 (Servizi per webmaster e sviluppatori)**  e fare clic su **百度开放云平台 (Piattaforma cloud aperta Baidu)**. 

  	![][6]

3. Nella pagina successiva fare clic su **开发者服务 (Servizi per sviluppatori)** nell'angolo superiore destro. 

  	![][7]

4. Nella pagina successiva fare clic su **云推送 (Push cloud)** dalla sezione **云服务 (Servizi cloud)**. 

  	![][12]

5. Al termine della registrazione come sviluppatore viene visualizzato **管理控制台 (Sistema di gestione)** nel menu superiore. Fare clic su **开发者服务管理 (Gestione dei servizi per gli sviluppatori)**. 

  	![][13]

6. Nella pagina successiva fare clic su **创建工程 (Crea progetto)**.

  	![][14]

7. Immettere un nome di applicazione e fare clic su **创建 (Crea)**.

  	![][15]

8. Al termine della creazione verrà visualizzata una pagina con le voci **AppID**, **API Key** e **Secret Key**. Prendere nota dei valori di **API key** e di **Secret key**, che verranno usati in seguito. 

  	![][16]

9. Configurare il progetto per le notifiche push facendo clic su **云推送 (Push cloud)** nel riquadro a sinistra. 

  	![][31]

10. Nella pagina successiva fare clic sul pulsante **推送设置 (Impostazioni push)**.

	![][32]  

11. Nella pagina di configurazione aggiungere il nome che verrà usato nel progetto Android nel campo **应用包名 (Pacchetto applicazione)** , quindi fare clic su **保存设置 (Salva)**  

	![][33]

Verrà visualizzato il messaggio **保存成功！(Salvataggio eseguito correttamente)**.

##<a id="configure-hub"></a>Configurare l'hub di notifica

1. Accedere al [portale di gestione di Azure] e quindi fare clic sul pulsante **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, quindi **Hub di notifica** e infine **Creazione rapida**.
 
3. Specificare un nome per **Hub di notifica**, selezionare la **Regione** e lo **Spazio dei nomi** in cui verrà creato l'hub di notifica e quindi fare clic su **Crea un nuovo hub di notifica**  

  	![][17]

4. Scegliere lo spazio dei nomi in cui è stato creato l'hub di notifica e quindi fare clic su **Hub di notifica** nella parte superiore. 

  	![][18]

5. Selezionare l'hub di notifica creato e fare clic su **Configura** dal menu superiore.

  	![][19]

6. Scorrere verso il basso fino alla sezione **Impostazioni di notifica Baidu** e immettere la **Chiave API** e la **Chiave privata** ottenuti in precedenza dalla console Baidu per il progetto push cloud Baidu. Dopo aver immesso questi valori, fare clic su **Salva**. 

  	![][20]

7. Fare clic sulla scheda **Dashboard** nella parte superiore per visualizzare l'hub di notifica e fare clic su **Visualizza stringa di connessione**.

  	![][21]

8. Prendere nota delle stringhe **DefaultListenSharedAccessSignature** e **DefaultFullSharedAccessSignature** dalla finestra Accedi a informazioni di connessione. 

    ![][22]

##<a id="connecting-app"></a>Connettere l'app all'hub di notifica

1. Nel plug-in ADT per Eclipse, creare un nuovo progetto Android (File -> New -> Android Application).

    ![][23]

2. Immettere un nome dell'applicazione in **Application Name** e verificare che la versione di **Minimum Required SDK** sia impostata su **API 16: Android 4.1**.

    ![][24]

3. Fare clic su **Avanti** e continuare a seguire la procedura guidata fino alla finestra **Create Activity**. Verificare che la voce **Blank Activity** sia selezionata e infine selezionare **Finish** per creare una nuova applicazione Android. 

    ![][25]

4. Verificare che l'opzione **Project Build Target** sia impostata correttamente.

    ![][26]

5. Scaricare e decomprimere [Mobile Services Android SDK], aprire la cartella **notificationhubs**, copiare il file **notification-hubs-x.y.jar** nella cartella *libs* del progetto Eclipse e aggiornare la cartella *libs*.

6. Scaricare e decomprimere [Baidu Push Android SDK], aprire la cartella **libs** e copiare il file *pushservice-x.y.z*.jar file e le cartelle *armeabi* e *mips* nella cartella **libs** dell'applicazione Android. 

    ![][27]

7. Aprire il file **AndroidManifest.xml** del progetto Android e aggiungere le autorizzazioni richieste dall'SDK Baidu.

	    <uses-permission android:name="android.permission.INTERNET" />
	    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
	    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
	    <uses-permission android:name="android.permission.VIBRATE" />
	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
	    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
	    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Aggiungere la proprietà *android:name* all'elemento *application* nel file **AndroidManifest.xml** sostituendo *yourprojectname*, ad esempio con **com.example.BaiduTest**. Assicurarsi che il nome del progetto corrisponda a quello configurato nella console di Baidu. 

		<application android:name="yourprojectname.DemoApplication"

9. Aggiungere la configurazione seguente all'interno dell'elemento dell'applicazione dopo l'elemento dell'attività .MainActivity che sostituisce *yourprojectname*, ad es. **com.example.BaiduTest**:

		<receiver android:name="yourprojectname.MyPushMessageReceiver">
		    <intent-filter>
		        <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
		        <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
		        <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
		    </intent-filter>
		</receiver>
		
		<receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
		    android:process=":bdservice_v1">
		    <intent-filter>
		        <action android:name="android.intent.action.BOOT_COMPLETED" />
		        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
				<action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
		    </intent-filter>
		</receiver>
        
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>                   
        </receiver>
        
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Aggiungere una nuova classe denominata **ConfigurationSettings.java** al progetto. 

    ![][28]

    ![][29]

10. Aggiungere alla classe il codice seguente:

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	Impostare il valore di *API_KEY* con quello recuperato in precedenza dal progetto cloud Baidu, *NotificationHubName* con il nome dell'hub di notifica del portale di Azure e *NotificationHubConnectionString* con la stringa DefaultListenSharedAccessSignature predefinita del portale di Azure. 

11. Aggiungere una nuova classe denominata **DemoApplication.java** e aggiungervi il codice seguente:

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Aggiungere una nuova classe denominata **MyPushMessageReceiver.java** e aggiungere il codice sotto di essa. Questa è la classe che gestisce le notifiche push ricevute dal server push di Baidu:

		import java.util.List;
		import android.content.Context;
		import android.os.AsyncTask;
		import android.util.Log;
		import com.baidu.frontia.api.FrontiaPushMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
		    /** TAG to Log */
			public static NotificationHub hub = null;
			public static String mChannelId, mUserId;
		    public static final String TAG = MyPushMessageReceiver.class
		            .getSimpleName();
		    
			@Override
		    public void onBind(Context context, int errorCode, String appid,
		            String userId, String channelId, String requestId) {
		        String responseString = "onBind errorCode=" + errorCode + " appid="
		                + appid + " userId=" + userId + " channelId=" + channelId
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		        mChannelId = channelId;
		        mUserId = userId;
		        
		        try {
		       	 if (hub == null) {
		                hub = new NotificationHub(
		                		ConfigurationSettings.NotificationHubName, 
		                		ConfigurationSettings.NotificationHubConnectionString, 
		                		context);
		                Log.i(TAG, "Notification hub initialized");
		            }
		        } catch (Exception e) {
		           Log.e(TAG, e.getMessage());
		        }
		        
		        registerWithNotificationHubs();
			}
		    
		    private void registerWithNotificationHubs() {
		       new AsyncTask<Void, Void, Void>() {
		          @Override
		          protected Void doInBackground(Void... params) {
		             try {
		            	 hub.registerBaidu(mUserId, mChannelId);
		            	 Log.i(TAG, "Registered with Notification Hub - '" 
		     	    			+ ConfigurationSettings.NotificationHubName + "'" 
		     	    			+ " with UserId - '"
		     	    			+ mUserId + "' and Channel Id - '"
		     	    			+ mChannelId + "'");
		             } catch (Exception e) {
		            	 Log.e(TAG, e.getMessage());
		             }
		             return null;
		         }
		       }.execute(null, null, null);
		    }
		    
		    @Override
		    public void onSetTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onSetTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onDelTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onDelTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onListTags(Context context, int errorCode, List<String> tags,
		            String requestId) {
		        String responseString = "onListTags errorCode=" + errorCode + " tags="
		                + tags;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onUnbind(Context context, int errorCode, String requestId) {
		        String responseString = "onUnbind errorCode=" + errorCode
		                + " requestId = " + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onNotificationClicked(Context context, String title,
		            String description, String customContentString) {
		        String notifyString = "title=\"" + title + "\" description=\""
		                + description + "\" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }
		
		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. Aprire **MainActivity.java** e aggiungere quanto segue al metodo **onCreate**:

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

e aggiungere le istruzioni import seguenti nella parte superiore:
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##<a id="send"></a>Inviare notifiche all'app

È possibile inviare notifiche usando Hub di notifica da qualsiasi back-end che usi l'<a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione verrà illustrato l'uso di un'app console .NET. 

1. Creare una nuova applicazione console in Visual C#:

	![][30]

2. Aggiungere un riferimento ad Azure Service Bus SDK con il <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacchetto NuGet WindowsAzure.ServiceBus</a>. Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare quanto segue e premere Invio:

        Install-Package WindowsAzure.ServiceBus

3. Aprire il file Program.cs e aggiungere l'istruzione using seguente:

        using Microsoft.ServiceBus.Notifications;

4. Nella classe `Program` aggiungere il metodo seguente e sostituire la stringa *DefaultFullSharedAccessSignatureSASConnectionString* e *NotificationHubName* con i valori disponibili. 

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. Aggiungere quindi le righe seguenti nel metodo Main:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="test-app"></a>Testare l'app

Per testare l'app con un telefono effettivo, collegare il telefono al computer con un cavo USB.

Per testare l'app con l'emulatore:

1. Nella barra degli strumenti superiore di Eclipse, fare clic su Run e quindi selezionare l'app. 

2. L'app verrà caricata nel telefono collegato oppure verrà avviato l'emulatore nel quale verrà caricata ed eseguita l'app.

3. L'app recupera i valori 'userId' e 'channelId' dal servizio di notifica push Baidu ed effettua la registrazione con l'hub di notifica.
	
4.	Per inviare una notifica di test quando si usa l'applicazione console .Net, è sufficiente premere il tasto F5 in Visual Studio per eseguire l'applicazione; verrà inviata una notifica che sarà visualizzata nell'area di notifica superiore del dispositivo o dell'emulatore. 


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Android SDK del servizio push Baidu]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale Baidu]: http://www.baidu.com/








	
