---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c919cfce3d868a81f28eb8172314e9639387e933
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112000"
---
### <a name="validate-package-name-and-permissions"></a>Convalidare il nome del pacchetto e le autorizzazioni

1. In **PushDemo.Android** aprire **Opzioni progetto** e quindi **Applicazione Android** nella sezione **Compila**.

1. Verificare che il valore di **Nome del pacchetto** corrisponda al valore usato nel progetto **PushDemo** di [Firebase Console](https://console.firebase.google.com). Il formato di **Nome del pacchetto** era ``com.<organization>.pushdemo``.

1. Impostare **Versione minima di Android** su **Android 8.0 (livello API 26)** e **Versione di destinazione di Android** sul **livello API** più recente.

    > [!NOTE]
    > Ai fini di questa esercitazione sono supportati solo i dispositivi che eseguono il **livello API 26 e successivo**, tuttavia è possibile estendere il supporto ai dispositivi che eseguono versioni precedenti.

1. Verificare che le autorizzazioni **INTERNET** e **READ_PHONE_STATE** siano abilitate in **Autorizzazioni necessarie**.

1. Fare clic su **OK**.

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Aggiungere i pacchetti Xamarin.GooglePlayServices.Base e Xamarin.Firebase.Messaging

1. In **PushDemo.Android** premere **CTRL** + **clic** sulla cartella **Packages** e quindi scegliere **Gestisci pacchetti NuGet**.

1. Cercare **Xamarin.GooglePlayServices.Base** (non **Basement**) e verificare che sia selezionato.

1. Cercare **Xamarin.Firebase.Messaging** e verificare che sia selezionato.

1. Fare clic su **Aggiungi pacchetti**, quindi fare clic su **Accetta** quando viene chiesto di accettare le **condizioni di licenza**.

### <a name="add-the-google-services-json-file"></a>Aggiungere il file JSON di Google Services

1. Premere **CTRL** + **clic** sul progetto `PushDemo.Android`, quindi scegliere **File esistente** dal menu **Aggiungi**.

1. Selezionare il file *google-services.json* scaricato in precedenza quando è stato configurato il progetto **PushDemo** in [Firebase Console](https://console.firebase.google.com), quindi fare clic su **Apri**.

1. Quando richiesto, scegliere **Copia il file nella directory**.

1. Premere **CTRL** + **clic** sul file *google-services.json* nel progetto `PushDemo.Android`, quindi verificare che **GoogleServicesJson** sia impostato come **Azione di compilazione**.

### <a name="handle-push-notifications-for-android"></a>Gestire le notifiche push per Android

1. Premere**CTRL** + **clic** sul progetto `PushDemo.Android`, scegliere **Nuova cartella** dal menu **Aggiungi**, quindi fare clic su **Aggiungi** e specificare *Services* in **Nome cartella**.

1. Premere **CTRL** + **clic** sulla cartella **Services**, quindi scegliere **Nuovo file** dal menu **Aggiungi**.

1. Selezionare **Generale** > **Classe vuota**, immettere *DeviceInstallationService.cs* in **Nome**, quindi fare clic su **Nuovo** e aggiungere l'implementazione seguente.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Questa classe fornisce un ID univoco (con [Secure.AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) come parte del payload di registrazione dell'hub di notifica.

1. Aggiungere alla cartella **Services** un'altra **classe vuota** denominata *PushNotificationFirebaseMessagingService.cs*, quindi aggiungere l'implementazione seguente.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. In **MainActivity.cs** verificare che gli spazi dei nomi seguenti siano stati aggiunti all'inizio del file.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. In **MainActivity.cs** impostare **LaunchMode** su **SingleTop** in modo che **MainActivity** non venga creato di nuovo all'apertura.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Aggiungere proprietà private e i campi sottostanti corrispondenti per archiviare un riferimento alle implementazioni di **IPushNotificationActionService** e **IDeviceInstallationService**.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Implementare l'interfaccia **IOnSuccessListener** per recuperare e archiviare il token **Firebase**.

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Aggiungere un nuovo metodo denominato **ProcessNotificationActions** che controlla se un determinato **Intent** ha un valore aggiuntivo denominato *action*. Attivare tale azione in modo condizionale usando l'implementazione di **IPushDemoNotificationActionService**.

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Eseguire l'override del metodo **OnNewIntent** per chiamare il metodo **CheckIntentForNotificationActions**.

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Poiché **LaunchMode** per **Activity** è impostato su **SingleTop**, verrà inviato un **Intent** all'istanza di **Activity** esistente tramite il metodo **OnNewIntent** invece del metodo **OnCreate**, pertanto è necessario gestire una finalità in ingresso in entrambi i metodi **OnCreate** e **OnNewIntent**.

1. Aggiornare il metodo **OnCreate** per chiamare `Bootstrap.Begin` subito dopo la chiamata a `base.OnCreate` passando l'implementazione specifica della piattaforma di **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. Nello stesso metodo, chiamare in modo condizionale **GetInstanceId** nell'istanza di **FirebaseApp**, subito dopo la chiamata a `Bootstrap.Begin`, aggiungendo **MainActivity** come **IOnSuccessListener**.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. Sempre in **OnCreate**, chiamare **ProcessNotificationActions** subito dopo la chiamata a `LoadApplication` passando l'elemento **Intent** corrente.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> È necessario ripetere la registrazione dell'app ogni volta che la si esegue e arrestarla da una sessione di debug per continuare a ricevere le notifiche push.
