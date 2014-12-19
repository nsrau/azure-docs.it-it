<properties urlDisplayName="" pageTitle="Introduzione ad Hub di notifica per le app per Xamarin.Android" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="donnam" solutions="" manager="dwrede" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Questo argomento illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin.Android. 
In questa esercitazione verrà creata un'app per Xamarin.Android vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][GitHub].

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Abilitare Google Cloud Messaging]
2. [Configurare l'hub di notifica]
3. [Connettere l'app all'hub di notifica]
4. [Eseguire l'app con l'emulatore]
5. [Inviare notifiche dal back-end]

Questa esercitazione illustra uno scenario di trasmissione semplice tramite hub di notifica. Per completare questa esercitazione, è necessario disporre di:

+ [Xamarin.Android]
+ Un account Google attivo
+ [Componente Servizi mobili di Azure]
+ [Componente Google Cloud Messaging]

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Xamarin.Android. 

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A9C9624B5&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

<h2><a name="register"></a>Abilitare Google Cloud Messaging</h2>

<p></p>

<div class="dev-callout"><b>Nota</b>
<p>Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Passare al sito Web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a>, eseguire l'accesso con le credenziali dell'account Google e quindi fare clic su **Create project...**.

   	![][1]   
	
	<div class="dev-callout"><b>Nota</b>
	<p>Se si dispone già di un progetto esistente, dopo l'accesso verrà visualizzata la pagina <strong>Dashboard</strong>. Per creare un nuovo progetto dalla pagina Dashboard, espandere <strong>API Project</strong>, fare clic su <strong>Create...</strong> in <strong>Other projects</strong>, quindi immettere un nome di progetto e fare clic su <strong>Create project</strong>.</p>
    </div>

2. Fare clic su **Overview** nella colonna di sinistra e prendere nota del numero di progetto nella sezione **Dashboard**. 

	Più avanti nell'esercitazione si imposterà questo valore come variabile PROJECT_ID nel client.

3. Nella pagina <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> fare clic su **Services**, quindi fare clic sull'interruttore per abilitare **Google Cloud Messaging for Android** e accettare le condizioni per l'uso del servizio. 

4. Fare clic su **API Access** e quindi su **Create new Server key...** 

   	![][2]

5. In **Configure Server Key for API Project** fare clic su **Create**.

   	![][3]

6. Prendere nota del valore **API key**.

   	![][4] 

Questo valore della chiave dell'API verrà usato successivamente per abilitare l'hub di notifica con GCM e inviare notifiche push per conto dell'app.

<h2><a name="configure-hub"></a>Configurare l'hub di notifica</h2>

1. Accedere al [portale di gestione di Azure] e quindi fare clic sul pulsante **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, quindi **Hub di notifica** e infine **Creazione rapida**.

   	![][7]

3. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub di notifica**.

   	![][8]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic su **Configura** nella parte superiore.

   	![][9]

5. Fare clic sulla scheda **Hub di notifica** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

   	![][10]

6. Fare clic sulla scheda the **Configura** nella parte superiore, immettere il valore **Chiave API** ottenuto nella sezione precedente, quindi fare clic su **Salva**.

   	![][11]

7. Selezionare la scheda **Dashboard** nella parte superiore e quindi fare clic su **Informazioni di connessione**. Prendere nota delle due stringhe di connessione.

   	![][12]

L'hub di notifica è ora configurato per l'uso con GCM e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche push.

<h2><a name="connecting-app"></a>Connettere l'app all'hub di notifica</h2>

### Creare un nuovo progetto

1. In Xamarin Studio (o Visual Studio) creare un nuovo progetto Android (selezionare File, New, Solution, Android Application).

   	![][13]   
   	![][14]

2. Aprire le proprietà del progetto facendo clic con il pulsante destro del mouse nella visualizzazione Solution e scegliendo **Options**. Selezionare la voce **Android Application** nella sezione **Build**.

   	![][15]

3. Impostare **Minimum Android version** su API Level 8.

4. Impostare **Target Android version** sulla versione dell'API di destinazione (almeno API livello 8 o successivo).

5. Verificare che la prima lettera di **Package name** sia minuscola.

	<div class="dev-callout"><b>Nota</b>
    <p>La prima lettera del nome del pacchetto deve essere minuscola. In caso contrario, al momento della registrazione di **BroadcastReceiver** e **IntentFilter** per le notifiche push, più avanti nell'argomento, verranno visualizzati errori del manifesto dell'applicazione.</p>
    </div> 

### Aggiungere il client Google Cloud Messaging al progetto

Il client Google Cloud Messaging disponibile in Xamarin Component Store semplifica il processo di supporto delle notifiche push in Xamarin.Android.

1. Fare clic con il pulsante destro del mouse sulla cartella Components nell'app per Xamarin.Android e scegliere **Get More Components...**

2. Cercare il componente **Google Cloud Messaging Client**.

3. Aggiungere il componente all'applicazione per Xamarin.Android. Verranno aggiunti automaticamente i riferimenti all'assembly necessario.

### Aggiungere Xamarin.NotificationHub al progetto

Questo assembly offre un modo semplice per effettuare la registrazione con Hub di notifica di Azure. È disponibile per il download tramite le istruzioni seguenti oppure nel [download di esempio][GitHub].

1. Visitare la [pagina Github di Xamarin.NotificationHub], quindi scaricare e creare la cartella di origine.

2. Creare una cartella **_external** nella cartella del progetto Xamarin.Android, quindi copiarvi il file **ByteSmith.WindowsAzure.Messaging.Android.dll** compilato.

3. Aprire il progetto Xamarin.Android in Xamarin Studio (o Visual Studio).

4. Fare clic con il pulsante destro del mouse sulla cartella **References** del progetto e scegliere **Edit References...**

5. Passare alla scheda **.Net Assembly**, individuare la cartella **_external** del progetto, selezionare il file **ByteSmith.WindowsAzure.Messaging.Android.dll** compilato in precedenza e fare clic su **Add**. Fare clic su OK per chiudere la finestra di dialogo. 

### Configurare Hub di notifica nel progetto

1. Creare una classe **Constants.cs** e definire i valori costanti seguenti, sostituendo i segnaposto con valori:

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. Aggiungere le istruzioni using seguenti a **MainActivity.cs**:

		using ByteSmith.WindowsAzure.Messaging;
		using Gcm.Client;

3. Creare il metodo seguente nella classe **MainActivity**:

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

	<div class="dev-callout"><b>Nota</b>
    <p>Di seguito viene illustrata in dettaglio la creazione da zero di una classe **BroadcastReceiver**. Esiste tuttavia un'alternativa più rapida alla creazione manuale di **MyBroadcastReceiver.cs**, che consiste nel fare riferimento al file **GcmService.cs** disponibile nel progetto Xamarin.Android di esempio su GitHub. Anche duplicare il file **GcmService.cs** e modificare i nomi delle classi rappresenta infatti un punto di partenza ideale.</p>
    </div> 

5. Aggiungere le istruzioni using seguenti a **MyBroadcastReceiver.cs**, facendo riferimento al componente e all'assembly aggiunti in precedenza:

		using ByteSmith.WindowsAzure.Messaging;
		using Gcm.Client;

5. Aggiungere le richieste di autorizzazione seguenti tra le istruzioni **using** e la dichiarazione **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. In **MyBroadcastReceiver.cs** change the **MyBroadcastReceiver** class to match the following:

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }
        
7. Add another class in **MyBroadcastReceiver.cs** named **PushHandlerService** which derives from **PushHandlerServiceBase**. Make sure to use the **Service** directive on the class:

    	[Service] //Must use the service tag
    	public class GcmService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public GcmService() : base(Constants.SenderID) 
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "GcmService() constructor"); 
        	}
    	}


8. **GcmServiceBase** implementa i metodi **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** e **OnError()**. La classe di implementazione **GcmService** deve eseguire l'override di questi metodi, che si attiveranno in risposta all'interazione con l'hub di notifica.

9. Eseguire l'override del metodo **OnRegistered()** in **PushHandlerService** con il codice seguente:

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub(Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                await Hub.UnregisterAllAsync(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = await Hub.RegisterNativeAsync(registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message); 
                Debugger.Break();
            }
        }

	<div class="dev-callout"><b>Nota</b>
    <p>Nel codice **OnRegistered()** precedente, si noti la capacità di specificare tag per la registrazione a specifici canali di messaggistica.</p>
    </div> 
    
10. Eseguire l'override del metodo **OnMessage** in **PushHandlerService** con il codice seguente:

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

11. Aggiungere il metodo **createNotification** seguente a **PushHandlerService** per l'invio di notifiche agli utenti come usato in precedenza:

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

1. In **Tools** fare clic su **Open Android Emulator Manager**, selezionare il dispositivo e quindi fare clic su **Edit**.

   	![][18]

2. Selezionare **Google APIs** in **Target**, quindi fare clic su **OK**.

   	![][19]

3. Fare clic su **Run** nella barra degli strumenti superiore, quindi selezionare l'app. In questo modo l'emulatore viene avviato e l'app eseguita.

4. L'app recupera la proprietà *registrationId* da GCM ed esegue la registrazione con l'hub di notifica.

	<div class="dev-callout"><b>Nota</b>
    <p>Per ricevere notifiche push è necessario configurare un account Google nell'emulatore Android Virtual Device scegliendo **Settings** e quindi **Add Account**. Assicurarsi inoltre che l'emulatore sia connesso a Internet.</p>
    </div> 

<h2><a name="send"></a>Inviare notifiche dal back-end</h2>

È possibile inviare notifiche usando gli hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione verranno inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1. Creare una nuova applicazione console in Visual C#: 

   	![][20]

2. Aggiungere un riferimento ad Azure Service Bus SDK con il <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacchetto NuGet WindowsAzure.ServiceBus</a>. Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

        Install-Package WindowsAzure.ServiceBus

    e premere INVIO.

2. Aprire il file Program.cs e aggiungere l'istruzione using seguente:

        using Microsoft.ServiceBus.Notifications;

3. Nella classe `Program` aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

4. Aggiungere quindi le righe seguenti nel metodo Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Premere F5 per eseguire l'app. Si riceverà una notifica di tipo avviso popup.

   	![][21]

Per inviare una notifica tramite un servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili], quindi eseguire le operazioni seguenti:

1. Accedere al [portale di gestione di Azure] e selezionare il servizio mobile.

2. Selezionare la scheda **Utilità di pianificazione** nella parte superiore.

   	![][22]

3. Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **Su richiesta**.

   	![][23]

4. Una volta creato il processo, fare clic sul relativo nome. Fare quindi clic sulla scheda **Script** nella barra superiore.

5. Inserire lo script seguente all'interno della funzione dell'utilità di pianificazione. Assicurarsi di sostituire i segnaposto con il nome dell'hub di notifica e la stringa di connessione associata a *DefaultFullSharedAccessSignature* ottenuti nel passaggio precedente. Fare clic su **Salva**.

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

6. Fare clic su **Esegui una volta** sulla barra inferiore. Si riceverà una notifica di tipo avviso popup.

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Android. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti], mentre se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Usare Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'utilizzo di Hub di notifica, vedere la pagina relativa alle [linee guida per gli hub di notifica] e quella relativa alle [procedure di Hub di notifica per Android].

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
[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/get-started-with-push-js

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[oggetto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Linee guida su Hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
[Procedure di Hub di notifica per Android]: http://msdn.microsoft.com/it-it/library/dn282661.aspx

[Usare Hub di notifica per inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users-aspnet
[Usare Hub di notifica per inviare le ultime notizie]: /it-it/manage/services/notification-hubs/breaking-news-dotnet
[Pagina del componente GCMClient]: http://components.xamarin.com/view/GCMClient
[Pagina Github di Xamarin.NotificationHub]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Componente Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
