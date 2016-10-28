<properties
	pageTitle="Aggiungere notifiche push all'app Xamarin.Forms | Microsoft Azure"
	description="Informazioni su come usare i servizi di Azure per inviare notifiche push multipiattaforma alle app Xamarin.Forms."
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
	ms.date="07/17/2016"
	ms.author="wesmc"/>

# Aggiungere notifiche push all'app Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Panoramica

Questa esercitazione illustra come usare i servizi di Azure per inviare notifiche push a un'app Xamarin.Forms in esecuzione nelle diverse piattaforme native dei dispositivi, ovvero Android, iOS e Windows. Le notifiche push vengono inviate da un back-end delle app per dispositivi mobili di Azure usando l'Hub di notifica di Azure. Le registrazioni dei modelli vengono usate in modo che sia possibile inviare lo stesso messaggio a dispositivi in esecuzione in tutte le piattaforme mediante i diversi servizi di notifica push. Per altre informazioni sull'invio di notifiche push multipiattaforma, vedere la documentazione sull'[Hub di notifica di Azure](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

È possibile aggiungere notifiche push a ogni progetto supportato dall'app Xamarin.Forms. Ogni volta che viene inserito un record nel back-end, viene inviata una notifica push.

##Prerequisiti

Per ottenere risultati ottimali con questa esercitazione, è consigliabile completare prima di tutto l'esercitazione [Creare un'app Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Al termine di questa esercitazione, sarà disponibile un progetto Xamarin.Forms che corrisponde a un'app TodoList multipiattaforma.

Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per inviare notifiche push a un dispositivo iOS, è necessaria l'[appartenenza al programma Apple Developer](https://developer.apple.com/programs/ios/). È inoltre necessario usare un dispositivo iOS fisico, perché il [simulatore iOS non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="create-hub"></a>Creare un hub di notifica

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Aggiornare il progetto server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(Facoltativo) Configurare ed eseguire il progetto Android

Completare questa sezione per abilitare le notifiche push per il progetto Xamarin.Forms Droid per Android.


###Abilitare Google Cloud Messaging (GCM)

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###Configurare il back-end dell'app per dispositivi mobili per inviare richieste push usando GCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###Aggiungere notifiche push al progetto Android

Dopo avere configurato il back-end per l'uso di Messaggistica cloud di Google (GCM), è possibile aggiungere i componenti e il codice al client che consente la registrazione dell'app in GCM, la registrazione per le notifiche push nell'Hub di notifica di Azure tramite il back-end dell'app per dispositivi mobili e la ricezione di notifiche.

1. Nel progetto **Droid** fare clic con il pulsante destro del mouse sulla cartella **Componenti**, scegliere **Altri componenti**, cercare il componente **Client di Messaggistica cloud di Google** e aggiungerlo al progetto. Questo componente supporta le notifiche push per un progetto Xamarin Android.


2. Aprire il nuovo file di progetto MainActivity.cs e aggiungere l'istruzione using seguente all'inizio del file:

		using Gcm.Client;

3. Aggiungere il codice seguente al metodo **OnCreate** dopo la chiamata a **LoadApplication**:

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
	        CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Aggiungere un nuovo metodo helper **CreateAndShowDialog**, come indicato di seguito:

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. Aggiungere il codice seguente alla classe **MainActivity**:

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

	Verrà esposta l'istanza corrente di **MainActivity**, in modo che sia possibile l'esecuzione nel thread principale dell'interfaccia utente.

6. Inizializzare la variabile `instance` all'inizio del metodo **OnCreate**, come indicato di seguito.

		// Set the current instance of MainActivity.
		instance = this;

2. Aggiungere un nuovo file di classe al progetto **Droid** denominato `GcmService.cs`, quindi assicurarsi che le istruzioni **using** seguenti siano presenti nella parte iniziale del file:

		using Android.App;
		using Android.Content;
		using Android.Media;
		using Android.Support.V4.App;
		using Android.Util;
		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Text;


9. Aggiungere le richieste di autorizzazione seguenti alla parte iniziale del file, dopo le istruzioni **using** e prima della dichiarazione **namespace**.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Aggiungere la definizione di classe seguente allo spazio dei nomi.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

	>[AZURE.NOTE]Sostituire **<PROJECT\_NUMBER>** con il numero di progetto annotato in precedenza.

11. Sostituire la classe **GcmService** vuota con il codice seguente, che usa il nuovo ricevitore di trasmissione:

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Aggiungere il codice seguente alla classe **GcmService** che esegue l'override del gestore eventi **OnRegistered** e implementa un metodo **Register**.

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

		Note that this code uses the `messageParam` parameter in the template registration. 

13. Aggiungere il codice seguente che implementa **OnMessage**:

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

	Ciò consente di gestire le notifiche in ingresso e di inviarle alla gestione notifiche per la visualizzazione.

14. **GcmServiceBase** richiede anche l'implementazione dei metodi gestore **OnUnRegistered** e **OnError**, come illustrato di seguito:

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}

È ora possibile testare le notifiche push nell'app in esecuzione su un dispositivo Android o nell'emulatore.

###Testare le notifiche push nell'app Android

I primi due passaggi sono necessari solo per i test in un emulatore.

1. Assicurarsi di distribuire o eseguire il debug su un dispositivo virtuale che ha le API Google API impostate come destinazione, come illustrato di seguito nel gestore del dispositivo virtuale Android (AVD).

2. Aggiungere un account Google nel dispositivo Android facendo clic su **App** > **Impostazioni** > **Aggiungi account**, quindi seguire le istruzioni per usare Aggiungi un account di Google esistente sul dispositivo per crearne uno nuovo.

1. In Visual Studio o Xamarin Studio fare clic con il pulsante destro del mouse sul progetto **Droid**, quindi scegliere **Imposta come progetto di avvio**.

2. Fare clic su **Esegui** per creare il progetto e avviare l'app sul dispositivo Android o sull'emulatore.

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

1. Nel progetto **iOS** aprire il file AppDelegate.cs e aggiungere l'istruzione **using** seguente all'inizio del file di codice.

        using Newtonsoft.Json.Linq;

4. Nella classe **AppDelegate** aggiungere un override per l'evento **RegisteredForRemoteNotifications** per eseguire la registrazione per le notifiche:

        public override void RegisteredForRemoteNotifications(UIApplication application, 
			NSData deviceToken)
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

5. In **AppDelegate** aggiungere anche l'override seguente per il gestore dell'evento **DidReceivedRemoteNotification**:

        public override void DidReceiveRemoteNotification(UIApplication application, 
			NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
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

	Questo metodo gestisce le notifiche in ingresso mentre l'applicazione è in esecuzione.

2. Nella classe **AppDelegate** aggiungere il codice seguente al metodo **FinishedLaunching**:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

	L'aggiunta di questo codice consente il supporto per le notifiche remote e richiede la registrazione push.

L'app è ora aggiornata per il supporto delle notifiche push.

####Testare le notifiche push nell'app iOS

1. Fare clic con il pulsante destro del mouse sul progetto iOS, quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Esegui** o premere **F5** in Visual Studio per compilare il progetto e avviare l'app in un dispositivo iOS, quindi fare clic su **OK** per accettare le notifiche push.

	> [AZURE.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

3. Nell'app digitare un'attività e fare clic sull'icona con il segno più (**+**).

4. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.


##(Facoltativo) Configurare ed eseguire i progetti Windows

Questa sezione illustra l'esecuzione dei progetti Xamarin.Forms WinApp e WinPhone81 per dispositivi Windows. Questa procedura supporta anche progetti per la piattaforma UWP (Universal Windows Platform). Se non si usano dispositivi Windows, è possibile ignorare questa sezione.


####Registrare l'app di Windows per le notifiche push con WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Configurare l'hub di notifica per WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Aggiungere notifiche push all'app di Windows

1. In Visual Studio aprire il file **App.xaml.cs** in un progetto Windows, quindi aggiungere le istruzioni **using** seguenti.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using <your_TodoItemManager_portable_class_namespace>;

	Sostituire `<your_TodoItemManager_portable_class_namespace>` con lo spazio dei nomi del progetto portabile che contiene la classe `TodoItemManager`.
 

2. Nel file App.xaml.cs aggiungere il metodo **InitNotificationsAsync** seguente:

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
				"<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
			{
				{"body", templateBodyWNS},
				{"headers", headers} // Needed for WNS.
			};

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
				.RegisterAsync(channel.Uri, templates);
        }

	Questo metodo ottiene il canale per notifica push e registra un modello per la ricezione di notifiche di modello dall'hub di notifica. A questo client verrà recapitata una notifica di modello che supporta *messageParam*.

3. Nel file App.xaml.cs aggiornare la definizione del metodo **OnLaunched** del gestore eventi aggiungendo il modificatore `async`, quindi aggiungere la riga di codice seguente alla fine del metodo:

        await InitNotificationsAsync();

	In questo modo sarà possibile assicurarsi che la registrazione della notifica push venga creata o aggiornata a ogni avvio dell'app. È importante eseguire questa operazione per assicurare che il canale di notifica push WNS sia sempre attivo.

4. In Esplora soluzioni di Visual Studio aprire il file **Package.appxmanifest** e in **Notifiche** impostare **Popup supportati** su **Sì**.

5. Compilare l'app e verificare che non siano presenti errori. A questo punto l'app client dovrebbe eseguire la registrazione per le notifiche di modello dal back-end dell'app per dispositivi mobili. Ripetere questa sezione per ogni progetto Windows nella soluzione.


####Testare le notifiche push nell'app di Windows

1. In Visual Studio fare clic con il pulsante destro del mouse su un progetto Windows, quindi scegliere **Imposta come progetto di avvio**.

2. Premere il pulsante **Esegui** per compilare il progetto e avviare l'app.

3. Nell'app digitare un nome per un nuovo elemento todoitem, quindi fare clic sull'icona del segno più (**+**) per aggiungerlo.

4. Assicurarsi di ricevere una notifica quando viene aggiunto l'elemento.

##Passaggi successivi

Altre informazioni sulle notifiche push:

* [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) I tag consentono di specificare come destinazione i clienti segmentati con i push. Informazioni su come aggiungere tag all'installazione di un dispositivo.

* [Diagnose push notification issues](../notification-hubs/notification-hubs-push-notification-fixer.md) (Diagnosticare i problemi relativi alle notifiche push) Esistono varie ragioni per cui le notifiche possono essere eliminate o non giungere ai dispositivi. Questo argomento illustra come analizzare e capire la causa radice degli errori relativi alle notifiche push.

È consigliabile proseguire con una delle esercitazioni seguenti:

* [Add authentication to your app ](app-service-mobile-xamarin-forms-get-started-users.md)(Aggiungere l'autenticazione all'app) Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Enable offline sync for your app](app-service-mobile-xamarin-forms-get-started-offline-data.md) (Abilitare la sincronizzazione offline per l'app) Informazioni su come aggiungere il supporto offline all'app usando il back-end di un'app per dispositivi mobili. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0720_2016-->