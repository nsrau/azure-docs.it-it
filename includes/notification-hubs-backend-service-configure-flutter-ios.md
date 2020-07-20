---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156329"
---
### <a name="configure-the-runner-target-and-infoplist"></a>Configurare la destinazione Runner e Info.plist

1. In **Visual Studio Code** premere **CTRL** + **clic** sulla cartella **ios**, quindi scegliere **Apri in Xcode**.

1. In **Xcode** fare clic su **Runner** (**xcodeproj** nella parte superiore, non la cartella), quindi selezionare la destinazione **Runner** e poi **Signing & Capabilities** (Firma e funzionalità). Con l'opzione **Tutto** selezionata per la configurazione di compilazione, scegliere l'account per sviluppatore per **Team**. Verificare quindi che l'opzione "Automatically manage signing" (Gestisci firma automaticamente) sia selezionata e che vengano automaticamente selezionati il certificato di firma e il profilo di provisioning.

    > [!NOTE]
    > Se il valore del nuovo profilo di provisioning non è visualizzato, provare ad aggiornare i profili di Signing Identity(Identità di firma) selezionando **Xcode** > **Preferences (Preferenze)**  > **Account** e infine selezionando il pulsante **Download Manual Profiles** (Scarica profili manuali) per scaricare i profili.

1. Fare clic su **+ Capability** (Funzionalità), quindi cercare **Notifiche push**. **Fare doppio clic su** **Notifiche push** per aggiungere questa funzionalità.

1. Aprire **Info.plist** e impostare **Versione minima del sistema** su **13.0**.

    > [!NOTE]
    > Ai fini di questa esercitazione sono supportati solo i dispositivi che eseguono **iOS 13.0 e versioni successive**, tuttavia è possibile estendere il supporto ai dispositivi che eseguono versioni precedenti.

1. Aprire **Runner.entitlements** e verificare che l'opzione **Ambiente APS** sia impostata su **Sviluppo**.

### <a name="handle-push-notifications-for-ios"></a>Gestire le notifiche push per iOS

1. Premere **CTRL** + **clic** sulla cartella **Runner** all'interno del progetto Runner, quindi scegliere **Nuovo gruppo** usando **Services** come nome.

1. Premere **CTRL** + **clic** sulla cartella **Services**, quindi scegliere **Nuovo file**. Scegliere quindi **File Swift** e fare clic su **Avanti**. Specificare **DeviceInstallationService** per il nome e quindi fare clic su **Crea**.

1. Implementare **DeviceInstallationService.swift** usando il codice seguente.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Questa classe implementa la controparte specifica della piattaforma per il canale `com.<your_organization>.pushdemo/deviceinstallation`. È stata definita nella parte Flutter dell'app all'interno di **DeviceInstallationService.dart**. In questo caso, le chiamate vengono effettuate dal codice comune all'host nativo. Assicurarsi di sostituire **<your_organization>** con il nome della propria organizzazione ogni volta viene usato.
    >
    > Questa classe fornisce un ID univoco (con il valore [UIDevice.identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor)) come parte del payload di registrazione dell'hub di notifica.

1. Aggiungere alla cartella **Services** un altro **file Swift** denominato *NotificationRegistrationService*, quindi aggiungere il codice seguente.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Questa classe implementa la controparte specifica della piattaforma per il canale `com.<your_organization>.pushdemo/notificationregistration`. È stata definita nella parte Flutter dell'app all'interno di **NotificationRegistrationService.dart**. In questo caso, le chiamate vengono effettuate dall'host nativo al codice comune. Assicurarsi anche in questo caso di sostituire **<your_organization>** con il nome della propria organizzazione ogni volta viene usato.

1. Aggiungere alla cartella **Services** un altro **file Swift** denominato *NotificationActionService*, quindi aggiungere il codice seguente.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Questa classe implementa la controparte specifica della piattaforma per il canale `com.<your_organization>.pushdemo/notificationaction`. È stata definita nella parte Flutter dell'app all'interno di **NotificationActionService.dart**. In questo caso le chiamate possono essere effettuate in entrambe le direzioni. Assicurarsi di sostituire **<your_organization>** con il nome della propria organizzazione ogni volta viene usato.

1. In **AppDelegate.swift** aggiungere le variabili per archiviare un riferimento ai servizi creati in precedenza.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Aggiungere una funzione denominata **processNotificationActions** per l'elaborazione dei dati di notifica. Attivare in modo condizionale l'azione o archiviarla per un uso successivo se viene elaborata durante l'avvio dell'app.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Eseguire l'override della funzione **didRegisterForRemoteNotificationsWithDeviceToken** impostando il valore del **token** per **DeviceInstallationService**. Chiamare quindi **refreshRegistration** in **NotificationRegistrationService**.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Eseguire l'override della funzione **didReceiveRemoteNotification** passando l'argomento **userInfo** alla funzione **processNotificationActions**.

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. Eseguire l'override della funzione **didFailToRegisterForRemoteNotificationsWithError** per registrare l'errore.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > In questo caso è un segnaposto. È opportuno implementare la registrazione e la gestione degli errori appropriate per gli scenari di produzione.

1. In **didFinishLaunchingWithOptions** creare un'istanza delle variabili **deviceInstallationService**, **notificationRegistrationService** e **notificationActionService**.

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. Nella stessa funzione richiedere l'autorizzazione in modo condizionale e registrarsi per le notifiche remote.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Se **launchOptions** contiene la chiave **remoteNotification**, chiamare **processNotificationActions** alla fine della funzione **didFinishLaunchingWithOptions**. Passare l'oggetto **userInfo** risultante e usare *true* per l'argomento *launchAction*. Un valore *true* indica che l'azione verrà elaborata durante l'avvio dell'app.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
