<properties
	pageTitle="Aggiungere notifiche push all'app Xamarin.Forms con il servizio app di Azure"
	description="Informazioni su come usare il servizio app di Azure per inviare notifiche push all'app Xamarin.Forms"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="wesmc"/>

# Aggiungere notifiche push all'app Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Panoramica

Questa esercitazione è basata sull'[esercitazione della guida introduttiva per Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md), che deve essere completata per prima. Verrà aggiunto il supporto di notifiche push per ogni progetto da supportare nel progetto di avvio rapido Xamarin.Forms. Ogni volta che viene inserito un record, viene inviata una notifica push.

Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Il [simulatore di iOS non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), quindi è necessario utilizzare un dispositivo iOS fisico. È necessario iscriversi al programma [Apple Developer Program](https://developer.apple.com/programs/ios/).

##Prerequisiti

* Completare l'esercitazione [Creare un'app Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md) che elenca altri prerequisiti. Questo articolo usa l'app completata di quell'esercitazione.

* Un dispositivo iOS fisico. Le notifiche push non sono supportate dal simulatore iOS.

##Creare un hub di notifica per l'app per dispositivi mobili

Per configurare l'app per l'invio di notifiche, creare un nuovo hub di notifica e configurarlo per i servizi di notifica della piattaforma che si intende usare.

Questa procedura illustra la creazione di un nuovo hub di notifica. Se è già stato creato, è sufficiente selezionarlo.

1. Accedere al [portale di Azure](https://portal.azure.com/). Fare clic su **Sfoglia** > **App per dispositivi mobili** > back-end > **Impostazioni** > **Dispositivi mobili** > **Push** > **Hub di notifica** > **+ Hub di notifica**, specificare un nome e uno spazio dei nomi per un nuovo hub di notifica e quindi fare clic sul pulsante **OK**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. Nel pannello per la creazione dell'hub di notifica, fare clic su **Crea**.


##Aggiornare il progetto server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(Facoltativo) Configurare ed eseguire il progetto Android

Questa sezione illustra l'esecuzione del progetto Xamarin droid per dispositivi Android. Se non si usano dispositivi Android, è possibile ignorare questa sezione.


####Abilitare Google Cloud Messaging (GCM)


[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


####Configurare l'hub di notifica per GCM

1. Accedere al [Portale di Azure](https://portal.azure.com/). Fare clic su **Sfoglia** > **App per dispositivi mobili** > App per dispositivi mobili > **Impostazioni** > **Push** > **Google (GCM)**. Incollare la chiave API server creata in precedenza e fare clic su **Salva**. Il servizio è ora configurato per l'uso delle notifiche push per Android.

	![](./media/app-service-mobile-xamarin-forms-get-started-push/mobile-app-save-gcm-api-key.png)


####Aggiungere notifiche push al progetto droid

1. Fare clic con il pulsante destro del mouse sulla cartella Componenti, fare clic su Altri componenti, cercare il componente **Client Google Cloud Messaging** e aggiungerlo al progetto. Questo componente consente di semplificare l'uso delle notifiche push con un progetto Xamarin Android.

2. Aprire il nuovo file di progetto MainActivity.cs e aggiungere l'istruzione using seguente all'inizio del file:

		using Gcm.Client;

3. Aggiungere il codice seguente al metodo `OnCreate` dopo la chiamata a `LoadApplication`.

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);

	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the Mobile Service. Verify the URL", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Aggiungere il codice seguente per il metodo di supporto `CreateAndShowDialog`.

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. Nella classe `MainActivity`, aggiungere il codice seguente per esporre l'attuale `MainActivity` ed eseguire parte dell'interfaccia utente nel thread dell'interfaccia utente principale:

		// Create a new instance field for this activity.
		static MainActivity instance = null;

		// Return the current activity instance.
		public static MainActivity CurrentActivity
		{
		    get
		    {
		        return instance;
		    }
		}

6. Inizializzare la variabile `instance` all'inizio del metodo `MainActivity.OnCreate`.

		// Set the current instance of MainActivity.
		instance = this;

7. Aggiungere un nuovo file della classe al progetto **Droid**. Denominare il nuovo file della classe come **GcmService**.

8. Assicurarsi che le istruzioni `using` seguenti siano incluse all'inizio del file.

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Android.App;
		using Android.Content;
		using System.Collections.Generic;
		using System.Diagnostics;
		using Android.Util;
		using Newtonsoft.Json.Linq;
		using System.Text;
		using System.Linq;
		using Android.Support.V4.App;
		using Android.Media;


9. Aggiungere le richieste di autorizzazione seguenti all'inizio del file, dopo le istruzioni `using` e prima della dichiarazione `namespace`.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

10. Aggiungere la definizione di classe seguente allo spazio dei nomi. Sostituire **<PROJECT_NUMBER>** con il numero di progetto annotato in precedenza.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

11. Aggiornare la classe `GcmService` per usare il nuovo ricevitore di trasmissioni.

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Aggiungere il codice seguente alla classe GcmService che sostituisce il gestore eventi OnRegistered e implementa un metodo `Register`.

	Questo codice registra un corpo del modello per ricevere notifiche di modello usando il parametro `messageParam`. Le notifiche di modello consentono di inviare notifiche multipiattaforma. Per altre informazioni, vedere [Modelli](https://msdn.microsoft.com/library/azure/dn530748.aspx).

		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                	{"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

13. È necessario implementare `OnMessage` per gestire una notifica push in arrivo. In questo codice la notifica viene gestita e inviata al gestore delle notifiche.

		protected override void OnMessage(Context context, Intent intent)
		{
		    Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

		    var msg = new StringBuilder();

		    if (intent != null && intent.Extras != null)
		    {
		        foreach (var key in intent.Extras.KeySet())
		            msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
		    }

		    //Store the message
		    var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
		    var edit = prefs.Edit();
		    edit.PutString("last_msg", msg.ToString());
		    edit.Commit();

		    string message = intent.Extras.GetString("message");
		    if (!string.IsNullOrEmpty(message))
		    {
		        createNotification("New todo item!", "Todo item: " + message);
		        return;
		    }

		    string msg2 = intent.Extras.GetString("msg");
		    if (!string.IsNullOrEmpty(msg2))
		    {
		        createNotification("New hub message!", msg2);
		        return;
		    }

		    createNotification("Unknown message details", msg.ToString());
		}

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

14. È necessario implementare anche i gestori `OnUnRegistered` e `OnError` per il ricevitore.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}



####Testare le notifiche push nell'app Android

1. In Visual Studio o Xamarin Studio, fare clic con il pulsante destro del mouse sul progetto **droid** e scegliere **Imposta come progetto di avvio**.

2. Fare clic su **Esegui** per creare il progetto e avviare l'app sul dispositivo Android.

3. Nell'app digitare un'attività e fare clic sull'icona con il segno più (**+**).

4. Assicurarsi di ricevere una notifica quando viene aggiunto un elemento.





##(Facoltativo) Configurare ed eseguire il progetto iOS

Questa sezione illustra l'esecuzione del progetto Xamarin iOS per dispositivi iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

[AZURE.INCLUDE [notification-hubs-xamarin-enable-apple-push-notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####Configurare l'hub di notifica per APNS

1. Accedere al [portale di Azure](https://portal.azure.com/). Fare clic su **Sfoglia** > **App per dispositivi mobili** > App per dispositivi mobili > **Impostazioni** > **Push** > **Apple (APNS)** > **Carica certificato**. Caricare il file del certificato push (con estensione p12) esportato in precedenza. Assicurarsi di selezionare **Sandbox** se è stato creato un certificato push di sviluppo per le fasi di sviluppo e test. In caso contrario, scegliere **Produzione**. Il servizio è ora configurato per l'uso delle notifiche push per iOS.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	Configurare successivamente l'impostazione di progetto iOS in Xamarin Studio o Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####Aggiungere notifiche push all'app iOS

1. Aggiungere l'istruzione `using` seguente all'inizio del file **AppDelegate.cs**.

        using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;


2. Nel progetto iOS aprire AppDelegate.cs e aggiornare `FinishedLaunching` per supportare le notifiche remote come indicato di seguito.

		public override bool FinishedLaunching (UIApplication app, NSDictionary options)
		{
			global::Xamarin.Forms.Forms.Init ();

			Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();

            // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
            // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
            //SQLitePCL.CurrentPlatform.Init();

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

			LoadApplication (new App ());

			return base.FinishedLaunching (app, options);
		}


4. In AppDelegate.cs, aggiungere anche un override per l'evento **RegisteredForRemoteNotifications** per eseguire la registrazione per le notifiche:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. In AppDelegate.cs, aggiungere anche un override per l'evento **DidReceivedRemoteNotification** per gestire le notifiche in ingresso mentre l'app è in esecuzione:

        public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

L'app è ora aggiornata per il supporto delle notifiche push.

####Testare le notifiche push nell'app iOS

1. Fare clic con il pulsante destro del mouse sul progetto iOS, quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Esegui** o premere **F5** in Visual Studio per compilare il progetto e avviare l'app in un dispositivo iOS, quindi fare clic su **OK** per accettare le notifiche push.

	> [AZURE.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

3. Nell'app digitare un'attività e fare clic sull'icona con il segno più (**+**).

4. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.




##(Facoltativo) Configurare ed eseguire il progetto Windows

Questa sezione illustra l'esecuzione del progetto Xamarin WinApp per dispositivi Windows. Se non si usano dispositivi Windows, è possibile ignorare questa sezione.


####Registrare l'app di Windows per le notifiche push con WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Configurare l'hub di notifica per WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Aggiungere notifiche push all'app di Windows

1. In Visual Studio, aprire **App.xaml.cs** nel progetto **WinApp**. Aggiungere le istruzioni `using` seguenti:

		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

	Aggiungere anche un'istruzione `using` per lo spazio dei nomi nel progetto portabile che contiene la classe `TodoItemManager`.

		using <Your namespace for the TodoItemManager class>;
 

2. In App.xaml.cs aggiungere il metodo `InitNotificationsAsync` seguente. Questo metodo ottiene il canale di notifica push e registra un modello per la ricezione di notifiche di modello dall'hub di notifica. A questo client verrà recapitata una notifica di modello che supporta `messageParam`.

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = "<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
			{
				{"body", templateBodyWNS},
				{"headers", headers} // Only needed for WNS & MPNS
			};

            await TodoItemManager.DefaultManager.CurrentClient.GetPush().RegisterAsync(channel.Uri, templates);
        }

3. In App.xaml.cs aggiornare il gestore dell'evento `OnLaunched` con l'attributo `async` e aggiungere una chiamata a `InitNotificationsAsync` alla fine del metodo.

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            Frame rootFrame = Window.Current.Content as Frame;

            // Do not repeat app initialization when the Window already has content,
            // just ensure that the window is active
            if (rootFrame == null)
            {
                // Create a Frame to act as the navigation context and navigate to the first page
                rootFrame = new Frame();
                // Set the default language
                rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
                rootFrame.NavigationFailed += OnNavigationFailed;
                Xamarin.Forms.Forms.Init(e);
                if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
                {
                    //TODO: Load state from previously suspended application
                }
                // Place the frame in the current Window
                Window.Current.Content = rootFrame;
            }

            if (rootFrame.Content == null)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(typeof(MainPage), e.Arguments);
            }
            // Ensure the current window is active
            Window.Current.Activate();

            await InitNotificationsAsync();
        }

4. In Esplora soluzioni di Visual Studio aprire il file **Package.appxmanifest** e in **Notifiche** impostare **Popup supportati** su **Sì**.

5. Compilare l'app e verificare che non siano presenti errori. A questo punto l'app client dovrebbe eseguire la registrazione per le notifiche di modello dal back-end dell'app per dispositivi mobili.


####Testare le notifiche push nell'app di Windows

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **WinApp** e quindi scegliere **Imposta come progetto di avvio**.


2. Premere il pulsante **Esegui** per compilare il progetto e avviare l'app.

3. Nell'app digitare un nome per un nuovo oggetto todoitem e quindi fare clic sul segno più (**+**) per aggiungerlo.

4. Assicurarsi di ricevere una notifica quando viene aggiunto l'elemento.



<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0511_2016-->