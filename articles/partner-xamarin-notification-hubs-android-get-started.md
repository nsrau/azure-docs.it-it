<properties
	pageTitle="Introduzione ad Hub di notifica per le app per Xamarin.Android"
	description="Informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin Android."
	authors="yuaxu"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter="xamarin"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/11/2014"
	ms.author="donnam"/>

# Introduzione ad Hub di notifica

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Informazioni generali

In questo argomento viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin.Android.
In questa esercitazione verrà creata un'app per Xamarin.Android vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][GitHub].

Questa esercitazione illustra lo scenario di trasmissione semplice tramite hub di notifica. 

##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

+ [Xamarin.Android]
+ Un account Google attivo
+ [Componente Servizi mobili di Azure]
+ [Componente di messaggistica di Azure]
+ [Componente client di Google Cloud Messaging]

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Xamarin.Android.

> [AZURE.IMPORTANT] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="register"></a>Abilitare Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a name="configure-hub"></a>Configurare l'hub di notifica

[AZURE.INCLUDE [notification-hubs-android-configure-push](../includes/notification-hubs-android-configure-push.md)]

##<a name="connecting-app"></a>Connettere l'app all'hub di notifica

### Creare un nuovo progetto

1. In Xamarin Studio (o Visual Studio) fare clic su **File** e **Nuovo**, quindi selezionare **Applicazione Android** nella finestra di dialogo **Nuova soluzione** e infine fare clic su **OK**.

   	![][14]

	In questo modo viene creato un nuovo progetto Android.

2. Aprire le proprietà del progetto facendo clic con il pulsante destro del mouse nella visualizzazione Solution e scegliendo **Options**. Selezionare la voce **Android Application** nella sezione **Build**.

   	![][15]

3. Impostare **Minimum Android version** su API Level 8.

4. Impostare **Target Android version** sulla versione dell'API di destinazione (almeno API livello 8 o successivo).

5. Verificare che la prima lettera di **Package name** sia minuscola.

	> [AZURE.IMPORTANT] La prima lettera del nome del pacchetto deve essere minuscola. In caso contrario verranno visualizzati errori del manifesto dell'applicazione quando si registrano **BroadcastReceiver** e **IntentFilter** per le notifiche push in seguito.

### Aggiungere i componenti necessari al progetto

Il client Google Cloud Messaging disponibile in Xamarin Component Store semplifica il processo di supporto delle notifiche push in Xamarin.Android.

1. Fare clic con il pulsante destro del mouse sulla cartella Components nell'app per Xamarin.Android e scegliere **Get More Components**.

2. Cercare il componente **Servizi mobili di Azure** e aggiungerlo al progetto.

3. Cercare il componente **Messaggistica di Azure** e aggiungerlo al progetto.

4. Cercare il componente **Client Google Cloud Messaging** e aggiungerlo al progetto.


### Configurare Hub di notifica nel progetto

1. Creare un classe **Constants.cs** e definire i seguenti valori costanti, sostituendo i segnaposto con valori:

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. Aggiungere le seguenti istruzioni using a **MainActivity.cs**:

		using Microsoft.WindowsAzure.MobileServices;
		using Gcm.Client;

3. Creare il seguente metodo nella classe **MainActivity**:

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. Creare una nuova classe **MyBroadcastReceiver**.

	> [AZURE.NOTE] Di seguito viene illustrata in dettaglio la creazione da zero di un **BroadcastReceiver**. Esiste tuttavia un'alternativa più rapida alla creazione manuale di **MyBroadcastReceiver.cs**, che consiste nel fare riferimento al file **GcmService.cs** disponibile nel progetto Xamarin.Android di esempio su GitHub. Anche duplicare il file **GcmService.cs** e modificare i nomi delle classi rappresenta un punto di partenza ideale.

5. Aggiungere le seguenti istruzioni using a **MyBroadcastReceiver.cs**, facendo riferimento al componente e all'assembly aggiunti in precedenza:

		using WindowsAzure.Messaging;
		using Gcm.Client;

5. Aggiungere le seguenti richieste di autorizzazione tra le istruzioni **using** e la dichiarazione **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. In **MyBroadcastReceiver.cs** modificare la classe **MyBroadcastReceiver** in base al seguente esempio:

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Aggiungere un'altra classe in **MyBroadcastReceiver.cs** denominata **PushHandlerService** che deriva da **GcmServiceBase**. Assicurarsi di applicare l'attributo **Service** alla classe:

    	[Service] // Must use the service tag
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID)
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
        	}
    	}


8. **GcmServiceBase** implementa i metodi **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** e **OnError()**. La classe di implementazione **PushHandlerService** deve eseguire l'override di questi metodi, che si attiveranno in risposta all'interazione con l'hub di notifica.

9. Eseguire l'override del metodo **OnRegistered()** in **PushHandlerService** con il seguente codice:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub (Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = Hub.RegisterNative (registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	> [AZURE.NOTE] Nel codice **OnRegistered()** precedente, si noti la capacità di specificare tag per la registrazione a specifici canali di messaggistica.

10. Eseguire l'override del metodo **OnMessage** in **PushHandlerService** con il seguente codice:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(messageText))
            {
                createNotification("New hub message!", messageText);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

11. Aggiungere il seguente metodo **createNotification** a **PushHandlerService** per l'invio di notifiche agli utenti come utilizzato in precedenza:

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. Eseguire l'override dei membri astratti **OnUnRegistered()**, **OnRecoverableError()** e **OnError()** per consentire la compilazione del codice.


<h2><a name="run-app"></a>Eseguire l'app nell'emulatore</h2>

Quando si esegue l'app nell'emulatore, assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

	> [AZURE.IMPORTANT] Per ricevere notifiche push, è necessario configurare un account Google nell'emulatore Android Virtual Device scegliendo **Settings** e quindi **Add Account**. Assicurarsi inoltre che l'emulatore sia connesso a Internet.

1. In **Tools** fare clic su **Open Android Emulator Manager**, selezionare il dispositivo e fare clic su **Edit**.

   	![][18]

2. Selezionare **Google APIs** in **Target**, quindi fare clic su **OK**.

   	![][19]

3. Fare clic su **Run** sulla barra degli strumenti nella parte superiore della schermata e selezionare l'app. In questo modo l'emulatore viene avviato e l'app eseguita.

  L'app recupera la proprietà  *registrationId* da GCM ed esegue la registrazione con l'hub di notifica.

<h2><a name="send"></a>Inviare notifiche dal back-end</h2>

È possibile inviare notifiche usando gli hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione verranno inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1. Creare una nuova applicazione console in Visual C#:

   	![][20]

2. Aggiungere un riferimento a Service Bus SDK di Azure con il pacchetto <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet</a>. Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi su **Gestione pacchetti libreria** e infine su **Console di gestione pacchetti**. Quindi nella finestra della console digitare:

        Install-Package WindowsAzure.ServiceBus

    e premere INVIO.

2. Aprire il file Program.cs e aggiungere la seguente istruzione using:

        using Microsoft.ServiceBus.Notifications;

3. Nella classe  `Program` aggiungere il seguente metodo:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

4. Aggiungere quindi le seguenti righe nel metodo Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Premere F5 per eseguire l'app. Si dovrebbe ricevere una notifica di tipo avviso popup.

   	![][21]

Per inviare una notifica tramite un servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili], quindi seguire questa procedura:

1. Accedere al [portale di gestione di Azure] e selezionare il servizio mobile.

2. Selezionare la scheda **Scheduler** nella parte superiore.

   	![][22]

3. Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **On demand**.

   	![][23]

4. Una volta creato il processo, fare clic sul relativo nome. Fare quindi clic sulla scheda **Script** nella barra superiore.

5. Inserire il seguente script all'interno della funzione dell'utilità di pianificazione. Assicurarsi di sostituire i segnaposto con il nome dell'hub di notifica e la stringa di connessione associata a  *DefaultFullSharedAccessSignature* ottenuta in precedenza. Fare clic su **Salva**.

        var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
    	  function (error)
    	  {
        	if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
	    );

6. Fare clic su **Esegui una volta** sulla barra inferiore. Si dovrebbe ricevere una notifica di tipo avviso popup.

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Android. Per inviare notifiche a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti], mentre se si desidera suddividere gli utenti per gruppi di interesse, consultare [Usare Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere la [pagina relativa alle linee guida per gli hub di notifica] e quella relativa alle [procedure di Hub di notifica per Android].

<!-- Anchors. -->
[Abilitare Google Cloud Messaging]: #register
[Configurare l'hub di notifica]: #configure-hub
[Connettere l'app all'hub di notifica]: #connecting-app
[Eseguire l'app con l'emulatore]: #run-app
[Inviare notifiche dal back-end]: #send
[Passaggi successivi]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript e HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[oggetto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Informazioni aggiuntive su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedure di Hub di notifica per Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Uso di Hub di notifica per inviare notifiche push agli utenti]: /manage/services/notification-hubs/notify-users-aspnet
[Uso di Hub di notifica per inviare le ultime notizie]: /manage/services/notification-hubs/breaking-news-dotnet
[Pagina del componente GCMClient]: http://components.xamarin.com/view/GCMClient
[Pagina GitHub di Xamarin.NotificationHub]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Componente client di Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Componente di messaggistica di Azure]: http://components.xamarin.com/view/azure-messaging

<!--HONumber=49-->