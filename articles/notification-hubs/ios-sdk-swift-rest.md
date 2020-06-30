---
title: Inviare notifiche push alle app iOS Swift con Hub di notifica di Azure e le API REST
description: In questa esercitazione si apprenderà come usare Hub di notifica di Azure e le API REST per inviare notifiche push a dispositivi iOS.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127264"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Esercitazione: Inviare notifiche push alle app iOS Swift con le API REST di Hub di notifica

Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'app iOS usando le API REST.

Questa esercitazione illustra i passaggi seguenti:

- Creare un'app iOS di esempio.
- Connettere l'app iOS a Hub di notifica di Azure.
- Inviare notifiche push di prova.
- Verificare che l'app riceva le notifiche.

Il codice completo per questa esercitazione può essere scaricato da [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Un Mac che esegue [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), insieme a un certificato dello sviluppatore valido installato nel portachiavi.

- Un iPhone o iPad con iOS versione 10 o successiva.

- Il dispositivo fisico registrato nel [portale Apple](https://developer.apple.com/) e associato al certificato.

Prima di procedere è importante seguire l'esercitazione precedente che spiega come iniziare a usare [Hub di notifica di Azure per le app iOS](https://go.microsoft.com/fwlink/?linkid=2129801) per configurare le credenziali push nell'hub di notifica. Anche se non si ha alcuna esperienza con lo sviluppo di app iOS, si dovrebbe essere in grado di seguire questa procedura.

> [!NOTE]
> Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push in un dispositivo iOS fisico (iPhone o iPad), anziché in un emulatore iOS.

Nelle sezioni seguenti si compilerà un'app iOS che si connette all'hub di notifica.

## <a name="create-an-ios-project"></a>Creare un progetto iOS

1. In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application** (App visualizzazione singola).

2. Quando si impostano le opzioni per il nuovo progetto:
   - Specificare il **nome prodotto** (PushDemo) e l' **identificatore dell'organizzazione** (`com.<organization>`) usati per impostare l'**identificatore del bundle** nel portale Apple Developer.
   - Scegliere il  **Team** per cui è stato configurato l' **ID app**.
   - Impostare il **linguaggio** su **Swift**.
   - Selezionare  **Avanti**.

3. Creare una nuova cartella denominata **SupportingFiles**.

4. Creare un nuovo file p-list denominato **devsettings.plist** nella cartella **SupportingFiles**. Assicurarsi di aggiungere questa cartella al file **gitignore** in modo che non ne venga eseguito il commit quando si lavora con un repository Git. In un'app di produzione si potrebbero impostare in modo condizionale questi segreti nell'ambito di un processo di compilazione automatizzato. Queste impostazioni non sono illustrate in questa esercitazione.

5. Aggiornare **devsettings.plist** per includere le voci di configurazione seguenti usando i valori dell'hub di notifica di cui è stato effettuato il provisioning:

   | **Chiave**                  | **Tipo** | **Valore**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | string   | `<hubKey>`       |
   | notificationHubKeyName   | string   | `<hubKeyName>`   |
   | notificationHubName      | string   | `<hubName>`      |
   | notificationHubNamespace | string   | `<hubNamespace>` |

   Per trovare i valori necessari, passare alla risorsa Hub di notifica nel portale di Azure. In particolare, i valori **notificationHubName** e **notificationHubNamespace** si trovano nell'angolo superiore destro del riepilogo **Informazioni di base** nella pagina **Panoramica**.

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Valori richiesti":::

   È anche possibile trovare i valori **notificationHubKeyName** e **notificationHubKey** passando a **Criteri di accesso** e selezionando il rispettivo **criterio di accesso**, ad esempio **DefaultFullSharedAccessSignature**. Copiare quindi da **Primary Connection String** (Stringa di connessione primaria)il valore con prefisso `SharedAccessKeyName=` per **notificationHubKeyName** e il valore con prefisso `SharedAccessKey=` per **notificationHubKey**. La stringa di connessione dovrebbe avere il formato seguente:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Per semplicità, specificare **DefaultFullSharedAccessSignature** in modo da poter usare il token per inviare le notifiche. In pratica,  **DefaultListenSharedAccessSignature** è una scelta migliore per le situazioni in cui si vuole solo ricevere le notifiche.

6. In **Project Navigator** (Esplorazione progetto) selezionare il **nome progetto** e quindi selezionare la scheda **General** (Generale).

7. Trovare **Identity** (Identità) e impostare il valore di **Bundle Identifier** (Identificatore del bundle) in modo che corrisponda a `com.<organization>.PushDemo`, ossia il valore usato per **ID app** in un passaggio precedente.

8. Trovare **Signing & Capabilities** (Firma e funzionalità) e quindi selezionare il **Team** appropriato per il proprio **Account Apple Developer**. Il valore di **Team** deve corrispondere al team in cui sono stati creati i certificati e i profili.

9. Xcode dovrebbe scaricare automaticamente il **profilo di provisioning** appropriato in base al valore di **Bundle Identifier** (Identificatore del bundle). Se il nuovo  **profilo di provisioning** non è visualizzato, provare ad aggiornare i profili di **Signing Identity** (Identità di firma) selezionando **Xcode**, quindi **Preferences** (Preferenze), **Account** e infine selezionando il pulsante **Download Manual Profiles** (Scarica profili manuali) per scaricare i profili.

10. Sempre nella scheda **Signing & Capabilities** toccare il pulsante **+ Capability** (Funzionalità) ed effettuare un doppio tocco su **Push Notifications** (Notifiche push) nell'elenco per assicurarsi che le **notifiche push** siano abilitate.

11. Aprire il file **AppDelegate.swift** per implementare il protocollo **UNUserNotificationCenterDelegate** e aggiungere il codice seguente all'inizio della classe:

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

       ...

    }
    ```

    Questi membri verranno usati più avanti. In particolare, si userà il membro **tag** nell'ambito della registrazione usando un **modello personalizzato**. Per altre informazioni sui tag, vedere [Tag per le registrazioni](notification-hubs-tags-segment-push-message.md) e [Registrazioni modello](notification-hubs-templates-cross-platform-push-messages.md).

12. Nello stesso file aggiungere le righe di codice seguenti alla funzione **didFinishLaunchingWithOptions**:

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

    Questo codice recupera le impostazioni da **devsettings.plist**, imposta la classe **AppDelegate** come delegato **UNUserNotificationCenter** e quindi richiede l'autorizzazione per le notifiche push e chiama **registerForRemoteNotifications**.

    Per semplicità, il codice supporta solo iOS 10 e versioni successive. È possibile aggiungere il supporto per le versioni precedenti di iOS usando in modo condizionale le rispettive API e i relativi approcci, come si farebbe normalmente.

13. Nello stesso file aggiungere il codice seguente:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Questo codice usa i valori di **installationId** e **pushChannel** per effettuare la registrazione con l'hub di notifica. In questo caso si sta usando **UIDevice.current.identifierForVendor** per fornire un valore univoco per identificare il dispositivo e si sta formattando **deviceToken** per fornire il valore **pushChannel** desiderato. La funzione **showAlert** esiste semplicemente per visualizzare dei messaggi a scopo dimostrativo.

14. Sempre nel file **AppDelegate.swift** aggiungere le funzioni **willPresent** e **didReceive** a **UNUserNotificationCenterDelegate**. Queste funzioni visualizzano un avviso quando ricevono una notifica che informa che un'app è in esecuzione in primo piano o in background.

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

15. Aggiungere istruzioni `print` alla fine della funzione **didRegisterForRemoteNotificationsWithDeviceToken** per verificare che a **installationId** e **pushChannel** vengano assegnati valori:

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Creare le cartelle **Models**, **Services** e **Utilities** per i componenti di base che verranno aggiunti al progetto più tardi.

17. Verificare che il progetto venga compilato ed eseguito in un dispositivo fisico. Le notifiche push non possono essere testate con il simulatore.

## <a name="create-models"></a>Creare modelli

In questo passaggio si crea un set di modelli per rappresentare i payload dell' [API REST di Hub di notifica](/rest/api/notificationhubs/) e per archiviare i dati dei token di firma di accesso condiviso necessari.

1. Aggiungere un nuovo file Swift denominato **PushTemplate.swift** alla cartella **Models**. Questo modello definisce una struttura che rappresenta il **CORPO** di un singolo modello come parte del payload **DeviceInstallation**.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Aggiungere un nuovo file Swift denominato **DeviceInstallation.swift** alla cartella **Models**. Questo file definisce una struttura che rappresenta il payload per la creazione o l'aggiornamento di una  **installazione del dispositivo**. Aggiungere il codice seguente al file:

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

3. Aggiungere un nuovo file Swift denominato **TokenData.swift** alla cartella **Models**. Questo modello viene usato per archiviare un token di firma di accesso condiviso insieme alla relativa scadenza. Aggiungere il codice seguente al file:

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

## <a name="generate-a-sas-token"></a>Generare un token di firma di accesso condiviso

Hub di notifica usa la stessa infrastruttura di sicurezza del bus di servizio di Azure. Per chiamare l'API REST, [generare a livello di codice un token di firma di accesso condiviso](/rest/api/eventhub/generate-sas-token) che può essere usato nell'intestazione **Authorization** della richiesta.

Il token risultante sarà nel formato seguente:

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

Il processo in sé è costituito dagli stessi sei passaggi:

1. Calcolare la scadenza nel formato [ora del periodo UNIX](https://en.wikipedia.org/wiki/Unix_time) che indica il numero di secondi trascorsi dalla mezzanotte del 1° gennaio 1970 ora UTC (Coordinated Universal Time).

2. Formattare il **ResourceUrl** che rappresenta la risorsa a cui si sta cercando di accedere in modo che sia codificato in percentuale e in minuscolo. Il **ResourceUrl** è in formato `https://<namespace>.servicebus.windows.net/<hubName>`.

3. Preparare **StringToSign**, che è in formato `<UrlEncodedResourceUrl>\n<ExpiryEpoch>`.

4. Calcolare e applicare la codifica Base64 alla **firma** usando l'hash HMAC-SHA256 del valore **StringToSign**. Il valore dell'hash viene usato con la parte **chiave** della **stringa di connessione** per la rispettiva **regola di autorizzazione**.

5. Formattare la **firma** con codifica Base64 in modo che sia codificata in percentuale.

6. Costruire il token nel formato previsto usando i valori **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName** e **UrlEncodedResourceUrl**.

Vedere la [documentazione del bus di servizio di Azure](../service-bus-messaging/service-bus-sas.md) per una panoramica più completa sulle firme di accesso condiviso e su come vengono usate dal bus di servizio e dagli hub di notifica di Azure.

Ai fini di questo esempio Swift, si usa la libreria open source **CommonCrypto** di Apple per semplificare l'hashing della firma. Essendo una libreria C, non è accessibile in Swift senza modifiche. È possibile renderla disponibile usando un'intestazione provvisoria.

Per aggiungere e configurare l'intestazione provvisoria:

1. In Xcode selezionare **File**, quindi **New** (Nuovo), quindi **File** e infine selezionare **Header File** (File di intestazione). Assegnare al file di intestazione il nome **BridgingHeader.h**.

2. Modificare il file per importare **CommonHMAC.h**:

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Aggiornare le **impostazioni di compilazione** di destinazione in modo che facciano riferimento all'intestazione provvisoria:

   1. Selezionare il progetto **PushDemo** e scorrere verso il basso fino alla sezione **Swift Compiler** (Compilatore Swift).

   2. Verificare che l'opzione **Install Objective-C Compatibility Header** (Installa intestazione di compatibilità Objective-C) sia impostata su **Yes** (Sì).

   3. Immettere il percorso del file `<ProjectName>/BridgingHeader.h` nell'opzione **Objective-C bridging Header** (Intestazione provvisoria Objective-C). È il percorso del file dell'intestazione provvisoria.

   Se non si trovano queste opzioni, verificare che sia selezionata la visualizzazione **All** (Tutto) invece di **Basic** (Di base) o **Customized** (Personalizzata).

   Sono disponibili molte librerie di wrapper open source di terze parti che potrebbero rendere l'uso di **CommonCrypto** un po' più semplice. La descrizione di queste librerie esula però dall'ambito di questo articolo.

4. Aggiungere un nuovo file Swift denominato **TokenUtility.swift** nella cartella **Utilities** e quindi aggiungere il codice seguente:

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

   Questa utilità incapsula la logica responsabile della generazione del token di firma di accesso condiviso.

   Come descritto in precedenza, la funzione **getSasToken** orchestra i passaggi generali necessari per preparare il token. La funzione verrà chiamata dal servizio di installazione più avanti nel corso dell'esercitazione.

   Le altre due funzioni vengono chiamate dalla funzione **getSasToken** : **sha256HMac** per il calcolo della firma e **urlEncodedString** per la codifica della stringa dell'URL associata. La funzione **urlEncodedString** è obbligatoria, in quanto non è possibile ottenere l'output necessario usando la funzione predefinita **addingPercentEncoding**.

    [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) è un ottimo esempio di come affrontare queste operazioni in Objective-C. Altre informazioni sui token di firma di accesso condiviso del bus di servizio di Azure sono disponibili nella [documentazione del bus di servizio di Azure](../service-bus-messaging/service-bus-sas.md).

5. In **AppDelegate.swift**aggiungere il codice seguente alla funzione `didRegisterForRemoteNotificationsWithDeviceToken` per verificare che **TokenUtility.getSasToken** generi un token valido.

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Assicurarsi di sostituire i segnaposto nella stringa **baseAddress** con valori personalizzati.

## <a name="verify-the-sas-token"></a>Verificare il token di firma di accesso condiviso

Prima di implementare il servizio di installazione nel client, verificare che l'app generi correttamente il token di firma di accesso condiviso usando un'utilità HTTP. Ai fini di questa esercitazione, lo strumento scelto è **Postman**.

Prendere nota dei valori di **installationId** e **token** generati dall'app.

Per chiamare l'API di **installazione**, seguire questa procedura:

1. In **Postman** aprire una nuova scheda.
2. Impostare la richiesta su **GET** e specificare l'indirizzo seguente:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Configurare le intestazioni della richiesta nel modo seguente:

   | **Chiave**       | **Valore**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorizzazione | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Selezionare il pulsante **Code** (Codice) visualizzato sotto il pulsante **Save** (Salva) in alto a destra. Il codice della richiesta dovrebbe essere simile all'esempio seguente:

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Selezionare il pulsante **Send** (Invia).

A questo punto per il valore **installationId** specificato non esiste alcuna registrazione. La verifica dovrebbe restituire una risposta "404 Non trovato" invece di una risposta "401 Non autorizzato". Questo risultato dovrebbe confermare che il token di firma di accesso condiviso è stato accettato.

## <a name="implement-the-installation-service-class"></a>Implementare la classe del servizio di installazione

A questo punto occorre implementare il wrapper di base per l' [API REST di installazione](/rest/api/notificationhubs/create-overwrite-installation).

Aggiungere un nuovo file Swift denominato **NotificationRegistrationService.swift** nella cartella **Services** e quindi aggiungere il codice seguente al file:

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
    private var tokenExpiryDate : Date? = nil

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
                        completion(err == nil &&
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {

            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)

            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
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

I dettagli dei requisiti sono forniti come parte dell'inizializzazione. I tag e i modelli vengono passati facoltativamente nella funzione **register** per formare parte del payload JSON di **installazione del dispositivo**.

La funzione **register** chiama le altre funzioni private per preparare la richiesta. Dopo la ricezione di una risposta, viene chiamato il completamento che indica se la registrazione è stata eseguita correttamente.

L'endpoint della richiesta viene costruito dalla funzione **getBaseAddress**. La costruzione usa i parametri dell'hub di notifica *namespace* e *name* forniti durante l'inizializzazione.

La funzione **getSasToken** controlla se il token attualmente archiviato è valido. Se il token non è valido, la funzione chiama **TokenUtility** per generare un nuovo token e quindi lo archivia prima di restituire un valore.

Infine, **encodeToJson** converte i rispettivi oggetti modello in codice JSON da usare come parte del corpo della richiesta.

## <a name="invoke-the-notification-hubs-rest-api"></a>Richiamare l'API REST di Hub di notifica

L'ultimo passaggio consiste nell'aggiornare **AppDelegate** in modo da usare **NotificationRegistrationService** per effettuare la registrazione con **NotificationHub**.

1. Aprire **AppDelegate.swift** e aggiungere variabili a livello di classe per archiviare un riferimento a **NoficiationRegistrationService** e al **PushTemplate** generico:

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. Nello stesso file aggiornare la funzione **didRegisterForRemoteNotificationsWithDeviceToken** per inizializzare **NotificationRegistrationService** con i parametri necessari, quindi chiamare la funzione **register**.

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

   Per semplicità, il codice usa istruzioni `print` per aggiornare la finestra di output con il risultato dell'operazione **register**.

3. Compilare ed eseguire l'app in un dispositivo fisico. Nella finestra di output dovrebbe essere visualizzato **Registered** (Registrato).

## <a name="test-the-solution"></a>Testare la soluzione

A questo punto l'app è registrata con **NotificationHub** e può ricevere notifiche push. In Xcode arrestare il debugger e chiudere l'app, se è in esecuzione. Controllare quindi che i dettagli di **installazione del dispositivo** siano visualizzati come previsto e che l'app possa ora ricevere notifiche push.

### <a name="verify-the-device-installation"></a>Verificare l'installazione del dispositivo

È ora possibile effettuare la stessa richiesta di prima, usando **Postman** per la [verifica del token di firma di accesso condiviso](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token). Supponendo che il token di firma di accesso condiviso non sia scaduto, la risposta dovrebbe ora includere i dettagli di installazione forniti, ad esempio i modelli e i tag.

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

Se il token di firma di accesso condiviso precedente è scaduto, è possibile aggiungere un punto di interruzione alla riga 24 della classe **TokenUtility**  per ottenere un nuovo token di firma di accesso condiviso e aggiornare l'intestazione **Authorization** con il nuovo valore.

### <a name="send-a-test-notification-azure-portal"></a>Inviare una notifica di prova (portale di Azure)

Il modo più rapido per verificare di poter ricevere le notifiche consiste nell'accedere all'hub di notifica nel portale di Azure:

1. Nel portale di Azure passare alla scheda **Panoramica** nell'hub di notifica.

2. Selezionare **Invio di prova**, che si trova sopra il riepilogo **Informazioni di base** nell'angolo superiore sinistro della finestra del portale:

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Opzione Invio di prova nel riepilogo Informazioni di base di Hub di notifica":::

3. Scegliere **Modello personalizzato** nell'elenco **Piattaforme**.

4. Immettere **12345** per **Invia a espressione tag**. Questo tag è stato specificato in precedenza nell'installazione.

5. Facoltativamente, modificare il **messaggio** nel payload JSON:

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Invio di prova in Hub di notifica":::

6. Selezionare **Invia**. Il portale dovrebbe indicare se la notifica è stata inviata correttamente al dispositivo:

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Risultato dell'invio di prova":::

   Supponendo che l'app non sia in esecuzione in primo piano, si dovrebbe anche vedere una notifica nel **Centro notifiche** nel dispositivo. Toccando la notifica viene aperta l'app in cui è visualizzato l'avviso.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Notifica di prova":::

### <a name="send-a-test-notification-mail-carrier"></a>Inviare una notifica di prova (vettore di posta)

È possibile inviare le notifiche tramite l' [API REST](/rest/api/notificationhubs/) usando **Postman**, che può essere un modo più pratico per eseguire il test.

1. Aprire una nuova scheda in **Postman**.

2. Impostare la richiesta su **POST** e immettere l'indirizzo seguente:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Configurare le intestazioni della richiesta nel modo seguente:

   | Chiave                           | valore                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | application/json;charset=utf-8 |
   | Autorizzazione                 | \<sasToken\>                   |
   | ServiceBusNotification-Format | template                       |
   | Tag                          | "12345"                        |

4. Configurare il **CORPO** della richiesta in modo che usi **JSON non elaborato (application.json)**  con il payload JSON seguente:

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Selezionare il pulsante **Code** (Codice) sotto il pulsante **Save** (Salva) in alto a destra nella finestra. Il codice della richiesta dovrebbe essere simile all'esempio seguente:

   ```xml
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

6. Selezionare il pulsante **Send** (Invia).

Si dovrebbe ottenere un codice di stato **201 Creato** e ricevere la notifica sul dispositivo client.

## <a name="next-steps"></a>Passaggi successivi

Si dispone ora di un'app iOS Swift di base connessa a un hub di notifica tramite l' [API REST di Hub di notifica](/rest/api/notificationhubs/) e in grado di inviare e ricevere notifiche. Per altre informazioni su come inviare notifiche a dispositivi specifici, passare all'esercitazione seguente:

- [Esercitazione: Inviare notifiche push a specifici dispositivi](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di Hub di notifica di Azure](notification-hubs-push-notification-overview.md)
- [API REST di Hub di notifica](/rest/api/notificationhubs/)
- [SDK di Hub di notifica per le operazioni back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [SDK di Hub di notifica su GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrazione con il back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Uso dei tag](notification-hubs-tags-segment-push-message.md)
- [Uso di modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
- [Controllo degli accessi del bus di servizio con firme di accesso condiviso](../service-bus-messaging/service-bus-sas.md)
- [Generare token di firma di accesso condiviso a livello di codice](/rest/api/eventhub/generate-sas-token)
- [Sicurezza di Apple: crittografia comune](https://developer.apple.com/security/)
- [Ora del periodo UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
