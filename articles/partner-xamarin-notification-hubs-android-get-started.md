<properties linkid="" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="elioda" solutions="" manager="" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

Introduzione ad Hub di notifica
===============================

[Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Hub di notifica di Azure per inviare notifiche push a un'applicazione di Xamarin.Android. In questa esercitazione verrà creata un'app per Xamarin.Android vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica. Il codice compilato è disponibile nell'esempio di [app NotificationHubs](http://go.microsoft.com/fwlink/p/?LinkId=331329).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Abilitazione di Google Cloud Messaging](#register)
2.  [Configurazione dell'hub di notifica](#configure-hub)
3.  [Connessione dell'app all'hub di notifica](#connecting-app)
4.  [Esecuzione dell'app con l'emulatore](#run-app)
5.  [Invio di notifiche dal back-end](#send)

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica. Per completare questa esercitazione, è necessario disporre di:

-   Xamarin.Android
-   Un account Google attivo

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Android.

**Nota**

Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F).

Abilitazione di Google Cloud MessagingAbilitazione di Google Cloud Messaging
----------------------------------------------------------------------------

**Nota**

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Passare al sito Web [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), eseguire l'accesso con le credenziali dell'account Google e quindi fare clic su **Create project...**.

    ![][1]   

    **Nota**

    Se si dispone già di un progetto esistente, dopo l'accesso verrà visualizzata la pagina **Dashboard**. Per creare un nuovo progetto dalla pagina Dashboard, espandere **API Project**, fare clic su **Create...** in **Other projects**, quindi immettere un nome di progetto e fare clic su **Create project**.

2.  Fare clic su **Overview** nella colonna di sinistra e prendere nota del numero di progetto nella sezione **Dashboard**.

    Più avanti nell'esercitazione si imposterà questo valore come variabile PROJECT\_ID nel client.

3.  Nella pagina [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) fare clic su **Services**, quindi fare clic sull'interruttore per abilitare **Google Cloud Messaging for Android** e accettare le condizioni per l'utilizzo del servizio.

4.  Fare clic su **API Access** e quindi su **Create new Server key**.

    ![][2]

5.  In **Configure Server Key for API Project** fare clic su **Create**.

    ![][3]

6.  Prendere nota del valore **API key**.

    ![][4] 

Questo valore della chiave dell'API verrà utilizzato successivamente per abilitare l'hub di notifica con GCM e inviare notifiche push per conto dell'app.

Configurazione dell'hub di notificaConfigurazione dell'hub di notifica
----------------------------------------------------------------------

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e fare clic su **+NEW** nella parte inferiore della schermata.

2.  Fare clic su **App Services**, selezionare **Service Bus**, **Notification Hub**, quindi **Quick Create**.

    ![][7]

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

    ![][8]

4.  Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic su **Configure** nella parte superiore.

    ![][9]

5.  Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

    ![][10]

6.  Fare clic sulla scheda **Configure** nella parte superiore, immettere il valore **API Key** ottenuto nella sezione precedente, quindi fare clic su **Save**.

    ![][11]

7.  Selezionare la scheda **Dashboard** nella parte superiore e quindi fare clic su **Connection Information**. Prendere nota delle due stringhe di connessione.

    ![][12]

L'hub di notifica è ora configurato per l'uso con GCM e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche push.

Connessione dell'appConnessione dell'app all'hub di notifica
------------------------------------------------------------

### Creare un nuovo progetto

1.  In Xamarin Studio (o Visual Studio) creare un nuovo progetto Android (selezionare File, New, Solution, Android Application).

    ![][13]   
    ![][14]

2.  Aprire le proprietà del progetto facendo clic con il pulsante destro del mouse nella visualizzazione Solution e scegliendo **Options**. Selezionare la voce **Android Application** nella sezione **Build**.

    ![][15]

3.  Impostare **Minimum Android version** su API Level 8.

4.  Impostare **Target Android version** sulla versione dell'API di destinazione (almeno API livello 8 o successivo).

5.  Verificare che la prima lettera di **Package name** sia minuscola.

    **Nota**

    La prima lettera del nome del pacchetto deve essere minuscola. In caso contrario, al momento della registrazione di **BroadcastReceiver** e **IntentFilter** per le notifiche push, più avanti nell'argomento, verranno visualizzati errori del manifesto dell'applicazione.

### Aggiungere PushSharp al progetto

1.  A questo punto si aggiungerà **PushSharp** al progetto come riferimento. A questo scopo è necessario compilare l'ultima versione di PushSharp e aggiungere la DLL compilata come riferimento al progetto Xamarin.Android.

2.  Visitare la [pagina Github di PushSharp](https://github.com/Redth/PushSharp) e scaricare l'ultima versione. Dopo aver estratto la raccolta di file, passare alla cartella del progetto di esempio seguente:

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. e aprire il file di progetto seguente in Xamarin Studio (o Visual Studio):

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  Compilare l'esempio di client PushSharp MonoForAndroid in modalità **Release**.

4.  Creare una cartella **\_external** nella cartella del progetto Xamarin.Android.

5.  Copiare il file seguente dall'esempio di client PushSharp MonoForAndroid alla nuova cartella **\_external** nella cartella del progetto Xamarin.Android:

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  Aprire il progetto Xamarin.Android in Xamarin Studio (o Visual Studio).

7.  Fare clic con il pulsante destro del mouse sulla cartella **References** del progetto e scegliere **Edit References**.

8.  Passare alla scheda **.Net Assembly**, individuare la cartella **\_external** del progetto, selezionare il file **PushSharp.Client.MonoForAndroid.dll** compilato in precedenza e fare clic su **Add**. Fare clic su OK per chiudere la finestra di dialogo.

### Aggiungere Xamarin.NotificationHub al progetto

1.  A questo punto è necessario aggiungere le librerie per il supporto della classe NotificationHub. Visitare la [pagina Github di Xamarin.NotificationHub](https://github.com/SaschaDittmann/Xamarin.NotificationHub), quindi scaricare e creare la cartella di origine.

2.  Copiare il file **ByteSmith.WindowsAzure.Messaging.Android.dll** compilato nella cartella **\_external** all'interno della cartella del progetto Xamarin.Android.

3.  Aprire il progetto Xamarin.Android in Xamarin Studio (o Visual Studio).

4.  Fare clic con il pulsante destro del mouse sulla cartella **References** del progetto e scegliere **Edit References**.

5.  Passare alla scheda **.Net Assembly**, individuare la cartella **\_external** del progetto, selezionare il file **ByteSmith.WindowsAzure.Messaging.Android.dll** compilato in precedenza e fare clic su **Add**. Fare clic su OK per chiudere la finestra di dialogo.

### Configurare Hub di notifica nel progetto

1.  Creare un classe **Constants.cs** e definire i valori costanti seguenti, sostituendo i segnaposto con valori:

         public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

         // Azure app specific connection string and hub path
         public const string ConnectionString = "<Azure connection string>"";
         public const string NotificationHubPath = "<hub path>";

2.  Aggiungere le istruzioni using seguenti a **MainActivity.cs**:

         using ByteSmith.WindowsAzure.Messaging;
         using PushSharp.Client;

3.  Creare il metodo seguente nella classe **MainActivity**:

         private void RegisterWithGCM()
         {
             // Check to ensure everything's setup right
             PushClient.CheckDevice(this);
             PushClient.CheckManifest(this);

             // Register for push notifications
             System.Diagnostics.Debug.WriteLine("Registering...");
             PushClient.Register(this, Constants.SenderID);
         }

4.  Creare una nuova classe **MyBroadcastReceiver**.

    **Nota**

    Di seguito viene illustrata in dettaglio la creazione da zero di un **BroadcastReceiver**. Esiste tuttavia un'alternativa più rapida alla creazione manuale di **MyBroadcastReceiver.cs**, che consiste nel fare riferimento al file **PushService.cs** disponibile nel progetto Xamarin.Android di esempio PushSharp su GitHub. Anche duplicare il file **PushService.cs** e modificare i nomi delle classi rappresenta infatti un punto di partenza ideale.

5.  Aggiungere le istruzioni using seguenti a **MyBroadcastReceiver.cs**:

         using ByteSmith.WindowsAzure.Messaging;
         using PushSharp.Client;

6.  Aggiungere le richieste di autorizzazione seguenti tra le istruzioni **using** e la dichiarazione **namespace**:

         [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
         [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
         [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

         //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
         [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
         [assembly: UsesPermission(Name = "android.permission.INTERNET")]
         [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  In **MyBroadcastReceiver.cs** modificare la classe **MyBroadcastReceiver** in base all'esempio seguente:

      	 [BroadcastReceiver(Permission=GCMConstants.PERMISSION\_GCM\_INTENTS)]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
         public class MyBroadcastReceiver : PushHandlerBroadcastReceiverBase<PushHandlerService>
         {
             public static string[] SENDER_IDS = new string[] { Constants.SenderID };

             public const string TAG = "MyBroadcastReceiver-GCM";
         }

8.  Aggiungere in **MyBroadcastReceiver.cs** un'altra classe denominata **PushHandlerService** che deriva da **PushHandlerServiceBase**. Assicurarsi di utilizzare la direttiva **Service** nella classe:

     	[Service] //Must use the service tag public class PushHandlerService : PushHandlerServiceBase 
		{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID) 
        	{
        		Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor"); 
        	}

     	}

9.  **PushHandlerServiceBase** implementa i metodi **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** e **OnError()**. La classe di implementazione **PushHandlerService** deve eseguire l'override di questi metodi, che si attiveranno in risposta all'interazione con l'hub di notifica.

10. Eseguire l'override del metodo **OnRegistered()** in **PushHandlerService** con il codice seguente:

         protected override async void OnRegistered(Context context, string registrationId)
         {
             Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
             RegistrationID = registrationId;

             createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

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

    **Nota**

    Nel codice **OnRegistered()** precedente, si noti la capacità di specificare tag per la registrazione a specifici canali di messaggistica.

11. Eseguire l'override del metodo **OnMessage** in **PushHandlerService** con il codice seguente:

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

12. Aggiungere il metodo **createNotification** seguente a **PushHandlerService** per l'invio di notifiche agli utenti come utilizzato in precedenza:

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

13. Eseguire l'override dei membri astratti **OnUnRegistered()**, **OnRecoverableError()** e **OnError()** per consentire la compilazione del codice.

Esecuzione dell'appEsecuzione dell'app con l'emulatore
------------------------------------------------------

Quando si esegue l'app nell'emulatore, assicurarsi di utilizzare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

1.  In **Tools** fare clic su **Open Android Emulator Manager**, selezionare il dispositivo e quindi fare clic su **Edit**.

    ![][18]

2.  Selezionare **Google APIs** in **Target**, quindi fare clic su **OK**.

    ![][19]

3.  Fare clic su **Run** sulla barra degli strumenti nella parte superiore della schermata e selezionare l'app. In questo modo l'emulatore viene avviato e l'app eseguita.

4.  L'app recupera la proprietà *registrationId* da GCM ed esegue la registrazione con l'hub di notifica.

    **Nota**

    Per ricevere notifiche push è necessario configurare un account Google nell'emulatore Android Virtual Device scegliendo **Settings** e quindi **Add Account**. Assicurarsi inoltre che l'emulatore sia connesso a Internet.

Invio di notificheInvio di notifiche dal back-end
-------------------------------------------------

È possibile utilizzare Hub di notifica per inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx). In questa esercitazione verranno inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1.  Creare una nuova applicazione console in Visual C\#:

    ![][20]

2.  Aggiungere un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

         Install-Package WindowsAzure.ServiceBus

    e premere INVIO.

3.  Aprire il file Program.cs e aggiungere l'istruzione using seguente:

         using Microsoft.ServiceBus.Notifications;

4.  Nella classe `Program` aggiungere il metodo seguente:

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{  private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;data private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot; : { private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;msg private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;: private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;Hello from Azure! private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;}}");
         }

5.  Aggiungere quindi le righe seguenti nel metodo Main:

          SendNotificationAsync();
          Console.ReadLine();

6.  Premere F5 per eseguire l'app. Si dovrebbe ricevere una notifica di tipo avviso popup.

    ![][21]

Per inviare una notifica tramite un servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service), quindi eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e selezionare il servizio mobile.

2.  Selezionare la scheda **Scheduler** nella parte superiore.

    ![][22]

3.  Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **On demand**.

    ![][23]

4.  Una volta creato il processo, fare clic sul relativo nome. Fare quindi clic sulla scheda **Script** nella barra superiore.

5.  Inserire lo script seguente all'interno della funzione dell'utilità di pianificazione. Assicurarsi di sostituire i segnaposto con il nome dell'hub di notifica e la stringa di connessione associata a *DefaultFullSharedAccessSignature* ottenuti nel passaggio precedente. Fare clic su **Save**.

         var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
         notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
    	 function (error) {
             if (!error) {
                console.warn("Notification successful");
             }
             else
             {
               console.warn("Notification failed" + error);
             }
           }
         );

6.  Fare clic su **Run Once** sulla barra inferiore. Si dovrebbe ricevere una notifica di tipo avviso popup.

Passaggi successivi
-------------------

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Android. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet), mentre se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Per ulteriori informazioni sull'utilizzo di Hub di notifica, vedere la pagina relativa alle [linee guida per gli hub di notifica](http://msdn.microsoft.com/en-us/library/jj927170.aspx) e quella relativa alle [procedure di Hub di notifica per Android](http://msdn.microsoft.com/en-us/library/dn282661.aspx).

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