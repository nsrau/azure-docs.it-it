---
title: Inviare notifiche push alle app Swift iOS che usano Hub di notifica di Azure. Documenti Microsoft
description: Informazioni su come inviare notifiche alle app Swift iOS che usano Hub di notifica di Azure.Learn how to push notifications to Swift iOS apps that use Azure Notification Hubs.
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
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336629"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Esercitazione: Inviare notifiche push alle app Swift iOS usando l'API REST degli hub di notificaTutorial: Send push notifications to Swift iOS apps using Notification Hubs REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Repentino](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

In questa esercitazione si usano hub di notifica di Azure per inviare notifiche a un'applicazione iOS basata su Swift usando [l'API REST.](/rest/api/notificationhubs/) È inoltre possibile creare un'app iOS vuota che riceve le notifiche push utilizzando il [servizio Apple Push Notification (APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Questa esercitazione illustra i passaggi seguenti:This tutorial takes you through the following steps:

> [!div class="checklist"]
> * Generare il file di richiesta di firma del certificato.
> * Richiedi notifiche push alla tua app.
> * Creare un profilo di provisioning per l'app.
> * Creare un hub di notifica.
> * Configurare l'hub di notifica con le informazioni APNs.
> * Connetti la tua app iOS a un hub di notifica.
> * Testare la soluzione.

Il codice completo per questa esercitazione è disponibile in [GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura, è necessario:

- Per usare la [panoramica](notification-hubs-push-notification-overview.md) degli hub di notifica di Azure se non si ha familiarità con il servizio.
- Per conoscere [le registrazioni e l'installazione](notification-hubs-push-notification-registration-management.md).
- Un [account Apple Developer](https://developer.apple.com)attivo .
- Un Mac che esegue Xcode insieme a un certificato per sviluppatori valido installato nel portachiavi.
- Un dispositivo iPhone fisico con cui è possibile eseguire ed eseguire il debug, in quanto non è possibile testare le notifiche push con il simulatore.
- Il dispositivo iPhone fisico registrato nel [portale Apple](https://developer.apple.com) e associato al certificato.
- Una sottoscrizione di [Azure](https://portal.azure.com) in cui è possibile creare e gestire le risorse.

Anche se non si dispone di alcuna esperienza precedente con lo sviluppo iOS, si dovrebbe essere in grado di seguire i passaggi per la creazione di questo esempio di primi principi. Tuttavia, si trarrà vantaggio dalla familiarità con i seguenti concetti:

- Creazione di app iOS con Xcode e Swift.
- Configurazione di un hub di notifica di Azure per iOS.Configuring an [Azure Notification hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) for iOS.
- Il [portale per sviluppatori Apple](https://developer.apple.com) e il portale di [Azure](https://portal.azure.com).

> [!NOTE]
> L'hub di notifica verrà configurato per utilizzare solo la modalità di autenticazione **sandbox.** Non usare questa modalità di autenticazione per i carichi di lavoro di produzione.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Connettere l'app iOS a un hub di notifica

In questa sezione verrà compilata l'app iOS che si connetterà all'hub di notifica.  

### <a name="create-an-ios-project"></a>Creare un progetto iOSCreate an iOS project

1. In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application** .

1. Quando si impostano le opzioni per il nuovo progetto:

   1. Specificare il **nome del prodotto** (PushDemo) e **l'identificatore dell'organizzazione** (`com.<organization>`) utilizzati quando si imposta l'identificatore del **bundle** nel portale per sviluppatori Apple.

   1. Scegli il **team** per il quale è stato impostato l'ID **app.**

   1. Impostare la **lingua** **su Swift**.

   1. Fare clic su **Avanti**.

1. Creare una nuova cartella denominata **SupportingFiles**.

1. Creare un nuovo file p-list denominato **devsettings.plist** nella cartella **SupportingFiles.** Assicurati di aggiungere questa cartella al file **gitignore** in modo che non ne venga eseguito il commit quando lavori con un repository git. In un'app di produzione, è probabile che questi segreti vengano impostato in modo condizionale come parte di un processo di compilazione automatizzato. Tali impostazioni non vengono trattate in questa procedura dettagliata.

1. Aggiornare **devsettings.plist** per includere le seguenti voci di configurazione usando i propri valori dell'hub di notifica di cui è stato eseguito il provisioning:

   | Chiave                            | Type                     | valore                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey (chiave)             | string                   | \<> hubKey                  |
   | notificationHubKeyName         | string                   | \<> hubKeyName              |
   | notificationHubName (nome Hub)            | string                   | \<> hubName                 |
   | notificationHubNamespace       | string                   | \<> hubNamespace            |

   È possibile trovare i valori necessari passando alla risorsa hub di notifica nel portale di Azure.You can find the required values by navigating to the notification hub resource in the Azure portal. In particolare, i valori **notificationHubName** e **notificationHubNamespace** si trovano nell'angolo superiore destro del riepilogo di **Essentials** all'interno della pagina **Panoramica.**

   ![Riepilogo di Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   È inoltre possibile trovare i valori **notificationHubKeyName** e **notificationHubKey** passando a Criteri `DefaultFullSharedAccessSignature`di **accesso** e selezionando i rispettivi criteri di **accesso**, ad esempio . Successivamente, copiare dalla **stringa** di connessione `SharedAccessKeyName=` primaria `notificationHubKeyName` il valore con `SharedAccessKey=` prefisso for e il valore preceduto da per l'oggetto `notificationHubKey`.

   La stringa di connessione deve essere nel formato seguente:The connection string should be in the following format:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Per semplificare la `DefaultFullSharedAccessSignature` semplicità, specificare in modo da poter utilizzare il token per inviare notifiche. In pratica `DefaultListenSharedAccessSignature` il sarebbe una scelta migliore per le situazioni in cui si desidera solo ricevere notifiche.

1. In **Navigatore progetto**, selezionare il **nome del progetto** e quindi selezionare la scheda **Generale** .

1. Trovare **Identity** e quindi impostare il `com.<organization>.PushDemo`valore di Bundle **Identifier** in modo che corrisponda a , ovvero il valore utilizzato per l'ID **app** di un passaggio precedente.

1. Trova **Funzionalità di &** di firma , quindi seleziona il **team** appropriato per il tuo account **Apple Developer.** Il valore **Team** deve corrispondere a quello in cui sono stati creati i certificati e i profili.

1. Xcode dovrebbe estrarre automaticamente il valore **del profilo** di provisioning appropriato in base all'identificatore del **bundle.** Se non vedi il nuovo valore **Profilo di provisioning,** prova ad aggiornare i profili per l'identità di **firma** selezionando**Account** **preferenze** >  **codice X,** > quindi seleziona il pulsante **Scarica profili manuali** per scaricare i profili.

1. Sempre nella scheda **Funzionalità di &** di firma, fare clic sul pulsante **Funzionalità** e toccare due volte **Notifiche push** dall'elenco per **assicurarsi** che le notifiche push siano abilitate.

1. Aprire il file **AppDelegate.swift** per implementare il protocollo **UNUserNotificationCenterDelegate** e aggiungere il codice seguente all'inizio della classe:

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

    Questi membri verranno utilizzati in un secondo momento. In particolare, si utilizzerà il membro **tags** come parte della registrazione utilizzando un **modello personalizzato.** Per ulteriori informazioni sui tag, vedere [Tag per le registrazioni](notification-hubs-tags-segment-push-message.md) e [le registrazioni di modelli](notification-hubs-templates-cross-platform-push-messages.md).

1. Nello stesso file aggiungere il codice seguente alla funzione **didFinishLaunchingWithOptions:**

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

    Questo codice recupera i valori di impostazione da **devsettings.plist**, imposta la classe **AppDelegate** come delegato **UNUserNotificationCenter,** richiede l'autorizzazione per le notifiche push e quindi chiama **registerForRemoteNotifications**.

    Per semplificare, il codice supporta *solo iOS 10 e versioni successive.* È possibile aggiungere il supporto per le versioni precedenti del sistema operativo usando in modo condizionale le API e gli approcci delle rispettive API come si farebbe normalmente.

1. Nello stesso file, aggiungere le seguenti funzioni:

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

    Il codice usa i valori **installationId** e **pushChannel** per la registrazione con l'hub di notifica. In questo caso, si usa **UIDevice.current.identifierForVendor** per fornire un valore univoco per identificare il dispositivo e quindi formattare **deviceToken** per fornire il valore **pushChannel** desiderato. La funzione **showAlert** esiste semplicemente per visualizzare il testo del messaggio a scopo dimostrativo.

1. Sempre nel file **AppDelegate.swift,** aggiungere le funzioni **willPresent** e **didReceive** a **UNUserNotificationCenterDelegate**. Queste funzioni visualizzano un avviso quando vengono notificate che un'app è in esecuzione rispettivamente in primo piano o in background.

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

1. Aggiungere istruzioni print nella parte inferiore della funzione **didRegisterForRemoteNotificationsWithDeviceToken** per verificare che i valori di **installationId** e **pushChannel** siano assegnati.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Creare le cartelle **Modelli**, **Servizi**e **Utilità** per i componenti di base che verranno aggiunti al progetto in un secondo momento.

1. Verificare che il progetto venga compilato ed eseguito in un dispositivo fisico. Le notifiche push non possono essere testate tramite il simulatore.

### <a name="create-models"></a>Creare modelli

In questo passaggio verrà creato un set di modelli per rappresentare i payload [dell'API REST degli hub](/rest/api/notificationhubs/) di notifica e per archiviare i dati del token di firma di accesso condiviso (SAS) necessari.

1. Aggiungere un nuovo file Swift denominato **PushTemplate.swift** alla cartella **Models.** Questo modello fornisce uno struct che rappresenta il BODY di un singolo modello come parte del payload **DeviceInstallation.This** model provides a struct representing the **BODY** of an individual template as part of the DeviceInstallation payload.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Aggiungere un nuovo file Swift denominato **DeviceInstallation.swift** alla cartella **Models.** Questo file definisce una struttura che rappresenta il payload per la creazione o l'aggiornamento di **un'installazione di dispositivi**. Aggiungere il codice seguente al file:

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

1. Aggiungere un nuovo file Swift denominato **TokenData.swift** alla cartella **Modelli.** Questo modello verrà usato per archiviare un token di firma di accesso condiviso insieme alla relativa scadenza.

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

Gli hub di notifica usano la stessa infrastruttura di sicurezza del bus di servizio di Azure.Notification Hubs use the same security infrastructure as Azure Service Bus. Per chiamare l'API REST, è necessario [generare a livello](/rest/api/eventhub/generate-sas-token) di codice un token di firma di accesso condiviso che può essere usato nell'intestazione **Authorization** della richiesta.  

Il token risultante sarà nel seguente formato:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Il processo stesso prevede gli stessi sei passaggi chiave:  

1. Calcolo della scadenza nel formato [ora UNIX Epoch,](https://en.wikipedia.org/wiki/Unix_time) ovvero il numero di secondi trascorsi dalla mezzanotte Coordinated Universal Time, 1 gennaio 1970.
1. Formattazione **di ResourceUrl** che rappresenta la risorsa a cui si sta tentando di accedere in modo che sia con codifica percentuale e minuscola. Il **ResourceUrl** ha `'https://<namespace>.servicebus.windows.net/<hubName>'`il formato .
1. Preparazione di **StringToSign**, formattato come `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Elaborazione e codifica Base64 della **firma** utilizzando l'hash HMAC-SHA256 del valore **StringToSign.** Il valore hash viene utilizzato con la parte **Chiave** della stringa di **connessione** per la rispettiva regola di **autorizzazione.**
1. Formattazione della **firma** con codifica Base64 in modo che sia codificata in percentuale.
1. Costruire il token nel formato previsto utilizzando i valori **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**e **UrlEncodedResourceUrl** .

Vedere la [documentazione](../service-bus-messaging/service-bus-sas.md) del bus di servizio di Azure per una panoramica più completa della firma di accesso condiviso e del modo in cui vengono usate dal bus di servizio di Azure e dagli hub di notifica.

Ai fini di questo esempio Swift, utilizzerai la libreria **CommonCrypto** open source di Apple per facilitare l'hashing della firma. Poiché è una libreria C, non è accessibile in Swift. È possibile rendere disponibile la libreria utilizzando un'intestazione di bridging.

Per aggiungere e configurare l'intestazione di bridging:

1. In Xcode, selezionare **File** > **nuovo** > **file** > **di intestazione file**. Assegnare al file di intestazione il nome **BridgingHeader.h**.

1. Modificare il file per importare **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Aggiornare le impostazioni di **compilazione** della destinazione in modo che faccia noto l'intestazione di bridging:

   1. Tocca il progetto **PushDemo** e scorri verso il basso fino alla sezione **Compilatore Swift.**

   1. Verificare che l'opzione **Installa intestazione compatibilità Objective-C** sia impostata su **Sì**.

   1. Immettere il `'<ProjectName>/BridgingHeader.h'` percorso del file nell'opzione **Intestazione bridging obiettivo-C.** Questo è il percorso del file per l'intestazione di bridging.

   Se non è possibile trovare queste opzioni, assicurarsi di avere selezionato la visualizzazione **Tutto** anziché **Di base** o **Personalizzato**.

   Sono disponibili molte librerie wrapper open source di terze parti che potrebbero semplificare l'utilizzo di **CommonCrypto.** Tuttavia, la discussione di tali librerie esula dall'ambito di questo articolo.

1. Aggiungere un nuovo file Swift denominato **TokenUtility.swift** all'interno della cartella **Utilities** e aggiungere il codice seguente:

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

   Come descritto in precedenza, la funzione **getSasToken** orchestra i passaggi di alto livello necessari per preparare il token. La funzione verrà chiamata dal servizio di installazione più avanti in questa esercitazione.

   Le altre due funzioni vengono chiamate dalla funzione **getSasToken:** **sha256HMac** per il calcolo della firma e **urlEncodedString** per la codifica della stringa URL associata. La funzione **urlEncodedString** è necessaria perché non è possibile ottenere l'output richiesto utilizzando la funzione **addingPercentEncoding** incorporata.

   [L'SDK iOS di Archiviazione](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) di Azure è un ottimo esempio di come affrontare queste operazioni in Objective-C.The Azure Storage iOS SDK is an excellent example of how to approach these operations in Objective-C. Ulteriori informazioni sui token di firma di accesso condiviso del bus di servizio di Azure sono disponibili nella [documentazione](../service-bus-messaging/service-bus-sas.md)del bus di servizio di Azure.Further information on Azure Service Bus SAS tokens can be found in the Azure Service Bus documentation .

1. In **AppDelegate.swift**aggiungere il codice seguente alla funzione *didRegisterForRemoteNotificationsWithDeviceToken* per verificare che **TokenUtility.getSasToken** generi un token valido
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Assicurarsi di sostituire i valori segnaposto nella stringa **baseAddress** con

### <a name="verify-the-sas-token"></a>Verificare il token di firma di accesso condivisoVerify the SAS token

Prima di implementare il servizio di installazione nel client, verificare che l'app generi correttamente il token di firma di accesso condiviso usando l'utilità HTTP scelta. Ai fini di questo tutorial, il nostro strumento di scelta sarà **Postman**.

Prendere nota dei valori **installationId** e **token** generati dall'app.

Attenersi alla seguente procedura per chiamare l'API **di installazione:**

1. In **Postman**aprire una nuova scheda.

1. Impostare la richiesta **su GET** e specificare il seguente indirizzo:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configurare le intestazioni della richiesta come indicato di seguito:Configure the request headers as follows:

   | Chiave           | valore            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorizzazione | \<> sasToken       |
   | x-ms-version  | 2015-01          |

1. Selezionare il pulsante **Codice** visualizzato in alto a destra sotto il pulsante **Salva.** La richiesta dovrebbe essere simile all'esempio seguente:The request should look similar to the following example:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Selezionare il pulsante **Invia**.

A questo punto non esiste alcuna registrazione per **l'id di installazione** specificato. La verifica dovrebbe generare una risposta "404 Non trovato" anziché una risposta "401 Non autorizzata". Questo risultato deve confermare che il token di firma di accesso condiviso è stato accettato.

### <a name="implement-the-installation-service-class"></a>Implementare la classe del servizio di installazioneImplement the installation service class

Successivamente si implementerà il wrapper di base per [l'API REST Installazions](/rest/api/notificationhubs/create-overwrite-installation).  

Aggiungere un nuovo file Swift denominato **NotificationRegistrationService.swift** nella cartella **Servizi,** quindi aggiungere il codice seguente a questo file:

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

I dettagli necessari vengono forniti come parte dell'inizializzazione. I tag e i modelli vengono facoltativamente passati alla funzione **register** per far parte del payload JSON **di installazione dispositivi.**  

La funzione **register** chiama le altre funzioni private per preparare la richiesta. Dopo aver ricevuto una risposta, il completamento viene chiamato e indica se la registrazione è stata eseguita correttamente.  

L'endpoint della richiesta viene costruito dalla funzione **getBaseAddress.** La costruzione utilizza lo *spazio dei nomi* e il *nome* dei parametri dell'hub di notifica forniti durante l'inizializzazione.  

La funzione **getSasToken** controlla se il token attualmente archiviato è valido. Se il token non è valido, la funzione chiama **TokenUtility** per generare un nuovo token e quindi lo archivia prima di restituire un valore.

Infine, **encodeToJson** converte i rispettivi oggetti modello in JSON da usare come parte del corpo della richiesta.

### <a name="invoke-the-notification-hubs-rest-api"></a>Richiamare l'API REST degli hub di notificaInvoke the Notification Hubs REST API

L'ultimo passaggio consiste **nell'aggiornamento** di AppDelegate per utilizzare **NotificationRegistrationService** per la registrazione con **NotificationHub**.

1. Aprire **AppDelegate.swift** e aggiungere variabili a livello di classe per archiviare un riferimento a **NoficiationRegistrationService** e **al pushTemplate**generico :

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. Nello stesso file, aggiornare la funzione **didRegisterForRemoteNotificationsWithDeviceToken** per inizializzare **NotificationRegistrationService** con i parametri necessari e quindi chiamare la funzione **register.**

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

    Per semplificare, si intende utilizzare un paio di istruzioni di stampa per aggiornare la finestra di output con il risultato dell'operazione di **registrazione.**

1. A questo punto compilare ed eseguire l'app in un dispositivo fisico. Dovresti vedere "Registrato" nella finestra di output.

## <a name="test-the-solution"></a>testare la soluzione

La nostra app in questa fase è registrata con **NotificationHub** e può ricevere notifiche push. In Xcode arrestare il debugger e chiudere l'app se è attualmente in esecuzione. Successivamente, verificare che i dettagli di **installazione del dispositivo** siano quelli previsti e che l'app possa ora ricevere notifiche push.  

### <a name="verify-the-device-installation"></a>Verificare l'installazione del dispositivo

È ora possibile effettuare la stessa richiesta precedente utilizzando **Postman** per [la verifica del token di firma](#verify-the-sas-token)di accesso condiviso . Supponendo che il token di firma di accesso condiviso non sia scaduto, la risposta dovrebbe ora includere i dettagli di installazione forniti, ad esempio i modelli e i tag.

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

Se il **token di firma** di accesso condiviso precedente è scaduto, è possibile aggiungere un punto di **interruzione** alla **riga 24** della classe **TokenUtility** per ottenere un nuovo **token di** firma di accesso condiviso e aggiornare l'intestazione **Authorization** con il nuovo valore.

### <a name="send-a-test-notification-azure-portal"></a>Inviare una notifica di test (portale di Azure)Send a test notification (Azure portal)

Il modo più rapido per testare che è ora possibile ricevere notifiche consiste nel passare all'hub di notifica nel portale di Azure:The quickest way to test that you can now receive notifications is to browse to the notification hub in the Azure portal:

1. Nel portale di Azure passare alla scheda **Panoramica** nell'hub di notifica.

1. Selezionare **Test Send**, che si trova sopra il riepilogo **di Essentials** nell'angolo superiore sinistro della finestra del portale:

    ![Pulsante Invio test di riepilogo hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Scegliere **Modello personalizzato** dall'elenco **Piattaforme.**

1. Immettere **12345** per **Invia a espressione tag**. Questo tag è stato specificato in precedenza nell'installazione.

1. Facoltativamente, modificare il **messaggio** nel payload JSON:Optionally edit the message in the JSON payload:

    ![Invio test hub di notifica](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selezionare **Invia**. Il portale deve indicare se la notifica è stata inviata correttamente al dispositivo:

    ![Risultati invio test hub di notificaNotification Hubs Test Send Results](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Supponendo che l'app non sia in esecuzione in primo piano, dovresti anche vedere una notifica nel **Centro notifiche** sul dispositivo. Toccando la notifica dovrebbe aprire l'app e visualizzare l'avviso.

    ![Notifica ricevuta Esempio](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Inviare una notifica di prova (Operatore di posta)

È possibile inviare notifiche tramite [l'API REST](/rest/api/notificationhubs/) usando **Postman**, che può essere un modo più pratico per testare.

1. Aprire una nuova scheda in **Postman**.

1. Impostare la richiesta **su POST**e immettere il seguente indirizzo:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configurare le intestazioni della richiesta come indicato di seguito:Configure the request headers as follows:

   | Chiave                            | valore                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | applicazione/json;charset-utf-8 |
   | Autorizzazione                  | \<> sasToken                     |
   | Formato ServiceBusNotification  | template                       |
   | Tag                           | "12345"                        |

1. Configurare la richiesta **BODY** per usare **RAW - JSON (application.json)** con il payload JSON seguente:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selezionare il pulsante **Codice,** che si trova sotto il pulsante **Salva** nella parte superiore destra della finestra. La richiesta dovrebbe essere simile all'esempio seguente:The request should look similar to the following example:

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

1. Selezionare il pulsante **Invia**.

Si dovrebbe ottenere un **201 Creato** codice di stato di successo e ricevere la notifica sul dispositivo client..

## <a name="next-steps"></a>Passaggi successivi
Ora hai un'app Swift iOS di base connessa a un hub di notifica tramite [l'API REST](/rest/api/notificationhubs/) e puoi inviare e ricevere notifiche. Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di Hub di notifica di AzureAzure Notification Hubs overview](notification-hubs-push-notification-overview.md)
- [API REST di Hub di notifica](/rest/api/notificationhubs/)
- [SDK di Hub di notifica per le operazioni di back-endNotification Hubs SDK for back-end operations](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [SDK di Notification Hubs su GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrati con il back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Utilizzo dei tag](notification-hubs-tags-segment-push-message.md) 
- [Utilizzo di modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
- [Controllo dell'accesso al bus di servizio con firme di accesso condivisoService Bus access control with shared access signatures](../service-bus-messaging/service-bus-sas.md)
- [Generazione di token di firma di accesso condiviso a livello di codiceProgrammatically generate SAS tokens](/rest/api/eventhub/generate-sas-token)
- [Sicurezza Apple: crittografia comune](https://developer.apple.com/security/)
- [UnIX Epoca](https://en.wikipedia.org/wiki/Unix_time)
- [Hmac](https://en.wikipedia.org/wiki/HMAC)
