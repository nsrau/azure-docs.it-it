---
title: Notifiche push alle App iOS Swift con hub di notifica di Azure | Microsoft Docs
description: Informazioni su come le notifiche push alle App iOS Swift con hub di notifica di Azure.
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
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994767"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>Esercitazione: Notifiche push alle App iOS Swift con l'API REST di hub di notifica

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

In questa esercitazione, si usa hub di notifica per inviare notifiche push a un'applicazione basata su codice Swift iOS con il [API REST](/rest/api/notificationhubs/). Si crea un'app iOS vuota che riceve notifiche push tramite [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Generare il file della richiesta di firma del certificato
> * Richiedere le notifiche push all'app
> * Creare un profilo di provisioning per l'app
> * Creare un hub di notifica
> * Configurare l'hub di notifica con informazioni APNS
> * Connettere l'app iOS all'hub di notifica
> * Testare la soluzione

## <a name="prerequisites"></a>Prerequisiti
Per seguire la procedura, è necessario:

- Seguire i passaggi [Panoramica di hub di notifica](notification-hubs-push-notification-overview.md) se non si ha familiarità con il servizio. 
- Informazioni su installazione e le registrazioni: [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- Un oggetto attivo [Account per sviluppatore Apple](https://developer.apple.com) 
- Un Mac con Xcode insieme a un certificato dello sviluppatore valido installato nel portachiavi
- Un dispositivo fisico iPhone è possibile eseguire ed eseguire il debug con (non è possibile testare le notifiche push con il simulatore)
- La registrazione del dispositivo fisico iPhone nel [portale Apple](https://developer.apple.com) e associato al certificato
- Un' [sottoscrizione di Azure](https://portal.azure.com) in cui è possibile creare e gestire le risorse

Dovrebbe essere possibile seguire la procedura per creare questo primo-principi di esempio senza esperienza precedente. Tuttavia, sarà utile familiarità con i concetti seguenti:

- Creazione di App iOS con Xcode e Swift
- Configurazione di un' [Hub di notifica di Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) per iOS
- Familiarità con le [portale Apple Developer](https://developer.apple.com) e il [portale di Azure](https://portal.azure.com)

> [!NOTE]
> L'hub di notifica verrà configurato per usare la *Sandbox* solo modalità di autenticazione. Utilizzare questa modalità di autenticazione non per i carichi di lavoro di produzione.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Connettere l'app iOS all'hub di notifica
In questa sezione si creerà l'app per iOS che si connetterà all'hub di notifica.  

### <a name="create-an-ios-project"></a>Creare un progetto iOS
1. Nelle **Xcode**, creare un nuovo progetto iOS e selezionare il **Single View Application** modello.
2. Quando si impostano le opzioni per il nuovo progetto, seguire questa procedura:
    1. Usare lo stesso **Product Name** (vale a dire **PushDemo**) e **identificatore organizzazione** (, ovvero `com.<organization>`) usata durante la **Bundle Identificatore** in cui è stato impostato il **portale Apple Developer**. 
    2. Scegliere il **Team** che il **App ID** è stato impostato per.
    3. Impostare il **language** al **Swift**.
    4. Fare clic su **Avanti**
3. Creare una nuova cartella denominata **SupportingFiles**.
4. Creare una nuova **plist** file denominato **devsettings.plist** sotto il **SupportingFiles** cartella. Assicurarsi di aggiungere questa cartella per i **gitignore** file in modo che non è eseguito il commit quando si lavora con un **repository git**. In un'app di produzione, si sarebbero probabilmente in modo condizionale impostare questi segreti come parte di un processo di compilazione automatizzata. Tuttavia, non è incluso come parte di questa procedura dettagliata.
5. Update **devsettings.plist** per includere le voci di configurazione seguente (utilizzando i propri valori il **Hub di notifica** è stato effettuato il provisioning):

   | Chiave                            | Type                     | Value                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   È possibile trovare i valori richiesti, passare al **Hub di notifica** risorsa nel **portale di Azure**. È possibile trovare il **notificationHubName** e il **notificationHubNamespace** valori nell'angolo superiore destro della **Essentials** riepilogo all'interno di  **Panoramica** pagina.

   ![Riepilogo di Essentials hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   È possibile trovare il **notificationHubKeyName** e **notificationHubKey** valori, passare a **criteri di accesso**, fare clic sui rispettivi **accesso Criteri**. Ad esempio: `DefaultFullSharedAccessSignature`. Quindi copiare dal **stringa di connessione primaria** il valore preceduto `SharedAccessKeyName=` per `notificationHubKeyName` e il valore preceduto `SharedAccessKey=` per il `notificationHubKey`. La stringa di connessione deve essere nel formato seguente:
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   In sostanza, si userà *DefaultFullSharedAccessSignature* quindi è possibile usare il token per inviare notifiche e. Tuttavia, in pratica il `DefaultListenSharedAccessSignature` sarebbe una scelta migliore per le situazioni in cui si desidera solo ricevere le notifiche.       
6. Sotto **Project Navigator**, fare clic sui **nome del progetto**, quindi fare clic sui **generali** scheda
7. Trovare **Identity**, quindi impostare il **identificatore del Bundle** valore in modo che corrisponda a quello usato per il **ID App** (dal passaggio precedente), ovvero `'com.<organization>.PushDemo'`
8. Trovare **Signing**, quindi assicurarsi selezionare un valore appropriato **Team** per il **Account per sviluppatore Apple** (quello in cui è stato creato i profili e certificati in precedenza).  **Xcode** recupererà automaticamente appropriato **profilo di Provisioning** base il **identificatore del Bundle**. Se non viene visualizzato il nuovo **profilo di Provisioning**, provare ad aggiornare i profili per il **identità di firma** (*Xcode > Preferenze > Account > Visualizza dettagli*). Facendo clic sui **identità di firma**, quindi facendo clic sul **aggiornare** pulsante in basso a destra deve scaricare i profili.
9. Selezionare il **capacità** scheda e assicurarsi che **notifiche push** sono abilitati.
10. Aprire il **appdelegate. SWIFT** file per implementare il *UNUserNotificationCenterDelegate* protocol e aggiungere il codice seguente all'inizio della classe:
    
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

    Si useranno questi membri in un secondo momento. Il *tag* e *genericTemplate* verrà usato come parte della registrazione. Per altre informazioni sui tag, vedere [tag per le registrazioni](notification-hubs-tags-segment-push-message.md) e [registrazioni modello](notification-hubs-templates-cross-platform-push-messages.md).
 
11. Nello stesso file, aggiungere il codice seguente nel *didFinishLaunchingWithOptions* funzione:

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

    Questo codice recupera i valori delle impostazioni da **devsettings.plist**, imposta le **AppDelegate** di classi il *UNUserNotificationCenter* delegare, l'autorizzazione delle richieste per le notifiche push e quindi chiama *registerForRemoteNotifications*.
    
    In sostanza, il codice supporta **iOS 10 e versioni successive solo**. È possibile aggiungere il supporto per le versioni precedenti del sistema operativo in modo condizionale con le rispettive API e gli approcci come si farebbe normalmente.

12. Nello stesso file, aggiungere le funzioni seguenti:

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

    Il codice Usa il *installationId* e *pushChannel* valori da registrare con il **Hub di notifica**. In questo caso, si usa *UIDevice.current.identifierForVendor* per fornire commenti con un valore univoco per identificare il dispositivo e quindi applicare il formato di *deviceToken* per fornirci il desiderato *pushChannel* valore. Il *showAlert* funzione è sufficiente per la visualizzazione di testo del messaggio a scopo dimostrativo.

13. Sempre nella **appdelegate. SWIFT**, aggiungere il *willPresent* e *didReceive* **UNUserNotificationCenterDelegate** funzioni ai visualizzare un avviso quando la notifica è arrivato quando viene eseguita l'app in primo piano e sfondo rispettivamente
    
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

14. Aggiungere istruzioni print a fondo le *didRegisterForRemoteNotificationsWithDeviceToken* funzione per verificare che *installationId* e *pushChannel* sono in corso valori assegnati
15. Creare cartelle (**modelli**, **Services**, e **utilità**) per i componenti fondamentali a cui verranno aggiunti al progetto in un secondo momento
16. Verificare che il progetto compilato ed eseguito in un dispositivo fisico (non è possibile testare le notifiche tramite il simulatore push)

### <a name="create-models"></a>Creare modelli
In questo passaggio si creerà un set di modelli per rappresentare il [API REST di hub di notifica](/rest/api/notificationhubs/) payload e archiviare la necessaria **token di firma di accesso condiviso** dei dati.


1.  Aggiungere un nuovo file swift per il **modelli** chiamato **PushTemplate.swift**. Questo modello fornisce un struct che rappresenta il **corpo** di un singolo modello come parte delle **DeviceInstallation** payload:
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Aggiungere un nuovo file swift per il **modelli** cartella denominata **DeviceInstallation.swift**. Questo file definisce un tipo struct che rappresenta il payload per la creazione o l'aggiornamento di un **installazione dei dispositivi**. Aggiungere il codice seguente al file:
    
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

3.  Aggiungere un nuovo file swift sotto **modelli** chiamato **TokenData.swift**. Questo modello verrà usato per archiviare una **token di firma di accesso condiviso** insieme a scadenza

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
**Hub di notifica** usano la stessa infrastruttura di sicurezza come **Bus di servizio di Azure**. Per chiamare l'API REST, dovrai [a livello di codice genera un token di firma di accesso condiviso](/rest/api/eventhub/generate-sas-token) che può essere utilizzato nel **autorizzazione** intestazione della richiesta.  

Il token risulta sarà nel formato seguente: 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Il processo stesso prevede gli stessi sei passaggi principali:  

1.  Calcolo della scadenza nel [UNIX come valore Epoch time](https://en.wikipedia.org/wiki/Unix_time) formato (secondi trascorsi dalla 00:00:00 UTC dell'1 gennaio 1970)
2.  Formattazione di **l'URL risorsa** (che rappresenta la risorsa si sta provando ad accedere, vale a dire `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`) è codificato in percentuale e lettere minuscole
3.  Preparare il **StringToSign**, che è costituito da `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  Calcolo (e la codifica base 64) il **firma** usando la **HMAC-SHA256** del **StringToSign** valore con il **chiave** fa parte del **Stringa di connessione** (per i rispettivi **regola di autorizzazione**)
5.  Formattazione di codificato in base 64 **firma** pertanto è percento codificati
6.  Costruzione di **token** nel formato previsto tramite il **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**e **UrlEncodedResourceUrl** valori

Vedere la [documentazione sul Bus di servizio di Azure](../service-bus-messaging/service-bus-sas.md) per una panoramica più approfondita delle **firma di accesso condiviso** e come viene usato dal **del Bus di servizio di Azure** e  **Hub di notifica**.

Ai fini di questo esempio Swift, si intende usare open source di Apple **CommonCrypto** libreria per facilitare l'hash della firma. Come è una libreria C, non è accessibile in Swift out-of-the-box. Tuttavia, è possibile rendere questo disponibili tramite un'intestazione provvisoria. Per aggiungere e configurare l'intestazione provvisoria:

1. Nelle **Xcode**, passare a **File**, quindi **New**, quindi **File** selezionare **File di intestazione** denominarlo *'BridgingHeader.h'*
2. Modificare il file da importare **CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. Aggiornare la destinazione **Build Settings** per fare riferimento l'intestazione provvisorio. Aprire il **delle impostazioni di compilazione** scheda e scorrere verso il basso il **Swift Compiler** sezione. Verificare che il **installare intestazione compatibilità Objective-C** opzione impostata su **Yes** e immettere il percorso file per l'intestazione provvisoria nel **Objective-C bridging intestazione**   vale a dire opzione `'\<ProjectName\>/BridgingHeader.h'`. Se non è possibile trovare queste opzioni, assicurarsi di disporre il **tutte** visualizzazione selezionata (anziché **base** o **personalizzato**).
    
   Sono disponibili molte librerie di wrapper open source di terze parti disponibili, questo potrebbero rendere usando **CommonCrypto** un po' più semplice. È oltre l'ambito di questo articolo.

4. Aggiungere un nuovo file Swift sotto il **Utilities** cartella denominata **TokenUtility.swift** e aggiungere il codice seguente:

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
    
    Questa utilità incapsula la logica responsabile della generazione di **token di firma di accesso condiviso**. Il *getSasToken* funzione gestisce inoltre i passaggi generali necessari per preparare il token, come descritto in precedenza e verrà chiamata dal servizio di installazione nei passaggi più avanti in questa esercitazione. Le altre due funzioni vengono chiamate dal *getSasToken funzione*; *sha256HMac* per il calcolo della firma e *urlEncodedString* per la codifica di stringa dell'Url corrispondente. Il *urlEncodedString* funzione si trovava richiesto perché non è stato possibile ottenere l'output richiesto tramite l'oggetto incorporato *addingPercentEncoding* (funzione). Il [archiviazione di Azure SDK per iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) servite come un ottimo esempio di come implementare queste operazioni, anche se in Objective-C. Ulteriori informazioni sul **i token SAS del Bus di servizio Azure** reperibile nella [documentazione sul Bus di servizio di Azure](../service-bus-messaging/service-bus-sas.md). 

### <a name="verify-the-sas-token"></a>Verificare il token di firma di accesso condiviso
Prima di implementare il servizio di installazione del client, è possibile verificare che l'app viene generato in modo corretto le **token di firma di accesso condiviso** tramite l'utilità http preferito. Ai fini di questo post, sarà nostro strumento di elezione **Postman**.

Annotare il *installationId* e il *token* valori generati dall'app usando un correttamente posizionato stampare istruzione o un punto di interruzione. 

Seguire questa procedura per chiamare il *installazioni* API:

1. Nelle **Postman**, aprire una nuova scheda
2. Impostare la richiesta **ottenere** e l'indirizzo seguente:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. Configurare le intestazioni della richiesta come indicato di seguito:
    
   | Chiave           | Value            |
   | ------------- | ---------------- |
   | Tipo contenuto  | application/json |
   | Authorization | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Fare clic sui **codice** pulsante (alto a destra sotto il **salvare** pulsante). La richiesta dovrebbe essere simile all'esempio seguente:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. Scegliere il **inviare** pulsante

È presente alcuna registrazione per l'oggetto specificato *installationId* a questo punto, tuttavia deve risultare un **404 non trovato** risposta anziché **401 non autorizzato**. Questo risultato deve verificare che il **token di firma di accesso condiviso** è stata accettata.

### <a name="implement-the-installation-service-class"></a>Implementare la classe di servizio di installazione
Successivamente si implementeranno i wrapper di base per il [API REST di installazioni](/rest/api/notificationhubs/create-overwrite-installation).  

Aggiungere un nuovo file Swift sotto il **Services** cartella denominata **NotificationRegistrationService.swift**, quindi aggiungere il codice seguente al file:

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
 
I dettagli necessari vengono forniti come parte dell'inizializzazione. I tag e i modelli, facoltativamente, vengono passati nel *registrare* funzione a far parte del **installazione dispositivo** payload JSON.  

Il *registrare* funzione chiama altre funzioni private per preparare la richiesta. Una volta che viene ricevuta una risposta, il completamento è chiamato che indica se la registrazione è riuscita o meno.  

L'endpoint di richiesta viene costruita mediante il *getBaseAddress* funzione usando la **Hub di notifica** parametri **dello spazio dei nomi** e **nome**fornite durante l'inizializzazione.  

Il *getSasToken* funzione controlla se il token attualmente archiviato è valido, in caso contrario, effettua una chiamata ai **TokenUtility** per generarne uno nuovo e archiviarle prima di restituire un valore. 

Infine il *encodeToJson* convertirà gli oggetti modello corrispondente in JSON da usare come parte del corpo della richiesta.

### <a name="invoke-the-notification-hubs-rest-api"></a>Richiamare l'API REST di hub di notifica
L'ultimo passaggio consiste nell'aggiornare **AppDelegate** da utilizzare il **NotifiationRegistrationService** registrarsi nostro **hub di notifica**. 

1. Aprire **appdelegate. SWIFT** e aggiungere una variabile a livello di classe per archiviare un riferimento per il **NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. Nello stesso file, aggiornare il *didRegisterForRemoteNotificationsWithDeviceToken* funzione per inizializzare il **NotificationRegistrationService** con i parametri richiesti, quindi chiamare il *registrare* (funzione).

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
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    In sostanza, si intende usare un paio di istruzioni print per aggiornare la finestra di output con il risultato del *registrare* operazione. 

3. A questo punto compilare ed eseguire l'app (in un dispositivo fisico). Si noterà **"Registrato"** nella finestra di output.

## <a name="test-the-solution"></a>Testare la soluzione
In questa fase, l'app viene registrata con **NotificationHub** e può ricevere le notifiche push. Nelle **Xcode**, arrestare il debugger e chiudere l'app (se è in esecuzione). Successivamente, si controllerà che il **installazione dei dispositivi** dettagli sono come previsto e che l'app può ora effettivamente ricevere le notifiche push.  

### <a name="verify-the-device-installation"></a>Verificare l'installazione del dispositivo
È ora possibile apportare la stessa richiesta, come è stato fatto in precedenza tramite **Postman** per [verifica per determinare se il token di firma di accesso condiviso](#verify-the-sas-token). Supponendo che il **token di firma di accesso condiviso** non è ancora scaduto, la risposta deve includono ora i dettagli di installazione specificati, ad esempio i tag e i modelli.  

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
È il modo più rapido per verificare che ora è possibile ricevere notifiche su cui spostarsi il **Hub di notifica** nel **portale di Azure**.

1. Nel **portale di Azure**, passare al **Cenni preliminari sulla** scheda il **Hub di notifica**
2. Fare clic su **invio di prova** (top-left) sopra la **Essentials** riepilogo

    ![Essentials di hub di notifica pulsante Invia riepilogo Test](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. Scegli **modello personalizzato** dall'elenco di **piattaforme**
4. Immettere **12345** per il **inviare a espressione Tag** (è stato specificato questo tag nel nostro installazione)
5. Facoltativamente, modificare il **messaggio** nel payload JSON
    
    ![Invio di prova di hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. Fare clic su **inviare** e il portale deve indicare se la notifica è stata inviata correttamente al dispositivo

    ![Test di hub di notifica invia i risultati](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Si dovrebbe vedere anche una notifica nel **centro notifiche** sul dispositivo (presupponendo che l'app non è in esecuzione in primo piano). Se si tocca la notifica deve aprire l'app e visualizzare l'avviso.

    ![Ricevuta notifica di esempio](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>Inviare una notifica di prova (Postman)
È possibile inviare notifiche tramite i rispettivi [API REST](/rest/api/notificationhubs/) tramite **Postman** come corretto e potrebbe essere un modo più pratico per eseguire il test. 

1. Nelle **Postman**, aprire una nuova scheda
2. Impostare la richiesta **POST** e immettere l'indirizzo seguente:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. Configurare le intestazioni della richiesta come indicato di seguito:
    
   | Chiave                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Tipo contenuto                   | application/json;charset=utf-8 |
   | Authorization                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | modello                       |
   | `Tags`                           | "12345"                        |

4. Configurare la richiesta **corpo** usare **RAW - JSON (application.json)** con il payload JSON seguente:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. Fare clic sui **codice** pulsante (alto a destra sotto il **salvare** pulsante). La richiesta dovrebbe essere simile all'esempio seguente:

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

5. Scegliere il **inviare** pulsante

È necessario ottenere un codice di stato di esito positivo e ricevere la notifica sul dispositivo client.

## <a name="next-steps"></a>Passaggi successivi
È ora disponibile un'app Swift iOS di base connessa a un **Hub di notifica** tramite il [API REST](/rest/api/notificationhubs/) e può inviare e ricevere le notifiche. Per altre informazioni, vedere gli articoli seguenti: 

- [Panoramica di hub di notifica di Azure](notification-hubs-push-notification-overview.md)
- [API REST di Hub di notifica](/rest/api/notificationhubs/)
- [Notification Hubs SDK per le operazioni di back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK su GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrare con back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Uso dei tag](notification-hubs-tags-segment-push-message.md) 
- [Uso dei modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
- [Controllo degli accessi del Bus di servizio con firme di accesso condiviso](../service-bus-messaging/service-bus-sas.md)
- [A livello di codice generano token SAS](/rest/api/eventhub/generate-sas-token)
- [Sicurezza di Apple: crittografia comune](https://developer.apple.com/security/)
- [UNIX come valore Epoch time](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
