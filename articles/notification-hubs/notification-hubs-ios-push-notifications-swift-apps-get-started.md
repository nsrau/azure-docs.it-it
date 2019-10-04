---
title: Notifiche push a app iOS Swift che usano Hub di notifica di Azure | Microsoft Docs
description: Informazioni su come inviare notifiche push a app iOS Swift che usano Hub di notifica di Azure.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: b830538f81d1696c34db3e4f66a07346c17bcdcc
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211963"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Esercitazione: Notifiche push a app iOS Swift che usano l'API REST di hub di notifica

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

In questa esercitazione si usa hub di notifica di Azure per inviare notifiche push a un'applicazione iOS basata su Swift usando l' [API REST](/rest/api/notificationhubs/). Si crea anche un'app iOS vuota che riceve notifiche push tramite [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Questa esercitazione illustra i passaggi seguenti:

> [!div class="checklist"]
> * Generare il file di richiesta di firma del certificato.
> * Richiedere l'app per le notifiche push.
> * Creare un profilo di provisioning per l'app.
> * Creare un hub di notifica.
> * Configurare l'hub di notifica con le informazioni APNs.
> * Connettere l'app iOS a un hub di notifica.
> * Testare la soluzione.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura, è necessario:

- Per esaminare la [Panoramica di hub di notifica di Azure](notification-hubs-push-notification-overview.md) se non si ha familiarità con il servizio.
- Per informazioni sulle [registrazioni e sull'installazione](notification-hubs-push-notification-registration-management.md).
- Un [account Apple Developer](https://developer.apple.com)attivo.
- Un Mac che esegue Xcode insieme a un certificato sviluppatore valido installato nel keychain.
- Un dispositivo iPhone fisico che è possibile eseguire ed eseguire il debug con, perché non è possibile testare le notifiche push con il simulatore.
- Il dispositivo iPhone fisico registrato nel [portale Apple](https://developer.apple.com) e associato al certificato.
- Una [sottoscrizione di Azure](https://portal.azure.com) in cui è possibile creare e gestire le risorse.

Anche se non si ha esperienza con lo sviluppo di iOS, è possibile seguire la procedura per la creazione di questo esempio di principi iniziali. Tuttavia, si trarrà vantaggio dalla familiarità con i concetti seguenti:

- Creazione di app iOS con Xcode e Swift.
- Configurazione di un [Hub di notifica di Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) per iOS.
- Il [portale Apple Developer](https://developer.apple.com) e il [portale di Azure](https://portal.azure.com).

> [!NOTE]
> L'hub di notifica verrà configurato per l'uso della modalità di autenticazione **sandbox** . Non usare questa modalità di autenticazione per i carichi di lavoro di produzione.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Connettere l'app iOS a un hub di notifica

In questa sezione verrà compilata l'app iOS che si connetterà all'hub di notifica.  

### <a name="create-an-ios-project"></a>Creare un progetto iOS

1. In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application** .

1. Quando si impostano le opzioni per il nuovo progetto:

   1. Specificare il **nome del prodotto** (PushDemo) e l'identificatore`com.<organization>`dell' **organizzazione** () usati quando si imposta l' **identificatore del bundle** nel portale Apple Developer.

   1. Scegliere il **Team** per cui è stato configurato l' **ID app** .

   1. Impostare la **lingua** su **Swift**.

   1. Selezionare **Avanti**.

1. Creare una nuova cartella denominata **SupportingFiles**.

1. Creare un nuovo file di elenco p denominato **devsettings. plist** nella cartella **SupportingFiles** . Assicurarsi di aggiungere questa cartella al file **gitignore** in modo che non venga eseguito il commit quando si lavora con un repository git. In un'app di produzione, è probabile che i segreti vengano configurati in modo condizionale come parte di un processo di compilazione automatizzato. Queste impostazioni non sono descritte in questa procedura dettagliata.

1. Aggiornare **devsettings. plist** per includere le voci di configurazione seguenti usando valori personalizzati dall'hub di notifica di cui è stato effettuato il provisioning:

   | Chiave                            | Type                     | Value                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Stringa                   | \<hubKey>                  |
   | notificationHubKeyName         | Stringa                   | \<> hubKeyName              |
   | notificationHubName            | Stringa                   | \<hubName>                 |
   | notificationHubNamespace       | Stringa                   | \<hubNamespace>            |

   È possibile trovare i valori necessari passando alla risorsa Hub di notifica nella portale di Azure. In particolare, i valori **notificationHubName** e **notificationHubNamespace** si trovano nell'angolo superiore destro del riepilogo di **Essentials** all'interno della pagina **Overview** .

   ![Riepilogo Essentials di hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   È anche possibile trovare i valori **notificationHubKeyName** e **NotificationHubKey** passando a criteri di **accesso** e selezionando i rispettivi **criteri di accesso**, ad `DefaultFullSharedAccessSignature`esempio. Successivamente, copiare dalla stringa di **connessione primaria** `SharedAccessKeyName=` il valore preceduto da per `notificationHubKeyName` e `notificationHubKey`il valore con `SharedAccessKey=` prefisso per.

   Il formato della stringa di connessione deve essere il seguente:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Per mantenerlo semplice, `DefaultFullSharedAccessSignature` specificare in modo che sia possibile usare il token per inviare le notifiche. In pratica, `DefaultListenSharedAccessSignature` sarebbe una scelta migliore per le situazioni in cui si desidera ricevere solo le notifiche.

1. In **Project Navigator**selezionare il **nome del progetto** , quindi selezionare la scheda **generale** .

1. Trovare **Identity** , quindi impostare il valore dell' **identificatore del bundle** in modo `com.<organization>.PushDemo`che corrisponda a, che corrisponde al valore usato per l' **ID app** di un passaggio precedente.

1. Trovare la **firma**e quindi selezionare il **Team** appropriato per l' **account sviluppatore Apple**. Il valore del **Team** deve corrispondere a quello in cui sono stati creati i certificati e i profili.

1. Xcode dovrebbe estrarre automaticamente il valore appropriato del **profilo di provisioning** in base all' **identificatore del bundle**. Se non viene visualizzato il nuovo valore del **profilo di provisioning** , provare ad aggiornare i profili per l' **identità di firma** selezionando la visualizzazione**account** > **Preferenze** > di **Xcode** > . **Dettagli**. Selezionare **firma identità**, quindi fare clic sul pulsante **Aggiorna** in basso a destra per scaricare i profili.

1. Selezionare la scheda **funzionalità** e assicurarsi che le **notifiche push** siano abilitate.

1. Aprire il file **AppDelegate. Swift** per implementare il protocollo **UNUserNotificationCenterDelegate** e aggiungere il codice seguente all'inizio della classe:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    Questi membri verranno usati in un secondo momento. In particolare, i **tag** e i membri **genericTemplate** verranno usati come parte della registrazione. Per altre informazioni sui tag, vedere [tag per](notification-hubs-tags-segment-push-message.md) registrazioni e [registrazioni di modelli](notification-hubs-templates-cross-platform-push-messages.md).

1. Nello stesso file aggiungere il codice seguente alla funzione **didFinishLaunchingWithOptions** :

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Questo codice recupera i valori delle impostazioni da **devsettings. plist**, imposta la classe **AppDelegate** come delegato **UNUserNotificationCenter** , richiede l'autorizzazione per le notifiche push e quindi chiama  **registerForRemoteNotifications**.

    Per mantenerlo semplice, il codice supporta *solo iOS 10 e versioni successive*. È possibile aggiungere il supporto per le versioni precedenti del sistema operativo usando in modo condizionale le rispettive API e approcci come si farebbe normalmente.

1. Nello stesso file aggiungere le funzioni seguenti:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Il codice usa i valori **installationId** e **pushChannel** per la registrazione con l'hub di notifica. In questo caso, si usa **UIDevice. Current. identifierForVendor** per fornire un valore univoco per identificare il dispositivo e quindi formattare il **deviceToken** per fornire il valore **pushChannel** desiderato. La funzione **showAlert** esiste semplicemente per visualizzare il testo di un messaggio a scopo dimostrativo.

1. Sempre nel file **AppDelegate. Swift** aggiungere le funzioni **willPresent** e **didReceive** a **UNUserNotificationCenterDelegate**. Queste funzioni visualizzano un avviso quando ricevono una notifica che un'app è in esecuzione rispettivamente in primo piano o in background.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Aggiungere istruzioni Print alla fine della funzione **didRegisterForRemoteNotificationsWithDeviceToken** per verificare che **installationId** e **pushChannel** siano assegnati a valori.

1. Creare le cartelle **modelli**, **Servizi**e **utilità** per i componenti di base che verranno aggiunti al progetto in un secondo momento.

1. Verificare che il progetto venga compilato ed eseguito in un dispositivo fisico. Le notifiche push non possono essere testate tramite il simulatore.

### <a name="create-models"></a>Creazione di modelli

In questo passaggio verrà creato un set di modelli per rappresentare i payload dell' [API REST di hub di notifica](/rest/api/notificationhubs/) e per archiviare i dati del token di firma di accesso condiviso (SAS) necessari.

1. Aggiungere un nuovo file Swift denominato **PushTemplate. Swift** alla cartella **Models** . Questo modello fornisce uno struct che rappresenta il **corpo** di un singolo modello come parte del payload **DeviceInstallation** .

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Aggiungere un nuovo file Swift denominato **DeviceInstallation. Swift** alla cartella **Models** . Questo file definisce uno struct che rappresenta il payload per la creazione o l'aggiornamento di un' **installazione del dispositivo**. Aggiungere il codice seguente al file:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Aggiungere un nuovo file Swift denominato **TokenData. Swift** alla cartella **Models** . Questo modello verrà usato per archiviare un token SAS insieme alla relativa scadenza.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Generare un token SAS

Hub di notifica usa la stessa infrastruttura di sicurezza del bus di servizio di Azure. Per chiamare l'API REST, è necessario generare a [livello di codice un token](/rest/api/eventhub/generate-sas-token) di firma di accesso condiviso che può essere usato nell'intestazione dell' **autorizzazione** della richiesta.  

Il token risultante avrà il formato seguente:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Il processo stesso comporta le stesse sei fasi principali:  

1. Calcolo della scadenza nel formato di [data e ora UNIX](https://en.wikipedia.org/wiki/Unix_time) , che indica il numero di secondi trascorsi dalla mezzanotte UTC (Coordinated Universal Time) il 1 ° gennaio 1970.
1. Formattazione del **ResourceUrl** che rappresenta la risorsa a cui si sta tentando di accedere, in modo che sia codificato in percentuale e minuscolo. Il **ResourceUrl** ha il formato `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Preparazione di **StringToSign**, formattato come `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Calcolo e codifica Base64 della **firma** usando l'hash HMAC-SHA256 del valore **StringToSign** . Il valore hash viene utilizzato con la parte **chiave** della **stringa di connessione** per la rispettiva regola di **autorizzazione**.
1. Formattare la **firma** base64 codificata in modo che sia codificata in percentuale.
1. Creazione del token nel formato previsto usando i valori **UrlEncodedSignature**, **ExpiryEpoch**, **nome**e **UrlEncodedResourceUrl** .

Vedere la [documentazione del bus di servizio di Azure](../service-bus-messaging/service-bus-sas.md) per una panoramica più completa della firma di accesso condiviso e il modo in cui usare il bus di servizio e gli hub di notifica di Azure.

Ai fini di questo esempio Swift, si userà la libreria **CommonCrypto** open source di Apple per semplificare l'hashing della firma. Poiché si tratta di una libreria C, non è possibile accedervi in modo rapido. È possibile rendere disponibile la libreria usando un'intestazione bridging.

Per aggiungere e configurare l'intestazione bridging:

1. In Xcode selezionare **file** > file di**intestazione** **nuovo** >  file> . Denominare il file di intestazione **BridgingHeader. h**.

1. Modificare il file per importare **CommonHMAC. h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Aggiornare le impostazioni di **compilazione** della destinazione per fare riferimento all'intestazione bridging:

   1. Aprire la scheda **Impostazioni** di compilazione e scorrere verso il basso fino alla sezione **Swift Compiler** .

   1. Verificare che l'opzione  **Installa intestazione compatibilità Objective-C**sia impostata su **Sì**.

   1. Immettere il percorso `'<ProjectName>/BridgingHeader.h'` del file nell'opzione relativa all' **intestazione** bridging Objective-C. Si tratta del percorso del file per l'intestazione bridging.

   Se queste opzioni non sono disponibili, assicurarsi che sia selezionata la vista **tutti** anziché **Basic** o **personalizzata**.

   Sono disponibili molte librerie di wrapper open source di terze parti che potrebbero semplificare l'uso di **CommonCrypto** . Tuttavia, la discussione di tali librerie esula dall'ambito di questo articolo.

1. Aggiungere un nuovo file Swift denominato **TokenUtility. Swift** nella cartella **Utilities** e aggiungere il codice seguente:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Questa utilità incapsula la logica responsabile della generazione del token SAS.

   Come descritto in precedenza, la funzione **Metodo getsastoken** orchestra i passaggi di alto livello necessari per preparare il token. La funzione verrà chiamata dal servizio di installazione più avanti in questa esercitazione.

   Le altre due funzioni vengono chiamate dalla funzione **Metodo getsastoken** : **sha256HMac** per il calcolo della firma e **urlEncodedString** per la codifica della stringa URL associata. La funzione **urlEncodedString** è obbligatoria perché non è possibile ottenere l'output necessario tramite la funzione **addingPercentEncoding** incorporata.

   [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) è un ottimo esempio di come affrontare queste operazioni in Objective-C. Altre informazioni sui token SAS del bus di servizio di Azure sono disponibili nella [documentazione del bus di servizio di Azure](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Verificare il token SAS

Prima di implementare il servizio di installazione nel client, verificare che l'app stia generando correttamente il token di firma di accesso condiviso usando l'utilità HTTP scelta. Ai fini di questa esercitazione, lo strumento da scegliere sarà **postazione**.

Usare un'istruzione di stampa o un punto di interruzione appropriato per prendere nota dei valori di **installationId** e **token** generati dall'app.

Per chiamare l'API di **installazione** , seguire questa procedura:

1. In **postazione**aprire una nuova scheda.

1. Impostare la richiesta per **ottenere** e specificare l'indirizzo seguente:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configurare le intestazioni della richiesta nel modo seguente:

   | Chiave           | Value            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Authorization | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. Selezionare il pulsante **codice** visualizzato in alto a destra sotto il pulsante **Salva** . La richiesta dovrebbe essere simile all'esempio seguente:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Selezionare il **inviare** pulsante.

A questo punto non esiste alcuna registrazione per il **installationId** specificato. La verifica dovrebbe generare una risposta "404 non trovata" anziché una risposta "401 non autorizzato". Questo risultato dovrebbe confermare che il token SAS è stato accettato.

### <a name="implement-the-installation-service-class"></a>Implementare la classe del servizio di installazione

Verrà quindi implementato il wrapper di base per l' [API REST di installazione](/rest/api/notificationhubs/create-overwrite-installation).  

Aggiungere un nuovo file Swift denominato **NotificationRegistrationService. Swift** nella cartella **Servizi** , quindi aggiungere il codice seguente al file:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

I dettagli del requisito sono forniti come parte dell'inizializzazione. I tag e i modelli vengono passati facoltativamente nella funzione **Register** per formare parte del payload JSON di **installazione del dispositivo** .  

La funzione **Register** chiama le altre funzioni private per preparare la richiesta. Dopo la ricezione di una risposta, il completamento viene chiamato e indica se la registrazione è stata eseguita correttamente.  

L'endpoint della richiesta viene creato dalla funzione **getBaseAddress** . La costruzione usa lo *spazio dei nomi* e il *nome* dei parametri dell'hub di notifica forniti durante l'inizializzazione.  

La funzione **Metodo getsastoken** controlla se il token attualmente archiviato è valido. Se il token non è valido, la funzione chiama **TokenUtility** per generare un nuovo token e quindi lo archivia prima di restituire un valore.

Infine, **encodeToJson** converte i rispettivi oggetti modello in JSON per l'uso come parte del corpo della richiesta.

### <a name="invoke-the-notification-hubs-rest-api"></a>Richiamare l'API REST di hub di notifica

L'ultimo passaggio consiste nell'aggiornamento di **AppDelegate** per l'uso di **NotificationRegistrationService** per la registrazione con **NotificationHub**.

1. Aprire **AppDelegate. Swift** e aggiungere una variabile a livello di classe per archiviare un riferimento a **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. Nello stesso file aggiornare la funzione **didRegisterForRemoteNotificationsWithDeviceToken** per inizializzare il **NotificationRegistrationService** con i parametri necessari e quindi chiamare la funzione **Register** .

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Per semplificare l'operazione, si utilizzeranno due istruzioni Print per aggiornare la finestra di output con il risultato dell'operazione di **registrazione** .

1. A questo punto, compilare ed eseguire l'app in un dispositivo fisico. Nella finestra di output dovrebbe essere visualizzato "registrato".

## <a name="test-the-solution"></a>Testare la soluzione

L'app in questa fase è registrata con **NotificationHub** e può ricevere notifiche push. In Xcode arrestare il debugger e chiudere l'app se è attualmente in esecuzione. Controllare quindi che i dettagli di **installazione del dispositivo** siano quelli previsti e che l'app possa ora ricevere notifiche push.  

### <a name="verify-the-device-installation"></a>Verificare l'installazione del dispositivo

È ora possibile effettuare la stessa richiesta, come in precedenza, usando il **post** per [verificare il token](#verify-the-sas-token)di firma di accesso condiviso. Supponendo che il token di firma di accesso condiviso non sia scaduto, la risposta dovrebbe ora includere i dettagli di installazione forniti, ad esempio i modelli e i tag.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>Invia una notifica di prova (portale di Azure)

Il modo più rapido per verificare che ora è possibile ricevere le notifiche consiste nell'accedere all'hub di notifica nel portale di Azure:

1. Nella portale di Azure passare alla scheda **Panoramica** nell'hub di notifica.

1. Selezionare **test Send**, che si trova al di sopra del riepilogo di **Essentials** in alto a sinistra nella finestra del portale:

    ![Pulsante di invio test di riepilogo Essentials di hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Scegliere **modello personalizzato** dall'elenco **piattaforme** .

1. Immettere **12345** per l' **espressione Invia a tag**. Questo tag è stato precedentemente specificato nell'installazione.

1. Facoltativamente, modificare il **messaggio** nel payload JSON:

    ![Invio di test di hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selezionare **Send** (Invia). Il portale deve indicare se la notifica è stata inviata correttamente al dispositivo:

    ![Risultati di invio test di hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Supponendo che l'app non sia in esecuzione in primo piano, dovrebbe essere visualizzata una notifica anche nel **Centro notifiche** del dispositivo. Toccare la notifica per aprire l'app e visualizzare l'avviso.

    ![Esempio di ricezione di notifiche](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Invia una notifica di prova (vettore di posta elettronica)

È possibile inviare le notifiche tramite l' [API REST](/rest/api/notificationhubs/) usando il **post**, che può essere un modo più pratico per eseguire il test.

1. Aprire una nuova scheda in **postazione**.

1. Impostare la richiesta su **post**e immettere l'indirizzo seguente:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configurare le intestazioni della richiesta nel modo seguente:

   | Chiave                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Authorization                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | modello                       |
   | Tag                           | "12345"                        |

1. Configurare il **corpo** della richiesta per l'uso del **formato JSON non elaborato (Application. Json)** con il payload JSON seguente:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selezionare il pulsante **codice** , che si trova sotto il pulsante **Salva** nella parte superiore destra della finestra. La richiesta dovrebbe essere simile all'esempio seguente:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Selezionare il **inviare** pulsante.

Si dovrebbe ottenere un codice di stato di esito positivo e ricevere la notifica sul dispositivo client.

## <a name="next-steps"></a>Passaggi successivi
È ora disponibile un'app iOS Swift di base connessa a un hub di notifica tramite l' [API REST](/rest/api/notificationhubs/) e può inviare e ricevere notifiche. Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di hub di notifica di Azure](notification-hubs-push-notification-overview.md)
- [API REST di Hub di notifica](/rest/api/notificationhubs/)
- [SDK di hub di notifica per le operazioni di back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [SDK di hub di notifica su GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registra con il back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Uso dei tag](notification-hubs-tags-segment-push-message.md) 
- [Utilizzo di modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
- [Controllo di accesso del bus di servizio con firme di accesso condiviso](../service-bus-messaging/service-bus-sas.md)
- [Generare token SAS a livello di codice](/rest/api/eventhub/generate-sas-token)
- [Sicurezza di Apple: crittografia comune](https://developer.apple.com/security/)
- [Ora UNIX Epoch](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
