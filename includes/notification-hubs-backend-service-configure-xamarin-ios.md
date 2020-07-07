---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 1dc491084f65bc90397b0897de6b6cfe4f2fd410
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448759"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Configurare Info.plist e Entitlements.plist

1. Assicurarsi di avere eseguito l'accesso con il proprio **account Apple Developer** in **Visual Studio** > **Preferenze** > **Pubblicazione** > **Account per sviluppatore Apple** e che siano stati scaricati il *certificato* e il *profilo di provisioning* appropriati. Questi asset dovrebbero essere stati creati nei passaggi precedenti.

1. In **PushDemo.iOS** aprire **Info.plist** e verificare che il valore di **BundleIdentifier** corrisponda al valore usato per il rispettivo profilo di provisioning nel [portale Apple Developer](https://developer.apple.com). **BundleIdentifier** era in formato ``com.<organization>.PushDemo``.

1. Nello stesso file impostare **Versione minima del sistema** su **13.0**.

    > [!NOTE]
    > Ai fini di questa esercitazione sono supportati solo i dispositivi che eseguono **iOS 13.0 e versioni successive**, tuttavia è possibile estendere il supporto ai dispositivi che eseguono versioni precedenti.

1. Aprire **Opzioni progetto** per **PushDemo.iOS** (fare doppio clic sul progetto).

1. In **Opzioni progetto**, in **Compila > Firma del bundle iOS**, verificare che l'account Developer sia selezionato in **Team**. Verificare quindi che sia selezionato "Automatically manage signing" (Gestisci firma automaticamente) e che siano automaticamente selezionati il certificato di firma e il profilo di provisioning.

    > [!NOTE]
    > Se il *certificato di firma* e il *profilo di provisioning* non sono stati selezionati automaticamente, scegliere **Provisioning manuale**, quindi fare clic su **Bundle Signing Options** (Opzioni di firma del bundle). Verificare che il proprio *Team* sia selezionato per **Identità di firma** e che il profilo di provisioning specifico di *PushDemo* sia selezionato per **Profilo di provisioning** per entrambe le configurazioni **Debug** e **Rilascio**, controllando anche che sia selezionato **iPhone** per **Piattaforma** in entrambi i casi.

1. In **PushDemo.iOS** aprire **Entitlements.plist** e assicurarsi che l'opzione **Abilita Notifiche push** sia selezionata quando viene visualizzata nella scheda **Entitlement**. Verificare quindi che **Ambiente APS** sia impostato su **Sviluppo** quando viene visualizzato nella scheda **Origine**.

### <a name="handle-push-notifications-for-ios"></a>Gestire le notifiche push per iOS

1. Premere**CTRL** + **clic** sul progetto **PushDemo.iOS**, scegliere **Nuova cartella** dal menu **Aggiungi**, quindi fare clic su **Aggiungi** e specificare *Services* in **Nome cartella**.

1. Premere **CTRL** + **clic** sulla cartella **Services**, quindi scegliere **Nuovo file** dal menu **Aggiungi**.

1. Selezionare **Generale** > **Classe vuota**, immettere *DeviceInstallationService.cs* in **Nome**, quindi fare clic su **Nuovo** e aggiungere l'implementazione seguente.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for APNS");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Questa classe fornisce un ID univoco (con il valore [UIDevice.IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12)) e il payload di registrazione dell'hub di notifica.

1. Aggiungere al progetto **PushDemo.iOS** una nuova cartella denominata *Extensions*, quindi aggiungere alla cartella una **classe vuota** denominata *NSDataExtensions.cs* con l'implementazione seguente.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. In **AppDelegate.cs** verificare che gli spazi dei nomi seguenti siano stati aggiunti all'inizio del file.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. Aggiungere proprietà private e i rispettivi campi sottostanti per archiviare un riferimento alle implementazioni **IPushDemoNotificationActionService**, **INotificationRegistrationService** e **IDeviceInstallationService**.

    ```csharp
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
    ```

1. Aggiungere il metodo **RegisterForRemoteNotifications** per registrare le impostazioni delle notifiche utente e quindi per le notifiche remote con **APNS**.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Aggiungere il metodo **CompleteRegistrationAsync** per impostare il valore della proprietà `IDeviceInstallationService.Token`. Aggiornare la registrazione e memorizzare nella cache il token del dispositivo, se è stato aggiornato dall'ultima archiviazione.

    ```csharp
    Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();
        return NotificationRegistrationService.RefreshRegistrationAsync();
    }
    ```

1. Aggiungere il metodo **ProcessNotificationActions** per elaborare i dati di notifica **NSDictionary** e chiamare in modo condizionale **NotificationActionService.TriggerAction**.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Eseguire l'override del metodo **RegisteredForRemoteNotifications** passando l'argomento **deviceToken** al metodo **CompleteRegistrationAsync**.

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Eseguire l'override del metodo **ReceivedRemoteNotification** passando l'argomento **userInfo** al metodo **ProcessNotificationActions**.

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. Eseguire l'override del metodo **FailedToRegisterForRemoteNotifications** per registrare l'errore.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > In questo caso è un segnaposto. È opportuno implementare la registrazione e la gestione degli errori appropriate per gli scenari di produzione.

1. Aggiornare il metodo **FinishedLaunching** per chiamare `Bootstrap.Begin` subito dopo la chiamata a `Forms.Init` passando l'implementazione specifica della piattaforma di **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. Nello stesso metodo, richiedere l'autorizzazione in modo condizionale e registrarsi per le notifiche remote immediatamente dopo `Bootstrap.Begin`.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. Sempre in **FinishedLaunching**, chiamare **ProcessNotificationActions** subito dopo la chiamata a `LoadApplication` se l'argomento **options** contiene **UIApplication.LaunchOptionsRemoteNotificationKey** passando l'oggetto **userInfo** risultante.

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
