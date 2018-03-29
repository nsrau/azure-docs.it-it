---
title: Aggiungere notifiche push all'app Xamarin.Forms | Microsoft Docs
description: Informazioni su come usare i servizi di Azure per inviare notifiche push multipiattaforma alle app Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 0bea00d411205541684e807182abd3236c09bd9d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Aggiungere notifiche push all'app Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione vengono aggiunte notifiche push a tutti i progetti creati nella [guida introduttiva per Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md), in modo che a ogni inserimento di record venga inviata una notifica push a tutti i client multipiattaforma.

Se non si usa il progetto server di avvio rapido scaricato, sarà necessario aggiungere il pacchetto di estensione di notifica push. Per altre informazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>prerequisiti
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

1. Nel progetto **Droid** fare clic con il pulsante destro del mouse su **Riferimenti > Gestisci pacchetti NuGet**.
1. Nella finestra Gestione pacchetti NuGet cercare il pacchetto **Xamarin.Firebase.Messaging** e aggiungerlo al progetto.
1. Nelle proprietà del progetto **Droid** impostare la compilazione dell'app con Android 7.0 o versione successiva.
1. Aggiungere il file **google-services.json**, scaricato dalla console di Firebase, alla radice del progetto **Droid** e impostare la relativa azione di compilazione su **GoogleServicesJson**. Per altre informazioni, vedere [Add the Google Services JSON File](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File) (Aggiungere il file JSON dei servizi Google).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrazione con Firebase Cloud Messaging

1. Aprire il file **AndroidManifest.xml** e inserire gli elementi `<receiver>` seguenti nell'elemento `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementazione del servizio Instance ID di Firebase

1. Aggiungere una nuova classe denominata `FirebaseRegistrationService` al progetto **Droid**, quindi assicurarsi che le istruzioni `using` seguenti siano presenti all'inizio del file:

        using System.Threading.Tasks;
        using Android.App;
        using Android.Util;
        using Firebase.Iid;
        using Microsoft.WindowsAzure.MobileServices;

1. Sostituire la classe `FirebaseRegistrationService` vuota con il codice seguente:

        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class FirebaseRegistrationService : FirebaseInstanceIdService
        {
            const string TAG = "FirebaseRegistrationService";

            public override void OnTokenRefresh()
            {
                var refreshedToken = FirebaseInstanceId.Instance.Token;
                Log.Debug(TAG, "Refreshed token: " + refreshedToken);
                SendRegistrationTokenToAzureNotificationHub(refreshedToken);
            }

            void SendRegistrationTokenToAzureNotificationHub(string token)
            {
                // Update notification hub registration
                Task.Run(async () =>
                {
                    await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
                });
            }
        }

    La classe `FirebaseRegistrationService` è responsabile della generazione di token di sicurezza che autorizzano l'accesso a FCM da parte dell'applicazione. Il metodo `OnTokenRefresh` viene richiamato quando l'applicazione riceve un token di registrazione da FCM. Il metodo recupera il token dalla proprietà `FirebaseInstanceId.Instance.Token`, che viene aggiornata in modo asincrono da FCM. Il metodo `OnTokenRefresh` viene richiamato raramente, perché il token viene aggiornato solo quando l'applicazione viene installata o disinstallata, l'utente elimina i dati dell'applicazione, l'applicazione cancella l'ID istanza o la sicurezza del token risulta compromessa. Inoltre, il servizio Instance ID di FCM richiederà all'applicazione l'aggiornamento periodico del token, in genere ogni 6 mesi.

    Il metodo `OnTokenRefresh` richiama anche il metodo `SendRegistrationTokenToAzureNotificationHub`, che viene usato per associare il token di registrazione dell'utente all'Hub di notifica di Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registrazione con l'Hub di notifica di Azure

1. Aggiungere una nuova classe denominata `AzureNotificationHubService` al progetto **Droid**, quindi assicurarsi che le istruzioni `using` seguenti siano presenti all'inizio del file:

        using System;
        using System.Threading.Tasks;
        using Android.Util;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

1. Sostituire la classe `AzureNotificationHubService` vuota con il codice seguente:

        public class AzureNotificationHubService
        {
            const string TAG = "AzureNotificationHubService";

            public static async Task RegisterAsync(Push push, string token)
            {
                try
                {
                    const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                    JObject templates = new JObject();
                    templates["genericMessage"] = new JObject
                    {
                        {"body", templateBody}
                    };

                    await push.RegisterAsync(token, templates);
                    Log.Info("Push Installation Id: ", push.InstallationId.ToString());
                }
                catch (Exception ex)
                {
                    Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
                }
            }
        }

    Il metodo `RegisterAsync` crea un modello di messaggio di notifica semplice come JSON ed esegue la registrazione per ricevere le notifiche modello dall'hub di notifica, usando il token di registrazione Firebase. In questo modo, le notifiche inviate dall'Hub di notifica di Azure saranno destinate al dispositivo rappresentato dal token di registrazione.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Visualizzazione del contenuto di una notifica push

1. Aggiungere una nuova classe denominata `FirebaseNotificationService` al progetto **Droid**, quindi assicurarsi che le istruzioni `using` seguenti siano presenti all'inizio del file:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V7.App;
        using Android.Util;
        using Firebase.Messaging;

1. Sostituire la classe `FirebaseNotificationService` vuota con il codice seguente:

        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class FirebaseNotificationService : FirebaseMessagingService
        {
            const string TAG = "FirebaseNotificationService";

            public override void OnMessageReceived(RemoteMessage message)
            {
                Log.Debug(TAG, "From: " + message.From);

                // Pull message body out of the template
                var messageBody = message.Data["message"];
                if (string.IsNullOrWhiteSpace(messageBody))
                    return;

                Log.Debug(TAG, "Notification message body: " + messageBody);
                SendNotification(messageBody);
            }

            void SendNotification(string messageBody)
            {
                var intent = new Intent(this, typeof(MainActivity));
                intent.AddFlags(ActivityFlags.ClearTop);
                var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

                var notificationBuilder = new NotificationCompat.Builder(this)
                    .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                    .SetContentTitle("New Todo Item")
                    .SetContentText(messageBody)
                    .SetContentIntent(pendingIntent)
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                    .SetAutoCancel(true);

                var notificationManager = NotificationManager.FromContext(this);
                notificationManager.Notify(0, notificationBuilder.Build());
            }
        }

    Il metodo `OnMessageReceived`, che viene richiamato quando un'applicazione riceve una notifica da FCM, estrae il contenuto del messaggio e chiama il metodo `SendNotification`. Questo metodo converte il contenuto del messaggio in una notifica locale, che viene avviata durante l'esecuzione dell'applicazione e visualizzata nell'area di notifica.

È ora possibile testare le notifiche push nell'app in esecuzione su un dispositivo Android o nell'emulatore.

### <a name="test-push-notifications-in-your-android-app"></a>Testare le notifiche push nell'app Android
I primi due passaggi sono necessari solo per i test eseguiti in un emulatore.

1. Assicurarsi che l'operazione di distribuzione o debug venga eseguita su un dispositivo o un emulatore configurato con Google Play Services. Per eseguire questa verifica, controllare che le app **Play** siano installate nel dispositivo o emulatore.
2. Aggiungere un account Google al dispositivo Android facendo clic su **App** > **Impostazioni** > **Aggiunti account**. Seguire quindi le istruzioni per aggiungere un account Google esistente al dispositivo o per crearne uno nuovo.
3. In Visual Studio o Xamarin Studio fare clic con il pulsante destro del mouse sul progetto **Droid** e scegliere **Imposta come progetto di avvio**.
4. Fare clic su **Esegui** per creare il progetto e avviare l'app sul dispositivo Android o sull'emulatore.
5. Nell'app digitare un'attività e fare clic sull'icona con il segno più (**+**).
6. Assicurarsi di ricevere una notifica quando viene aggiunto un elemento.

## <a name="configure-and-run-the-ios-project-optional"></a>Configurare ed eseguire il progetto iOS (facoltativo)
Questa sezione illustra l'esecuzione del progetto Xamarin iOS per dispositivi iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

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

* [Sending Push Notifications from Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/) (Invio di notifiche push dalle app per dispositivi mobili di Azure)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Remote Notifications with Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/) (Notifiche remote con Firebase Cloud Messaging)
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
