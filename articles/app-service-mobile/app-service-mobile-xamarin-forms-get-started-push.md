---
title: Aggiungere notifiche push all'app Xamarin.Forms | Microsoft Docs
description: Informazioni su come usare i servizi di Azure per inviare notifiche push multipiattaforma alle app Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f1fe97c6b3e2d28b7e17d035bc7e3ecced8a0d0f
ms.contentlocale: it-it
ms.lasthandoff: 03/21/2017

---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Aggiungere notifiche push all'app Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione vengono aggiunte notifiche push a tutti i progetti creati nella [guida introduttiva per Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md), in modo che a ogni inserimento di record venga inviata una notifica push a tutti i client multipiattaforma.

Se non si usa il progetto server di avvio rapido scaricato, sarà necessario aggiungere il pacchetto di estensione di notifica push. Per altre informazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Prerequisiti
Per iOS sono necessari un dispositivo iOS fisico e un'[appartenenza all'Apple Developer Program](https://developer.apple.com/programs/ios/). [Il simulatore iOS non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Configurare un hub di notifica
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aggiornare il progetto server per l'invio di notifiche push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Configurare ed eseguire il progetto Android (facoltativo)
Completare questa sezione per abilitare le notifiche push per il progetto Xamarin.Forms Droid per Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Abilitare Firebase Cloud Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Configurare il back-end dell'app per dispositivi mobili per inviare richieste push usando FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Aggiungere notifiche push al progetto Android
Dopo aver configurato il back-end con FCM, è possibile aggiungere componenti e codici al client per la registrazione in FCM, iscriversi alle notifiche push con l'Hub di notifica di Azure tramite il back-end dell'app per dispositivi mobili e ricevere notifiche.

1. Nel progetto **Droid** fare doppio clic sulla cartella **Components** (Componenti) e scegliere **Get More Components...** (Recupera altri componenti...). Cercare quindi il componente **Google Cloud Messaging Client** (Client Google Cloud Messaging) e aggiungerlo al progetto. Questo componente supporta le notifiche push per un progetto Xamarin Android.
2. Aprire il file di progetto MainActivity.cs e aggiungere l'istruzione seguente all'inizio del file:

        using Gcm.Client;
3. Aggiungere il codice seguente al metodo **OnCreate** dopo la chiamata a **LoadApplication**:

        try
        {
            // Check to ensure everything's set up right
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
4. Aggiungere un nuovo metodo helper **CreateAndShowDialog** , come indicato di seguito:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. Aggiungere il codice seguente alla classe **MainActivity** :

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
7. Aggiungere un nuovo file di classe al progetto **Droid** denominato `GcmService.cs`, quindi assicurarsi che le istruzioni **using** seguenti siano presenti nella parte iniziale del file:

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
8. Aggiungere le richieste di autorizzazione seguenti alla parte iniziale del file, dopo le istruzioni **using** e prima della dichiarazione **namespace**.

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. Aggiungere la definizione di classe seguente allo spazio dei nomi.

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > Sostituire **<PROJECT_NUMBER>** con il numero di progetto annotato in precedenza.    
   >
   >
10. Sostituire la classe **GcmService** vuota con il codice seguente, che usa il nuovo ricevitore di trasmissione:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. Aggiungere il codice seguente alla classe **GcmService** in modo da sostituire il gestore eventi **OnRegistered** e implementare un metodo **Register**.

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
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

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

    Si noti che questo codice usa il parametro `messageParam` nella registrazione del modello.
12. Aggiungere il codice seguente che implementa **OnMessage**:

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

    Questa operazione consente di gestire le notifiche in ingresso e di inviarle alla gestione notifiche per la visualizzazione.
13. **GcmServiceBase** richiede anche l'implementazione dei metodi gestore **OnUnRegistered** e **OnError**, come illustrato di seguito:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

È ora possibile testare le notifiche push nell'app in esecuzione su un dispositivo Android o nell'emulatore.

### <a name="test-push-notifications-in-your-android-app"></a>Testare le notifiche push nell'app Android
I primi due passaggi sono necessari solo per i test eseguiti in un emulatore.

1. Assicurarsi di distribuire o eseguire il debug su un dispositivo virtuale che ha le API Google impostate come destinazione, come illustrato di seguito nel gestore del dispositivo virtuale Android.
2. Aggiungere un account Google al dispositivo Android facendo clic su **App** > **Impostazioni** > **Aggiunti account**. Seguire quindi le istruzioni per aggiungere un account Google esistente al dispositivo o per crearne uno nuovo.
3. In Visual Studio o Xamarin Studio fare clic con il pulsante destro del mouse sul progetto **Droid** e scegliere **Imposta come progetto di avvio**.
4. Fare clic su **Esegui** per creare il progetto e avviare l'app sul dispositivo Android o sull'emulatore.
5. Nell'app digitare un'attività e fare clic sull'icona con il segno più (**+**).
6. Assicurarsi di ricevere una notifica quando viene aggiunto un elemento.

## <a name="configure-and-run-the-ios-project-optional"></a>Configurare ed eseguire il progetto iOS (facoltativo)
Questa sezione illustra l'esecuzione del progetto Xamarin iOS per dispositivi iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configurare l'hub di notifica per APNS
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

In seguito verrà configurata l'impostazione di progetto iOS in Xamarin Studio o Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Aggiungere notifiche push all'app iOS
1. Nel progetto **iOS** aprire il file AppDelegate.cs e aggiungere l'istruzione seguente all'inizio del file di codice.

        using Newtonsoft.Json.Linq;
2. Nella classe **AppDelegate** aggiungere un override per l'evento **RegisteredForRemoteNotifications** per eseguire la registrazione per le notifiche:

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. In **AppDelegate** aggiungere anche l'override seguente per il gestore eventi **DidReceiveRemoteNotification**:

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
4. Nella classe **AppDelegate** aggiungere il codice seguente al metodo **FinishedLaunching**:

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

#### <a name="test-push-notifications-in-your-ios-app"></a>Testare le notifiche push nell'app iOS
1. Fare clic con il pulsante destro del mouse sul progetto iOS e quindi scegliere **Imposta come progetto di avvio**.
2. Scegliere **Esegui** o premere **F5** per compilare il progetto e avviare l'app in un dispositivo iOS e quindi fare clic su **OK** per accettare le notifiche push.

   > [!NOTE]
   > È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.
   >
   >
3. Nell'app digitare un'attività e fare clic sull'icona con il segno più (**+**).
4. Verificare che venga ricevuta una notifica e quindi fare clic su **OK** per ignorarla.

## <a name="configure-and-run-windows-projects-optional"></a>Configurare ed eseguire progetti Windows (facoltativo)
Questa sezione illustra l'esecuzione dei progetti Xamarin.Forms WinApp e WinPhone81 per dispositivi Windows. Questa procedura supporta anche progetti per la piattaforma UWP (Universal Windows Platform). Se non si usano dispositivi Windows, è possibile ignorare questa sezione.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrare l'app Windows per le notifiche push con il servizio di notifica Windows (WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurare l'hub di notifica per WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Aggiungere notifiche push all'app di Windows
1. In Visual Studio aprire il file **App.xaml.cs** in un progetto Windows e aggiungere le istruzioni seguenti.

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
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

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

    Questo metodo ottiene il canale per la notifica push e registra un modello per la ricezione di notifiche di modello dall'hub di notifica. A questo client verrà recapitata una notifica di modello che supporta *messageParam* .
3. Nel file App.xaml.cs aggiornare la definizione del metodo **OnLaunched** del gestore eventi aggiungendo il modificatore `async` e quindi aggiungere la riga di codice seguente alla fine del metodo:

        await InitNotificationsAsync();

    In questo modo è possibile garantire che la registrazione della notifica push venga creata o aggiornata a ogni avvio dell'app. È importante eseguire questa operazione per assicurare che il canale di notifica push WNS sia sempre attivo.  
4. In Esplora soluzioni di Visual Studio aprire il file **Package.appxmanifest** e in **Notifiche** impostare **Avvisi popup supportati** su **Sì**.
5. Compilare l'app e verificare che non siano presenti errori. A questo punto l'app client dovrebbe eseguire la registrazione per le notifiche di modello dal back-end dell'app per dispositivi mobili. Ripetere questa sezione per ogni progetto Windows nella soluzione.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testare le notifiche push nell'app di Windows
1. In Visual Studio fare clic con il pulsante destro del mouse su un progetto Windows e quindi scegliere **Imposta come progetto di avvio**.
2. Premere il pulsante **Esegui** per compilare il progetto e avviare l'app.
3. Nell'app digitare un nome per un nuovo elemento todoitem, quindi fare clic sull'icona del segno più (**+**) per aggiungerlo.
4. Assicurarsi di ricevere una notifica quando viene aggiunto l'elemento.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle notifiche push:

* [Diagnose push notification issues](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  (Diagnosticare i problemi relativi alle notifiche push) Esistono varie ragioni per cui le notifiche possono essere eliminate o non giungere ai dispositivi. Questo argomento illustra come analizzare e capire la causa radice degli errori relativi alle notifiche push.

È possibile anche proseguire con una delle esercitazioni seguenti:

* [Add authentication to your app ](app-service-mobile-xamarin-forms-get-started-users.md)  
  (Aggiungere l'autenticazione all'app) Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.
* [Abilitare la sincronizzazione offline per l'app per dispositivi mobili Xamarin.Forms](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Informazioni su come aggiungere il supporto offline all'app usando il back-end di un'app per dispositivi mobili. Con la sincronizzazione offline è possibile interagire con un'app per dispositivi mobili &mdash;visualizzando, aggiungendo e modificando i dati&mdash; anche se non è disponibile una connessione di rete.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

