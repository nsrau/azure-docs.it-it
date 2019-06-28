---
title: Le notifiche push alle App iOS Swift che usano hub di notifica di Azure | Microsoft Docs
description: Informazioni su come le notifiche push alle App iOS Swift che usano hub di notifica di Azure.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: c35044918876b2c7710e26f6b868bc1096c2f538
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340399"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Esercitazione: Le notifiche push alle App iOS Swift che usano l'API REST di hub di notifica

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

In questa esercitazione, si usa hub di notifica di Azure per inviare notifiche push a un'applicazione basata su codice Swift iOS usando il [API REST](/rest/api/notificationhubs/). È anche creare un'app iOS vuota che riceve notifiche push tramite il [Apple Push Notification service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Questa esercitazione illustra i passaggi seguenti:

> [!div class="checklist"]
> * Generare il file di richiesta di firma del certificato.
> * Richiedere l'app per le notifiche push.
> * Creare un profilo di provisioning per l'app.
> * Creare un hub di notifica.
> * Configurare l'hub di notifica con informazioni APNs.
> * Connettere l'app iOS a un hub di notifica.
> * Test della soluzione.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura, è necessario:

- Per scorrere [Panoramica di hub di notifica](notification-hubs-push-notification-overview.md) se non si ha familiarità con il servizio.
- Per apprendere [installazione e le registrazioni](notification-hubs-push-notification-registration-management.md).
- Un oggetto attivo [Account per sviluppatore Apple](https://developer.apple.com).
- Un Mac che esegue Xcode insieme a un certificato dello sviluppatore valido installato nel portachiavi.
- Un dispositivo fisico iPhone è possibile eseguire ed eseguire il debug, come è possibile testare le notifiche push con il simulatore.
- La registrazione del dispositivo fisico iPhone nel [portale Apple](https://developer.apple.com) e associato al certificato.
- Un' [sottoscrizione di Azure](https://portal.azure.com) in cui è possibile creare e gestire le risorse.

Anche se si ha alcuna esperienza precedente con lo sviluppo di iOS, sarà in grado di seguire i passaggi per la creazione di questo esempio first-principi. Tuttavia, è possibile sfruttare familiarità con i concetti seguenti:

- Creazione di App iOS con Xcode e Swift.
- Configurazione di un' [hub di notifica di Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) per iOS.
- Il [portale Apple Developer](https://developer.apple.com) e il [portale di Azure](https://portal.azure.com).

> [!NOTE]
> L'hub di notifica verrà configurato per usare la **Sandbox** solo modalità di autenticazione. Utilizzare questa modalità di autenticazione non per i carichi di lavoro di produzione.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Connettere l'app iOS a un hub di notifica

In questa sezione si creerà l'app per iOS che si connetterà all'hub di notifica.  

### <a name="create-an-ios-project"></a>Creare un progetto iOS

1. In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application** .

1. Quando si impostano le opzioni per il nuovo progetto:

   1. Specificare il **Product Name** (PushDemo) e **identificatore organizzazione** (`com.<organization>`) che è stato utilizzato quando si imposta **identificatore del Bundle** del portale per sviluppatori Apple.

   1. Scegliere il **Team** che il **App ID** è stato impostato per.

   1. Impostare il **language** al **Swift**.

   1. Selezionare **Avanti**.

1. Creare una nuova cartella denominata **SupportingFiles**.

1. Creare un nuovo file p-list chiamato **devsettings.plist** nel **SupportingFiles** cartella. Assicurarsi di aggiungere questa cartella per i **gitignore** file in modo che non è eseguito il commit quando si lavora con un repository git. In un'app di produzione, si sarebbero probabilmente in modo condizionale impostare questi segreti come parte di un processo di compilazione automatizzata. Tali impostazioni non sono trattati in questa procedura dettagliata.

1. Update **devsettings.plist** per includere le seguenti voci di configurazione usando i propri valori dall'hub di notifica che è stato effettuato il provisioning:

   | Chiave                            | Type                     | Value                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey>                  |
   | notificationHubKeyName         | string                   | \<hubKeyName>              |
   | notificationHubName            | String                   | \<hubName>                 |
   | notificationHubNamespace       | string                   | \<hubNamespace>            |

   È possibile trovare i valori richiesti, passare alla risorsa di hub di notifica nel portale di Azure. In particolare, il **notificationHubName** e **notificationHubNamespace** i valori sono nell'angolo superiore destro del **Essentials** riepilogo all'interno di **Cenni preliminari su** pagina.

   ![Riepilogo di Essentials di hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   È anche possibile trovare il **notificationHubKeyName** e **notificationHubKey** valori, passare a **criteri di accesso** e selezionando i rispettivi  **Criteri di accesso**, ad esempio `DefaultFullSharedAccessSignature`. Successivamente, copiare il **stringa di connessione primaria** il valore preceduto `SharedAccessKeyName=` per `notificationHubKeyName` e il valore preceduto `SharedAccessKey=` per il `notificationHubKey`.

   La stringa di connessione deve essere nel formato seguente:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   In sostanza, specificare `DefaultFullSharedAccessSignature` quindi è possibile usare il token per inviare notifiche. In pratica il `DefaultListenSharedAccessSignature` sarebbe una scelta migliore per le situazioni in cui si desidera solo ricevere le notifiche.

1. Sotto **Project Navigator**, selezionare la **nome del progetto** e quindi selezionare il **generali** scheda.

1. Trovare **Identity** e quindi impostare il **identificatore del Bundle** valore in modo che corrisponda al `com.<organization>.PushDemo`, che viene usato per il valore di **ID App** da un passaggio precedente.

1. Trovare **Signing**e quindi selezionare un valore appropriato **Team** per il **Account per sviluppatore Apple**. Il **Team** valore deve corrispondere a quella in cui è stato creato i profili e certificati.

1. Xcode recupererà automaticamente appropriato **profilo di Provisioning** valore base di **identificatore del Bundle**. Se non viene visualizzato il nuovo **profilo di Provisioning** di valore, provare ad aggiornare i profili per il **identità di firma** selezionando **Xcode**  >  **Preferenze** > **Account** > **visualizzare i dettagli**. Selezionare **identità di firma**, quindi selezionare il **aggiornare** pulsante nella parte inferiore destra per scaricare i profili.

1. Selezionare il **capacità** scheda e assicurarsi che **notifiche push** sono abilitati.

1. Aprire il **appdelegate. SWIFT** file per implementare il **UNUserNotificationCenterDelegate** protocol e aggiungere il codice seguente all'inizio della classe:

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

    Si useranno questi membri in un secondo momento. In particolare, si userà il **tag** e **genericTemplate** membri come parte della registrazione. Per altre informazioni sui tag, vedere [tag per le registrazioni](notification-hubs-tags-segment-push-message.md) e [registrazioni modello](notification-hubs-templates-cross-platform-push-messages.md).

1. Nello stesso file, aggiungere il codice seguente per il **didFinishLaunchingWithOptions** funzione:

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

    Questo codice recupera i valori delle impostazioni da **devsettings.plist**, imposta le **AppDelegate** di classi il **UNUserNotificationCenter** delegare, l'autorizzazione delle richieste per le notifiche push e quindi chiama **registerForRemoteNotifications**.

    In sostanza, il codice supporta *iOS 10 e versioni successive solo*. È possibile aggiungere il supporto per le versioni precedenti del sistema operativo in modo condizionale con le rispettive API e gli approcci come si farebbe normalmente.

1. Nello stesso file, aggiungere le funzioni seguenti:

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

    Il codice Usa il **installationId** e **pushChannel** valori da registrare con l'hub di notifica. In questo caso, si usa **UIDevice.current.identifierForVendor** per specificare un valore univoco per identificare il dispositivo e la formattazione quindi le **deviceToken** per fornire il valore desiderato  **pushChannel** valore. Il **showAlert** funzione disponibile è sufficiente per visualizzare testo del messaggio a scopo dimostrativo.

1. Sempre nella **appdelegate. SWIFT** , aggiungere il **willPresent** e **didReceive** funzioni ai **UNUserNotificationCenterDelegate**. Queste funzioni visualizzano un avviso quando si riceverà una notifica in un'app in esecuzione in foreground o background rispettivamente.

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

1. Aggiungere istruzioni print a fondo le **didRegisterForRemoteNotificationsWithDeviceToken** funzione per verificare che **installationId** e **pushChannel** sono in corso valori assegnati.

1. Creare il **modelli**, **Services**, e **utilità** cartelle per i componenti fondamentali a cui verranno aggiunti al progetto in un secondo momento.

1. Verificare che il progetto compilato ed eseguito in un dispositivo fisico. Non è possibile testare le notifiche push con il simulatore.

### <a name="create-models"></a>Creare modelli

In questo passaggio si creerà un set di modelli per rappresentare il [API REST di hub di notifica](/rest/api/notificationhubs/) payload e per archiviare l'accesso condiviso di firma di token dei dati.

1. Aggiungere un nuovo file Swift chiamato **PushTemplate.swift** per il **modelli** cartella. Questo modello fornisce un struct che rappresenta il **corpo** di un singolo modello come parte delle **DeviceInstallation** payload.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Aggiungere un nuovo file Swift chiamato **DeviceInstallation.swift** per il **modelli** cartella. Questo file definisce un tipo struct che rappresenta il payload per la creazione o l'aggiornamento di un **installazione dei dispositivi**. Aggiungere il codice seguente al file:

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

1. Aggiungere un nuovo file Swift chiamato **TokenData.swift** per il **modelli** cartella. Questo modello verrà usato per archiviare un token di firma di accesso condiviso con scadenza.

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

### <a name="generate-a-sas-token"></a>Generare un token di firma di accesso condiviso

Hub di notifica di usare la stessa infrastruttura di sicurezza come il Bus di servizio di Azure. Per chiamare l'API REST, dovrai [a livello di codice genera un token di firma di accesso condiviso](/rest/api/eventhub/generate-sas-token) che può essere utilizzato nel **autorizzazione** intestazione della richiesta.  

Il token risulta sarà nel formato seguente:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Il processo stesso prevede gli stessi sei passaggi principali:  

1. Calcolo della scadenza in [UNIX come valore Epoch time](https://en.wikipedia.org/wiki/Unix_time) formato, ovvero il numero di secondi trascorsi dalla mezzanotte ora UTC, 1 gennaio 1970.
1. Formattazione di **l'URL risorsa** che rappresenta la risorsa si sta provando ad accedere in modo che è codificato in percentuale e lettere minuscole. Il **l'URL risorsa** ha il formato `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Preparare il **StringToSign**, che viene formattato come `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Elaborazione e codifica base 64 di **firma** usando l'hash di HMAC-SHA256 delle **StringToSign** valore. Il valore hash viene usato con il **chiave** in parte il **stringa di connessione** per i rispettivi **regola di autorizzazione**.
1. Formattazione di con codificata base 64 **firma** pertanto è percento codificati.
1. Creare il token nel formato previsto utilizzando la **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, e **UrlEncodedResourceUrl** i valori.

Vedere le [documentazione di Azure Service Bus](../service-bus-messaging/service-bus-sas.md) per una panoramica più approfondita della firma di accesso condiviso e usarlo come il Bus di servizio di Azure e hub di notifica.

Ai fini di questo esempio Swift, si intende usare open source di Apple **CommonCrypto** libreria per facilitare l'hash della firma. Come è una libreria C, non è accessibile in Swift impostazione predefinita. È possibile rendere disponibile la libreria tramite un'intestazione provvisoria.

Per aggiungere e configurare l'intestazione provvisoria:

1. In Xcode, selezionare **File** > **New** > **File** > **File di intestazione**. Denominare il file di intestazione **BridgingHeader.h**.

1. Modificare il file da importare **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Aggiornare la destinazione **Build Settings** per fare riferimento l'intestazione provvisoria:

   1. Aprire il **delle impostazioni di compilazione** scheda e scorrere verso il basso il **Swift Compiler** sezione.

   1. Verificare che il **installare intestazione compatibilità Objective-C** opzione è impostata su **Sì**.

   1. Immettere il percorso del file `'<ProjectName>/BridgingHeader.h'` nella **Objective-C bridging intestazione** opzione. Questo è il percorso del file per l'intestazione provvisoria.

   Se non è possibile trovare queste opzioni, assicurarsi di avere il **tutti i** visualizzazione selezionata invece **base** o **personalizzato**.

   Sono disponibili molte librerie di wrapper open source di terze parti disponibili che potrebbero risultare usando **CommonCrypto** un po' più semplice. Tuttavia, discussione di tali librerie esula dall'ambito di questo articolo.

1. Aggiungere un nuovo file Swift denominato **TokenUtility.swift** all'interno di **utilità** cartella e aggiungere il codice seguente:

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

   Come descritto in precedenza, il **getSasToken** funzione Orchestra i passaggi generali necessari per preparare il token. La funzione verrà chiamata dal servizio di installazione più avanti in questa esercitazione.

   Le altre due funzioni vengono chiamate dal **getSasToken** funzione: **sha256HMac** per il calcolo della firma e **urlEncodedString** per la codifica URL associato stringa. Il **urlEncodedString** poiché non è possibile ottenere l'output richiesto tramite l'elemento predefinito, è necessaria la funzione **addingPercentEncoding** (funzione).

   Il [archiviazione di Azure SDK per iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) è un ottimo esempio di come implementare queste operazioni in Objective-C. Ulteriori informazioni sui token SAS del Bus di servizio Azure sono reperibile nel [documentazione di Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Verificare il token di firma di accesso condiviso

Prima di implementare il servizio di installazione del client, verificare che l'app viene generato correttamente il token di firma di accesso condiviso con l'utilità HTTP preferito. Ai fini di questa esercitazione, sarà nostro strumento di elezione **Postman**.

Usare un'istruzione di stampa posizionato in modo appropriato o un punto di interruzione notare il **installationId** e il **token** valori generati dall'app.

Seguire questa procedura per chiamare il **installazioni** API:

1. Nelle **Postman**, aprire una nuova scheda.

1. Impostare la richiesta **ottenere** e specificare l'indirizzo seguente:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configurare le intestazioni della richiesta come indicato di seguito:

   | Chiave           | Value            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Authorization | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. Selezionare il **codice** pulsante visualizzato in alto a destra sotto il **salvare** pulsante. La richiesta dovrebbe essere simile all'esempio seguente:

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

È presente alcuna registrazione per l'oggetto specificato **installationId** a questo punto. La verifica dovrebbe restituire una risposta "404 non trovato" invece di una risposta "401 non autorizzato". Questo risultato è necessario verificare che il token di firma di accesso condiviso è stato accettato.

### <a name="implement-the-installation-service-class"></a>Implementare la classe di servizio di installazione

Successivamente si implementeranno i wrapper di base per il [API REST di installazioni](/rest/api/notificationhubs/create-overwrite-installation).  

Aggiungere un nuovo file Swift denominato **NotificationRegistrationService.swift** sotto il **Services** cartella, quindi aggiungere il codice seguente al file:

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

I dettagli necessari vengono forniti come parte dell'inizializzazione. I tag e i modelli, facoltativamente, vengono passati nel **registrare** funzione a far parte del **installazione dispositivo** payload JSON.  

Il **registrare** funzione chiama altre funzioni private per preparare la richiesta. Dopo che viene ricevuta una risposta, il completamento è denominato e indica se la registrazione è riuscita.  

L'endpoint di richiesta viene costruita mediante il **getBaseAddress** (funzione). La costruzione Usa i parametri di hub di notifica *dello spazio dei nomi* e *nome* che sono stati forniti durante l'inizializzazione.  

Il **getSasToken** funzione controlla se il token attualmente archiviato è valido. Se il token non è valido, la funzione chiama **TokenUtility** per generare un nuovo token e quindi lo archivia prima di restituire un valore.

Infine **encodeToJson** converte gli oggetti modello corrispondente in JSON da usare come parte del corpo della richiesta.

### <a name="invoke-the-notification-hubs-rest-api"></a>Richiamare l'API REST di hub di notifica

L'ultimo passaggio è l'aggiornamento **AppDelegate** da usare **NotificationRegistrationService** registrarsi nostro **hub di notifica**.

1. Aprire **appdelegate. SWIFT** e aggiungere una variabile a livello di classe per archiviare un riferimento per il **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. Nello stesso file, aggiornare il **didRegisterForRemoteNotificationsWithDeviceToken** funzione per inizializzare il **NotificationRegistrationService** con i parametri richiesti e quindi chiamare il **registrare** (funzione).

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

    In sostanza, si intende usare un paio di istruzioni print per aggiornare la finestra di output con il risultato del **registrare** operazione.

1. A questo punto compilare ed eseguire l'app in un dispositivo fisico. Dovrebbe essere "Registrato" nella finestra di output.

## <a name="test-the-solution"></a>Testare la soluzione

L'app in questa fase è registrato con **NotificationHub** e può ricevere le notifiche push. In Xcode, arrestare il debugger e chiudere l'app, se è in esecuzione. Successivamente, verificare che il **installazione dei dispositivi** dettagli sono come previsto e che l'App nell'app possono ora ricevere notifiche push.  

### <a name="verify-the-device-installation"></a>Verificare l'installazione del dispositivo

È ora possibile apportare la stessa richiesta, come in precedenza tramite **Postman** per [verifica per determinare se il token di firma di accesso condiviso](#verify-the-sas-token). Supponendo che il token di firma di accesso condiviso non è ancora scaduto, la risposta dovrebbe ora includere i dettagli di installazione che è stata specificata, ad esempio i tag e i modelli.

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

### <a name="send-a-test-notification-azure-portal"></a>Inviare una notifica di prova (portale di Azure)

Il modo più rapido per verificare che ora è possibile ricevere le notifiche è per passare all'hub di notifica nel portale di Azure:

1. Nel portale di Azure, selezionare la **Panoramica** scheda nell'hub di notifica.

1. Selezionare **invio di prova**, che è maggiore di **Essentials** riepilogo in alto a sinistra della finestra del portale:

    ![Essentials di hub di notifica pulsante Invia riepilogo Test](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Scegli **modello personalizzato** dalle **piattaforme** elenco.

1. Immettere **12345** per il **Invia a espressione Tag**. In precedenza è stato specificato questo tag nell'installazione.

1. Facoltativamente, modificare il **messaggio** nel payload JSON:

    ![Invio di prova di hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selezionare **Send** (Invia). Il portale deve indicare se la notifica è stata inviata correttamente al dispositivo:

    ![Test di hub di notifica invia i risultati](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Supponendo che l'app non è in esecuzione in primo piano, si dovrebbe vedere anche una notifica nel **centro notifiche** nel dispositivo. Se si tocca la notifica, è necessario aprire l'app e Mostra l'avviso.

    ![Ricevuta notifica di esempio](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Inviare una notifica di prova (posta elettronica del vettore)

È possibile inviare notifiche tramite il [API REST](/rest/api/notificationhubs/) utilizzando **Postman**, che può essere un modo più pratico per eseguire il test.

1. Aprire una nuova scheda nel **Postman**.

1. Impostare la richiesta **POST**e immettere l'indirizzo seguente:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configurare le intestazioni della richiesta come indicato di seguito:

   | Chiave                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Authorization                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | template                       |
   | `Tags`                           | "12345"                        |

1. Configurare la richiesta **corpo** usare **RAW - JSON (application.json)** con il payload JSON seguente:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selezionare il **codice** pulsante, che è sotto il **salvare** pulsante in alto a destra della finestra. La richiesta dovrebbe essere simile all'esempio seguente:

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

È necessario ottenere un codice di stato di esito positivo e ricevere la notifica sul dispositivo client.

## <a name="next-steps"></a>Passaggi successivi
È ora disponibile un'app Swift iOS di base connessa a un hub di notifica tramite il [API REST](/rest/api/notificationhubs/) e può inviare e ricevere le notifiche. Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di hub di notifica di Azure](notification-hubs-push-notification-overview.md)
- [API REST di Hub di notifica](/rest/api/notificationhubs/)
- [Notification Hubs SDK per le operazioni di back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK su GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrare con il servizio back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Uso dei tag](notification-hubs-tags-segment-push-message.md) 
- [Uso dei modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
- [Controllo degli accessi del Bus di servizio con firme di accesso condiviso](../service-bus-messaging/service-bus-sas.md)
- [A livello di codice generano token SAS](/rest/api/eventhub/generate-sas-token)
- [Sicurezza di Apple: crittografia comune](https://developer.apple.com/security/)
- [UNIX come valore Epoch time](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
