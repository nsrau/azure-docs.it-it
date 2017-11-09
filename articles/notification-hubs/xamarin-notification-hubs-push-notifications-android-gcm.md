---
title: Introduzione a Hub di notifica per le app per Xamarin.Android | Documentazione Microsoft
description: Questa esercitazione contiene informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin Android.
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/25/2017
ms.author: yuaxu
ms.openlocfilehash: f9fef96b71e0db7b15ff5208e9bd1a0b4ecf7211
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Introduzione ad Hub di notifica con Xamarin per Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione di Xamarin.Android. Verrà creata un'app per Xamarin.Android vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM). Al termine, sarà possibile usare l’hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][GitHub].

Questa esercitazione illustra uno scenario di trasmissione semplice tramite hub di notifica.

## <a name="before-you-begin"></a>Prima di iniziare
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione è disponibile su GitHub [qui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario disporre di:

* Visual Studio con Xamarin su Windows o Xamarin Studio su Mac OS X. Per istruzioni complete sull'installazione , vedere [Configurazione e installazione di Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* Un account Google attivo
* [Componente di messaggistica di Azure]
* [Componente client di Google Cloud Messaging]

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Xamarin.Android.

> [!IMPORTANT]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-google-cloud-messaging"></a>Abilitare Google Cloud Messaging
[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Configurare l'hub di notifica
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="5">

<li><p>Scegliere la scheda <b>Configure</b> (Configura) nella parte superiore, immettere il valore di <b>API Key</b> (Chiave API) ottenuto nella sezione precedente e quindi selezionare su <b>Save</b> (Salva).</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

L'hub di notifica è ora configurato per l'uso con GCM e si dispone delle stringhe di connessione per registrare l'app in modo da ricevere e inviare le notifiche push.

## <a name="connect-your-app-to-the-notification-hub"></a>Connettere l'app all'hub di notifica
Creare prima di tutto un nuovo progetto.
1. In Xamarin Studio scegliere **New Solution** (Nuova soluzione)  > **Android App** (App Android) e quindi selezionare **Next** (Avanti).
   
      ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Compilare i campi **App Name** (Nome app) e **Identifier** (Identificatore). Scegliere **Target Plaforms** (Piattaforme di destinazione) per selezionare la piattaforma da supportare e quindi fare clic su **Next** (Avanti) e su **Create** (Crea).
   
      ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)

    In questo modo viene creato un nuovo progetto Android.

3. Aprire le proprietà del progetto facendo clic con il pulsante destro del mouse sul nuovo progetto nella visualizzazione Solution e scegliendo **Options**. Selezionare la voce **Android Application** (Applicazione Android) nella sezione **Build** (Crea).
   
    Verificare che la prima lettera di **Package name** sia minuscola.
   
   > [!IMPORTANT]
   > La prima lettera del nome del pacchetto deve essere minuscola. In caso contrario, verranno visualizzati errori del manifesto dell'applicazione al momento della registrazione di **BroadcastReceiver** e **IntentFilter** per le notifiche push più avanti.
   > 
   > 
   
      ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)
4. Impostare facoltativamente **Minimum Android version** su un altro livello API.
5. Facoltativamente, impostare **Target Android version** sulla versione dell'API di destinazione (deve essere almeno API livello 8 o superiore).
6. Scegliere **OK** e chiudere la finestra di dialogo delle opzioni del progetto.

### <a name="add-the-required-components-to-your-project"></a>Aggiungere i componenti necessari al progetto
Il client Google Cloud Messaging disponibile in Xamarin Component Store semplifica il processo di supporto delle notifiche push in Xamarin.Android.

1. Fare clic con il pulsante destro del mouse sulla cartella Components nell'app Xamarin.Android e scegliere **Get More Components**.
2. Cercare il componente **Messaggistica di Azure** e aggiungerlo al progetto.
3. Cercare il componente **Client Google Cloud Messaging** e aggiungerlo al progetto.

### <a name="set-up-notification-hubs-in-your-project"></a>Configurare Hub di notifica nel progetto
1. Raccogliere le informazioni seguenti per l'app Android e l'hub di notifica:
   
   * **GoogleProjectNumber**: ottenere il valore Project Number (Numero progetto) dalla panoramica dell'app nel portale di sviluppo di Google. Prendere nota di questo valore in precedenza, durante la creazione dell'app nel portale.
   * **Stringa di connessione Listen**: nel dashboard del [portale di Azure classico] scegliere **Visualizza stringhe di connessione**. Copiare la stringa di connessione *DefaultListenSharedAccessSignature* per questo valore.
   * **Hub name**: si tratta del nome dell'hub del [portale di Azure classico]. Ad esempio, *mynotificationhub2*.
     
2. Creare una classe **Constants.cs** per il progetto Xamarin e definire i valori costanti seguenti nella classe. Sostituire i segnaposto con i valori.
     
        public static class Constants
        {
     
           public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
3. Aggiungere le istruzioni using seguenti a **MainActivity.cs**:
   
        using Android.Util;
        using Gcm.Client;
4. Aggiungere una variabile di istanza alla classe `MainActivity` che verrà usata per visualizzare una finestra di dialogo di avviso quando l'app è in esecuzione:
   
        public static MainActivity instance;
5. Creare il metodo seguente nella classe **MainActivity** :
   
        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);
   
            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }
6. Nel metodo `OnCreate` di **MainActivity.cs** inizializzare la variabile `instance` e aggiungere una chiamata a `RegisterWithGCM`:
   
        protected override void OnCreate (Bundle bundle)
        {
            instance = this;
   
            base.OnCreate (bundle);
   
            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);
   
            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);
   
            RegisterWithGCM();
        }
7. Creare una nuova classe **MyBroadcastReceiver**.
   
   > [!NOTE]
   > Di seguito è illustrata in dettaglio la creazione da zero di una classe **BroadcastReceiver** . Esiste tuttavia un'alternativa più rapida alla creazione manuale di **MyBroadcastReceiver.cs**, che consiste nel fare riferimento al file **GcmService.cs** disponibile nel progetto Xamarin.Android di esempio incluso con gli [esempi di NotificationHubs][GitHub]. Anche duplicare il file **GcmService.cs** e modificare i nomi delle classi può essere un ottimo punto di partenza.
   > 
   > 
8. Aggiungere le istruzioni using seguenti a **MyBroadcastReceiver.cs** , facendo riferimento al componente e all'assembly aggiunti in precedenza:
   
        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;
9. In **MyBroadcastReceiver.cs** aggiungere le richieste di autorizzazione seguenti tra le istruzioni **using** e la dichiarazione **namespace**:
   
        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
   
        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
10. In **MyBroadcastReceiver.cs** modificare la classe **MyBroadcastReceiver** in modo che corrisponda all'esempio seguente:
   
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
11. Aggiungere un'altra classe in **MyBroadcastReceiver.cs** denominata **PushHandlerService** che deriva da **GcmServiceBase**. Assicurarsi di applicare l'attributo **Service** alla classe:
    
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
12. **GcmServiceBase** implementa i metodi **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** e **OnError()**. La classe di implementazione **PushHandlerService** deve eseguire l'override di questi metodi, che si attiveranno in risposta all'interazione con l'hub di notifica.
13. Eseguire l'override del metodo **OnRegistered()** in **PushHandlerService** con il codice seguente:
    
         protected override void OnRegistered(Context context, string registrationId)
         {
             Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
             RegistrationID = registrationId;
    
             createNotification("PushHandlerService-GCM Registered...",
                                 "The device has been Registered!");
    
             Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                         context);
             try
             {
                 Hub.UnregisterAll(registrationId);
             }
             catch (Exception ex)
             {
                 Log.Error(MyBroadcastReceiver.TAG, ex.Message);
             }
    
             //var tags = new List<string>() { "falcons" }; // create tags if you want
             var tags = new List<string>() {};
    
             try
             {
                 var hubRegistration = Hub.Register(registrationId, tags.ToArray());
             }
             catch (Exception ex)
             {
                 Log.Error(MyBroadcastReceiver.TAG, ex.Message);
             }
         }
    
    > [!NOTE]
    > Nel codice **OnRegistered()** precedente notare la capacità di specificare tag per la registrazione a canali di messaggistica specifici.
    > 
    > 
14. Eseguire l'override del metodo **OnMessage** in **PushHandlerService** con il codice seguente:
    
        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");
    
            var msg = new StringBuilder();
    
            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }
    
            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }
15. Aggiungere i metodi **createNotification** e **dialogNotify** seguenti a **PushHandlerService** per informare gli utenti quando viene ricevuta una notifica.
    
    > [!NOTE]
    > La progettazione di notifiche in Android versione 5.0 e successive rappresenta un'importante variazione rispetto alle versioni precedenti. Se si testa in Android 5.0 o versioni successive, l'app deve essere in esecuzione per poter ricevere la notifica. Per altre informazioni, vedere [Notifiche di Android](http://go.microsoft.com/fwlink/?LinkId=615880).
    > 
    > 
    
        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
    
            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));
    
            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);
    
            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;
    
            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));
    
            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }
    
        protected void dialogNotify(String title, String message)
        {
    
            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }
16. Eseguire l'override dei membri astratti **OnUnRegistered()**, **OnRecoverableError()** e **OnError()** per consentire la compilazione del codice:
    
        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);
    
            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }
    
        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);
    
            return base.OnRecoverableError (context, errorId);
        }
    
        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

## <a name="run-your-app-in-the-emulator"></a>Eseguire l'app nell'emulatore
Se si esegue l'app nell'emulatore, assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

> [!IMPORTANT]
> Per ricevere notifiche push, è necessario impostare un account Google in Android Virtual Device. Nell'emulatore passare a **Settings** (Impostazioni) e scegliere **Add Account** (Aggiungi account). Assicurarsi inoltre che l'emulatore sia connesso a Internet.
> 
> [!NOTE]
> La progettazione di notifiche in Android versione 5.0 e successive rappresenta un'importante variazione rispetto alle versioni precedenti. Per altre informazioni, vedere [Notifiche di Android](http://go.microsoft.com/fwlink/?LinkId=615880).
> 
> 

1. In **Tools** (Strumenti) scegliere **Open Android Emulator Manager** (Apri Gestione emulatori Android), selezionare il dispositivo e quindi scegliere **Edit** (Modifica).
   
      ![][18]
2. Selezionare **Google APIs** (API Google) in **Target** (Destinazione) e quindi scegliere **OK**.
   
      ![][19]
3. Scegliere **Run** (Esegui) sulla barra degli strumenti nella parte superiore della schermata e selezionare l'app. In questo modo l'emulatore viene avviato e l'app eseguita.
   
   L'app recupera la proprietà *registrationId* da GCM ed esegue la registrazione con l'hub di notifica.

## <a name="send-notifications-from-your-backend"></a>Inviare notifiche dal back-end
È possibile testare la ricezione delle notifiche nell'app inviando notifiche nel [portale di Azure classico] tramite la scheda Debug nell'hub di notifica, come illustrato nella schermata seguente.

![][30]

Le notifiche push vengono in genere inviate in un servizio back-end come Servizi mobili o ASP.NET usando una libreria compatibile. È anche possibile utilizzare l'API REST direttamente per inviare messaggi di notifica se una libreria non è disponibile per il back-end.

Di seguito è riportato un elenco di altre esercitazioni, che è possibile esaminare per l'invio di notifiche:

* ASP.NET: vedere [Usare Hub di notifica per inviare notifiche push agli utenti].
* Azure Notification Hubs Java SDK: vedere [Come usare Hub di notifica da Java](notification-hubs-java-push-notification-tutorial.md) per l'invio di notifiche da Java. È stato testato in Eclipse per lo sviluppo per Android.
* PHP: vedere [Come usare Hub di notifica da PHP](notification-hubs-php-push-notification-tutorial.md).

Nelle sezioni secondarie successive di questa esercitazione vengono inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

#### <a name="optional-send-notifications-by-using-a-net-app"></a>(Facoltativo) Inviare notifiche tramite un'app .NET
In questa sezione si invieranno notifiche con un'app console .NET.

1. Creare una nuova applicazione console in Visual C#:
   
      ![][20]
2. In Visual Studio scegliere **Strumenti** > **Gestione pacchetti NuGet** e quindi selezionare **Console di Gestione pacchetti**.
   
    In questo modo viene visualizzata la console di Gestione pacchetti in Visual Studio.
3. Nella finestra Console di Gestione pacchetti impostare **Progetto predefinito** sul nuovo progetto di applicazione console, quindi eseguire il comando seguente nella finestra della console:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Verrà aggiunto un riferimento all’SDK dell’Hub di notifica di Azure mediante il <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacchetto NuGet Microsoft.Azure.NotificationHubs</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
4. Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:
   
        using Microsoft.Azure.NotificationHubs;
5. Nella classe `Program` aggiungere il metodo seguente. Aggiornare il testo segnaposto con la stringa di connessione *DefaultFullSharedAccessSignature* e il nome dell'hub dal [portale di Azure classico].
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }
6. Aggiungere le righe seguenti nel metodo **Main** :
   
         SendNotificationAsync();
         Console.ReadLine();
7. Premere F5 per eseguire l'app. Si dovrebbe ricevere una notifica nell’app.
   
      ![][21]

#### <a name="optional-send-notifications-by-using-a-mobile-service"></a>(Facoltativo) Inviare notifiche tramite un servizio mobile
1. Guardare [Introduzione a Servizi mobili].
2. Accedere al [portale di Azure classico]e selezionare il servizio mobile.
3. Selezionare la scheda **Utilità di pianificazione** in alto.
   
      ![][22]
4. Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **On demand**.
   
      ![][23]
5. Al termine della creazione del processo, selezionare il relativo nome. Scegliere la scheda **Script** nella barra superiore.
6. Inserire lo script seguente all'interno della funzione dell'utilità di pianificazione. Assicurarsi di sostituire i segnaposto con il nome dell'hub di notifica e la stringa di connessione associata a *DefaultFullSharedAccessSignature* ottenuti nel passaggio precedente. Scegliere **Salva**.
   
        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
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
7. Scegliere **Esegui una volta** sulla barra inferiore. Si dovrebbe ricevere una notifica di tipo avviso popup.

## <a name="next-steps"></a>Passaggi successivi
In questo semplice esempio le notifiche sono state trasmesse a tutti i dispositivi Android. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti]. Se si desidera segmentare gli utenti per gruppi di interesse, consultare [Uso di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere [Panoramica dell'Hub di notifica] e [Notification Hubs How-To for Android.] (Procedura di Hub di notifica per Android).

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[portale di Azure classico]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Panoramica dell'Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android.]: http://msdn.microsoft.com/library/dn282661.aspx

[Usare Hub di notifica per inviare notifiche push agli utenti]: /manage/services/notification-hubs/notify-users-aspnet
[Uso di Hub di notifica per inviare le ultime notizie]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Componente client di Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Componente di messaggistica di Azure]: http://components.xamarin.com/view/azure-messaging
