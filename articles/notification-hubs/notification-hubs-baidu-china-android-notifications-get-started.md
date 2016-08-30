<properties
	pageTitle="Introduzione ad Hub di notifica di Azure tramite Baidu | Microsoft Azure"
	description="In questa esercitazione, si apprenderà come usare Hub di notifica di Azure per inviare notifiche push a dispositivi Android tramite Baidu."
	services="notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="mobile-baidu"
	ms.workload="mobile"
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Introduzione ad Hub di notifica tramite Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Overview

Baidu cloud push è un servizio cloud cinese che consente di inviare notifiche push ai dispositivi mobili. Questo servizio è particolarmente utile in Cina dove il recapito di notifiche push ad Android è complesso a causa della presenza di diversi Store per app e diversi servizi push, oltre alla disponibilità di dispositivi Android che non sono generalmente connessi a GCM (Google Cloud Messaging).

##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

+ Android SDK (si presuppone che verrà usato Eclipse), disponibile per il download dal <a href="http://go.microsoft.com/fwlink/?LinkId=389797">sito Android</a>
+ [Mobile Services Android SDK]
+ [Baidu Push Android SDK]

>[AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##Creare un account Baidu

Per usare Baidu, è necessario un account Baidu. Se è già disponibile un account, accedere al [portale di Baidu] e procedere al passaggio successivo. In caso contrario, vedere le istruzioni seguenti sulla creazione di un account Baidu.

1. Passare al [portale di Baidu] e fare clic sul collegamento **登录** (**Accesso**). Fare clic su **立即注册** per avviare il processo di registrazione di un nuovo account.

   	![][1]

2. Immettere i dettagli richiesti, ovvero numero di telefono/indirizzo di posta elettronica, password e codice di verifica, quindi fare clic su **Signup**.

   	![][2]

3. Si riceverà un messaggio di posta elettronica all'indirizzo specificato, contenente un collegamento per l'attivazione dell'account Baidu.

   	![][3]

4. Accedere all'account di posta elettronica, aprire il messaggio per l'attivazione di Baidu e fare clic sul collegamento per attivare l'account.

   	![][4]

Dopo l'attivazione di un account Baidu, accedere al [portale di Baidu].

##Registrarsi come sviluppatore Baidu

1. Dopo l'accesso al [portale di Baidu], fare clic su **更多>>** (**Altro**).

  	![][5]

2. Scorrere in basso nella sezione **站长与开发者服务 (Servizi per webmaster e sviluppatori)** e fare clic su **百度开放云平台** (**Piattaforma open cloud Baidu**).

  	![][6]

3. Nella pagina successiva fare clic su **开发者服务** (**Servizi per gli sviluppatori**) nell'angolo superiore destro.

  	![][7]

4. Nella pagina successiva fare clic su **注册开发者** (**Sviluppatori registrati**) nel menu nell'angolo superiore destro.

  	![][8]

5. Immettere nome, descrizione e numero di cellulare per ricevere un messaggio di testo di verifica, quindi fare clic su **送验证码** (**Invia codice di verifica**). Si noti che per i numeri di telefono internazionali è necessario inserire il codice paese tra parentesi. Ad esempio, per un numero degli Stati Uniti, sarà **(1)1234567890**.

  	![][9]

6. Si riceverà quindi un messaggio di testo con un numero di verifica, come mostrato nell'esempio seguente:

  	![][10]

7. Immettere il numero di verifica del messaggio in **验证码** (**Codice di conferma**).

8. Infine, completare la registrazione come sviluppatore accettando il contratto di Baidu e facendo clic su **提交** (**Invia**). Al termine della registrazione viene visualizzata la seguente pagina:

  	![][11]

##Creare un progetto di Baidu cloud push

Quando si crea un progetto push cloud Baidu, viene visualizzato l'ID dell'app, la chiave API e la chiave segreta.

1. Dopo l'accesso al [portale di Baidu], fare clic su **更多>>** (**Altro**).

  	![][5]

2. Scorrere in basso nella sezione**站长与开发者服务** (**Servizi per webmaster e sviluppatori**) e fare clic su **百度开放云平台** (**Piattaforma open cloud Baidu**).

  	![][6]

3. Nella pagina successiva fare clic su **开发者服务** (**Servizi per gli sviluppatori**) nell'angolo superiore destro.

  	![][7]

4. Nella pagina seguente fare clic su **云推送** (**Push cloud**) nella sezione **云服务** (**Servizi cloud**).

  	![][12]

5. Al termine della registrazione come sviluppatore viene visualizzato **管理控制台** (**Console di gestione**) nel menu in alto. Fare clic su **开发者服务管理** (**Gestione dei servizi per gli sviluppatori**).

  	![][13]

6. Nella pagina successiva fare clic su **创建工程** (**Crea progetto**).

  	![][14]

7. Immettere il nome di un'applicazione e fare clic su **创建** (**Crea**).

  	![][15]

8. Al termine della creazione viene visualizzata una pagina con **ID API**, **chiave API** e **chiave privata**. Annotare la chiave API e la chiave privata, che verranno usate più avanti.

  	![][16]

9. Configurare il progetto per le notifiche push facendo clic su **云推送** (**Push cloud**) nel riquadro a sinistra.

  	![][31]

10. Nella pagina successiva fare clic sul pulsante **推送设置** (**Impostazioni push**).

	![][32]

11. Nella pagina di configurazione aggiungere il nome del pacchetto che verrà usato nel progetto Android nel campo **应用包名** (**Pacchetto applicazione**), quindi fare clic su **保存设置** (**Salva**).

	![][33]

Verrà visualizzato il messaggio **保存成功！** (**Salvataggio completato!**).

##Configurazione dell'hub di notifica

1. Accedere al [portale di Azure classico] e quindi fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, **Bus di servizio**, **Hub di notifica** e infine su **Creazione rapida**.

3. Specificare un nome per **Hub di notifica**, selezionare un valore per **Area** e **Spazio dei nomi** per indicare dove verrà creato l'hub di notifica, quindi fare clic su **Crea un nuovo hub di notifica**.

  	![][17]

4. Selezionare lo spazio dei nomi in cui è stato creato l'hub di notifica, quindi fare clic su **Hub di notifica** nella parte superiore della schermata.

  	![][18]

5. Selezionare l'hub di notifica creato e scegliere **Configura** dal menu principale.

  	![][19]

6. Scorrere verso il basso fino alla sezione delle **impostazioni di notifica di Baidu** e immettere la chiave API e la chiave privata ottenute in precedenza dalla console di Baidu per il progetto di Baidu cloud push. Fare clic su **Save**.

  	![][20]

7. Fare clic sulla scheda **Dashboard** per l'hub di notifica nella parte superiore della schermata, quindi su **Visualizza stringa di connessione**.

  	![][21]

8. Annotare i valori **DefaultListenSharedAccessSignature** e **DefaultFullSharedAccessSignature** dalla finestra **Accedi a informazioni di connessione**.

    ![][22]

##Connettere l'app all'hub di notifica

1. Nel plug-in ADT per Eclipse creare un nuovo progetto Android (**File** > **New** > **Android Application Project**).

    ![][23]

2. Immettere un nome in **Application Name** e verificare che la versione di **Minimum Required SDK** sia impostata su **API 16: Android 4.1**.

    ![][24]

3. Fare clic su **Next** e continuare la procedura guidata fino alla visualizzazione della finestra **Crea attività**. Verificare che sia selezionata l'opzione **Blank Activity**, infine scegliere **Finish** per creare una nuova applicazione Android.

    ![][25]

4. Assicurarsi che l'opzione **Project Build Target** sia impostata correttamente.

    ![][26]

5. Scaricare il file notification-hubs-0.4.jar dalla scheda **File** di [Notification-Hubs-Android-SDK in Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Aggiungere il file alla cartella **libs** del progetto Eclipse e quindi aggiornare la cartella *libs*.

6. Scaricare e decomprimere [Baidu Push Android SDK], aprire la cartella **libs** e copiare il file JAR **pushservice-x.y.z** e le cartelle **armeabi** e **mips** nella cartella **libs** dell'applicazione Android.

7. Aprire il file **AndroidManifest.xml** del progetto Android e aggiungere le autorizzazioni necessarie per Baidu SDK.

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

8. Aggiungere la proprietà **android:name** all'elemento **application** in **AndroidManifest.xml** sostituendo *yourprojectname* (ad esempio, **com.example.BaiduTest**). Assicurarsi che il nome del progetto corrisponda a quello configurato nella console di Baidu.

		<application android:name="yourprojectname.DemoApplication"

9. Aggiungere la configurazione seguente all'interno dell'elemento dell'applicazione dopo l'elemento di attività **.MainActivity**, sostituendo *yourprojectname* (ad esempio, **com.example.BaiduTest**):

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

9. Aggiungere al progetto una nuova classe denominata **ConfigurationSettings.java**.

    ![][28]

    ![][29]

10. Aggiungere alla classe il codice seguente:

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}

	Impostare il valore di **API\_KEY** specificando il valore recuperato in precedenza dal progetto cloud di Baidu, **NotificationHubName** con il nome dell'hub di notifica nel portale di Azure classico e **NotificationHubConnectionString** con DefaultListenSharedAccessSignature dal portale di Azure classico.

11. Aggiungere una nuova classe denominata **DemoApplication.java** e aggiungervi il codice seguente:

		import com.baidu.frontia.FrontiaApplication;

		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Aggiungere un'altra nuova classe denominata **MyPushMessageReceiver.java** e aggiungere alla classe il codice seguente. Questa è la classe che gestisce le notifiche push ricevute dal server push di Baidu.

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
		        String notifyString = "title="" + title + "" description=""
		                + description + "" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }

		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message="" + message + "" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. Aprire **MainActivity.java** e aggiungere quanto segue al metodo **onCreate**:

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Aprire le istruzioni import seguenti nella parte superiore:

			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##Invio di notifiche all'app


È possibile testare rapidamente la ricezione delle notifiche nell'app inviando le notifiche nel [portale di Azure](https://portal.azure.com/) con il pulsante **Invio di prova** dell'hub di notifica, come illustrato nella schermata seguente.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Le notifiche push vengono in genere inviate in un servizio back-end come Servizi mobili o ASP.NET usando una libreria compatibile. È anche possibile utilizzare l'API REST direttamente per inviare messaggi di notifica se una libreria non è disponibile per il back-end.

In questa esercitazione verrà usata un'app semplice e verrà illustrato solo il test dell'app client mediante l'invio di notifiche tramite .NET SDK per gli hub di notifica in un'applicazione console, invece che in un servizio back-end. Come passaggio successivo per l'invio di notifiche da un back-end ASP.NET, è consigliabile vedere l'esercitazione [Uso di Hub di notifica di Azure per inviare notifiche agli utenti](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md). Si possono tuttavia usare gli approcci seguenti per l'invio di notifiche:

* **Interfaccia REST**: è possibile supportare la notifica su qualsiasi piattaforma back-end tramite l'[interfaccia REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure Notification Hubs .NET SDK**: in Gestione pacchetti NuGet per Visual Studio eseguire [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**: vedere [Come usare Hub di notifica da Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

* **Servizi mobili di Azure**: per un esempio di invio di notifiche da un back-end di Servizi mobili di Azure integrato con Hub di notifica, vedere [Aggiungere notifiche push all'app di Servizi mobili](../mobile-services/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md).

* **Java/PHP**: per un esempio di invio di notifiche con le API REST, vedere "Come usare Hub di notifica da Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

##(Facoltativo) Inviare notifiche da un'app console .NET

In questa sezione verrà illustrato come inviare notifiche con un'app console .NET.

1. Creare una nuova applicazione console in Visual C#:

	![][30]

2. Nella finestra Console di Gestione pacchetti impostare **Progetto predefinito** sul nuovo progetto di applicazione console e quindi eseguire il comando seguente nella finestra della console:

        Install-Package Microsoft.Azure.NotificationHubs

	Verrà aggiunto un riferimento ad Azure Notification Hubs SDK usando il <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacchetto NuGet Microsoft.Azure.Notification Hubs</a>.

	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Aprire il file **Program.cs** e aggiungere l'istruzione using seguente:

        using Microsoft.Azure.NotificationHubs;

4. Nella classe `Program` aggiungere il metodo seguente e sostituire *DefaultFullSharedAccessSignatureSASConnectionString* e *NotificationHubName* con i valori disponibili.

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{"title":"((Notification title))","description":"Hello from Azure"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. Aggiungere le righe seguenti nel metodo **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

##Test dell'app

Per testare l'app con un telefono effettivo, collegare il telefono al computer con un cavo USB. L'app verrà caricata nel telefono collegato.

Nella barra degli strumenti superiore di Eclipse fare clic su **Run** e selezionare l'app. L'emulatore verrà avviato e l'app verrà caricata ed eseguita.

L'app recupera le proprietà 'userId' e 'channelId' dal servizio di notifiche push di Baidu ed esegue la registrazione nell'hub di notifica.

Per inviare una notifica di prova, è possibile usare la scheda Debug del portale di Azure classico. Se è stata compilata l'applicazione console .NET per Visual Studio, è sufficiente premere F5 in Visual Studio per eseguirla. L'applicazione invierà una notifica, che verrà visualizzata nell'area di notifica principale del dispositivo o dell'emulatore.


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
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[portale di Azure classico]: https://manage.windowsazure.com/
[portale di Baidu]: http://www.baidu.com/

<!---HONumber=AcomDC_0824_2016-->