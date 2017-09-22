---
title: Come utilizzare iOS SDK per App Mobile di Azure
description: Come utilizzare iOS SDK per App Mobile di Azure
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: bd5e2fce31ae597f8ce48fb8f5492e280cbbf28f
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Come usare la libreria client iOS per le app mobili di Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida descrive come eseguire scenari comuni usando il più recente [SDK per iOS per le app per dispositivi mobili di Azure][1]. Se si ha familiarità con le App per dispositivi mobili di Azure, completare innanzitutto [Azure Mobile App Quick Start] per creare un back-end, creare una tabella e scaricare un progetto Xcode iOS preesistente. In questa Guida, l'attenzione è posta sul lato client iOS SDK. Per altre informazioni sull'SDK sul lato server per il back-end, vedere le procedure per l'SDK del server.

## <a name="reference-documentation"></a>Documentazione di riferimento
La documentazione di riferimento per il client SDK per iOS è disponibile qui: [Riferimento al Client iOS di App per dispositivi mobili di Azure][2].

## <a name="supported-platforms"></a>Piattaforme supportate
L'SDK di iOS supporta progetti Objective-C, Swift 2.2 e Swift 2.3 per le versioni iOS 8.0 o successive.

L'autenticazione "flusso server" usa una visualizzazione Web per l'interfaccia utente presentata.  Se il dispositivo non è in grado di presentare un'interfaccia utente con visualizzazione Web, è necessario un altro metodo di autenticazione non incluso nell'ambito del prodotto.  
Questo SDK non è quindi adatto per i dispositivi di tipo controllo o con restrizioni simili.

## <a name="Setup"></a>Installazione e prerequisiti
In questa guida si presuppone che siano stati creati un backend e una tabella. In questa guida si presuppone che la tabella abbia lo stesso schema delle tabelle presenti in tali esercitazioni. In questa guida si presuppone inoltre che nel codice, si faccia riferimento a `MicrosoftAzureMobile.framework` e si importi `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Procedura: creare Client
Per accedere a un back-end di applicazioni per dispositivi mobili di Azure nel progetto, creare un `MSClient`. Sostituire `AppUrl` con l'URL dell'app. È possibile lasciare `gatewayURLString` e `applicationKey` vuoti. Se si configura un gateway per l'autenticazione, popolare `gatewayURLString` con l'URL del gateway.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


## <a name="table-reference"></a>Procedura: Creare un riferimento alla tabella
Per l'accesso o l'aggiornamento dei dati, creare un riferimento alla tabella di back-end. Sostituire `TodoItem` con il nome della tabella

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


## <a name="querying"></a>Procedura: Eseguire query sui dati
Per creare una query di database, eseguire una query sull'oggetto `MSTable` . La query seguente ottiene tutti gli elementi in `TodoItem` e registra il testo di ciascun elemento.

**Objective-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="filtering"></a>Procedura: Filtrare i dati restituiti
Per filtrare i risultati sono disponibili numerose opzioni.

Per filtrare tramite un predicato, usare `NSPredicate` e `readWithPredicate`. I filtri seguenti hanno restituito i dati per trovare solo gli elementi Todo incompleti.

**Objective-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="query-object"></a>Procedura: Usare MSQuery
Per eseguire una query complessa che includa l'ordinamento e il paging, creare un oggetto `MSQuery` , direttamente o tramite un predicato:

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` consente di controllare diversi comportamenti di query.

* Specificare l'ordine dei risultati
* Limitare i campi da restituire
* Limitare il numero di record da restituire
* Specificare il conteggio totale nella risposta
* Specificare i parametri della stringa di query personalizzata nella richiesta
* Applicare funzioni aggiuntive

Eseguire una query `MSQuery` chiamando `readWithCompletion` sull'oggetto.

## <a name="sorting"></a>Procedura: Ordinare i dati con MSQuery
Per ordinare i risultati, verrà ora esaminato un esempio. Per ordinare il campo "text" in ordine crescente, quindi "complete" in ordine decrescente, richiamare `MSQuery` nel modo seguente:

**Objective-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Procedura: Limitare i campi ed espandere i parametri di query di tipo stringa con MSQuery
Per limitare i campi da restituire in una query, specificare i nomi dei campi nella proprietà **selectFields** . Questo esempio restituisce solo i campi text e completed:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Per includere parametri di query di tipo stringa aggiuntivi nella richiesta server, poiché ad esempio sono usati da uno script sul lato server personalizzato, è possibile popolare `query.parameters` come segue:

**Objective-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Procedura: Configurare la dimensione di pagina
Con App per dispositivi mobili di Azure la dimensione di pagina controlla il numero di record che vengono estratti contemporaneamente dalle tabelle di back-end. Una chiamata per eseguire il `pull` dei dati eseguirà il batch sui dati, in base alla dimensione di pagina, fino a quando non ci sono più record da estrarre.

È possibile configurare la dimensione di pagina mediante **MSPullSettings** come illustrato di seguito. La dimensione di pagina predefinita è 50 e l'esempio seguente la cambia in 3.

È possibile configurare una dimensione di pagina diversa per finalità di prestazioni. Se si dispone di un numero elevato di record di dati di piccole dimensioni, una dimensione di pagina elevata riduce il numero di round trip al server.

Questa impostazione controlla la dimensione di pagina solo sul lato client. Se il client richiede una pagina più grande di quella supportata dal back-end di App per dispositivi mobili, la dimensione di pagina è limitata al massimo valore che il back-end può supportare.

Questa impostazione è inoltre il *numero* dei record dei dati, e non la *dimensione in byte*.

Se si aumenta la dimensione di pagina del client, è necessario aumentare anche la dimensione di pagina sul server. Per la procedura da eseguire, vedere ["Procedura: Modificare le dimensioni di pagina delle tabelle"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

**Objective-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```


**Swift**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Procedura: Inserire dati
Per inserire una nuova riga di tabella, creare un elemento `NSDictionary` e richiamare `table insert`. Se [Schema dinamico] è abilitato, il back-end per dispositivi mobili del Servizio app di Azure genera automaticamente nuove colonne in base a `NSDictionary`.

Se non viene specificato il valore `id` , il back-end genera automaticamente un nuovo ID univoco. Specificare il proprio `id` per usare indirizzi e-mail, nomi utente o valori personalizzati come ID. La specifica del proprio ID può semplificare l'esecuzione di join e la logica dei database aziendali.

`result` contiene il nuovo elemento inserito. A seconda della logica del server, può includere dati aggiuntivi o modificati rispetto a quelli passati al server.

**Objective-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>Procedura: Modificare dati
Per aggiornare una riga esistente, modificare un elemento e chiamare `update`:

**Objective-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

In alternativa, fornire l'ID di riga e il campo aggiornato:

**Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Per le operazioni di aggiornamento è necessario che sia impostato almeno l'attributo `id` .

## <a name="deleting"></a>Procedura: Eliminare dati
Per eliminare un elemento, richiamare `delete` con l'elemento:

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

In alternativa, eliminarlo specificando un ID di riga:

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Per le operazioni di eliminazione, è necessario che sia impostato almeno l'attributo `id` .

## <a name="customapi"></a>Procedura: Chiamare un'API personalizzata
Con un'API personalizzata è possibile esporre qualsiasi funzionalità di back-end. Non occorre eseguire il mapping a un'operazione su tabella. In questo modo, non solo si ottiene maggiore controllo sulla messaggistica, ma è anche possibile leggere o impostare le intestazioni e modificare il formato del corpo della risposta. Per informazioni su come creare un'API personalizzata nel back-end, leggere [API personalizzate](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Per chiamare un'API personalizzata, chiamare `MSClient.invokeAPI`. Il contenuto della richiesta e della risposta è in formato JSON. Per utilizzare altri tipi di supporto, [usare l'altro overload di `invokeAPI`][5].  Per eseguire una richiesta `GET` invece di una richiesta `POST`, impostare il parametro `HTTPMethod` su `"GET"` e il parametro `body` su `nil` (dal momento che le richieste GET non hanno corpi dei messaggi). Se l'API personalizzata supporta altri verbi HTTP, modificare `HTTPMethod` in modo appropriato.

**Objective-C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="templates"></a>Procedura: registrare modelli push per inviare notifiche multipiattaforma
Per registrare i modelli, passare modelli con il metodo **client.push registerDeviceToken** nell'app client.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

I modelli sono di tipo NSDictionary e possono contenere più modelli nel formato seguente:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Tutti i tag vengono rimossi dalla richiesta per motivi di sicurezza.  Per aggiungere tag alle istallazione o ai modelli all'interno delle istallazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure][4].  Per inviare notifiche tramite questi modelli registrati, usare le [API di Hub di notifica][3].

## <a name="errors"></a>Procedura: Gestire gli errori
Quando viene eseguita una chiamata a un back-end per dispositivi mobili del Servizio app di Azure, il blocco di completamento contiene un parametro `NSError` . Quando si verifica un errore, il parametro sarà diverso da Nil. In questo caso, è necessario verificare il parametro nel codice e gestire l'errore nel modo appropriato, come dimostrato nei frammenti di codice precedenti.

Il file [`<WindowsAzureMobileServices/MSError.h>`][6] definisce le costanti `MSErrorResponseKey`, `MSErrorRequestKey` e `MSErrorServerItemKey`. Per ottenere più dati relativi all'errore:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Il file definisce anche le costanti per ogni codice di errore:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Procedura: Autenticare gli utenti con Active Directory Authentication Library
È possibile usare Active Directory Authentication Library (ADAL) per far accedere gli utenti all'applicazione tramite Azure Active Directory. È preferibile usare l'autenticazione del flusso client tramite un SDK del provider di identità anziché il metodo `loginWithProvider:completion:` .  L'autenticazione del flusso client garantisce un'esperienza utente più naturale e consente una maggiore personalizzazione.

1. Configurare il back-end dell'app per dispositivi mobili per l'accesso ad Azure Active Directory seguendo l'esercitazione [Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory][7]. Assicurarsi di completare il passaggio facoltativo di registrazione di un'applicazione client nativa. Per iOS è consigliabile che l'URI di reindirizzamento sia nel formato `<app-scheme>://<bundle-id>`. Per altre informazioni, vedere [Guida introduttiva di ADAL iOS][8].
2. Installare ADAL usando Cocoapods. Modificare il podfile includendo la definizione seguente e sostituendo **YOUR-PROJECT** con il nome del progetto Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   e il Pod:

        pod 'ADALiOS'
3. Nel terminale eseguire `pod install` dalla directory contenente il progetto e quindi aprire l'area di lavoro di Xcode generata (non il progetto).
4. Aggiungere il codice seguente all'applicazione, in base al linguaggio usato. In ogni esempio eseguire queste sostituzioni:

   * Sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato eseguito il provisioning dell'applicazione. Il formato deve essere https://login.microsoftonline.com/contoso.onmicrosoft.com. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di Azure classico].
   * Sostituire **INSERT-RESOURCE-ID-HERE** con l'ID client per il back-end dell'app per dispositivi mobili. L'ID client è disponibile nella scheda **Avanzate** in **Impostazioni di Azure Active Directory** nel portale.
   * Sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.
   * Sostituire **INSERT-REDIRECT-URI-HERE** con l'endpoint */.auth/login/done* del sito, usando lo schema HTTPS. Questo valore deve essere simile a *https://contoso.azurewebsites.net/.auth/login/done*.

**Objective-C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**Swift**:

    // add the following imports to your bridging header:
    //        #import <ADALiOS/ADAuthenticationContext.h>
    //        #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>Procedura: Autenticare gli utenti con Facebook SDK for iOS
È possibile usare Facebook SDK for iOS per consentire l'accesso degli utenti all'applicazione tramite Facebook.  È preferibile usare l'autenticazione del flusso client all'uso del metodo `loginWithProvider:completion:` .  L'autenticazione del flusso client garantisce un'esperienza utente più naturale e consente una maggiore personalizzazione.

1. Configurare il back-end dell'app per dispositivi mobili per l'accesso con l'account Facebook seguendo l'esercitazione [Come configurare un'applicazione del servizio App per usare l'account di accesso di Facebook][9].
2. Installare Facebook SDK for iOS secondo le indicazioni della documentazione [Facebook SDK for iOS - Getting Started][10] (Facebook SDK for iOS: guida introduttiva). Anziché creare un'app, è possibile aggiungere la piattaforma iOS alla procedura di registrazione esistente.
3. La documentazione di Facebook include codice Objective-C nel delegato dell'app. Se si usa **Swift**, è possibile utilizzare le seguenti traduzioni per AppDelegate.swift:

        // Add the following import to your bridging header:
        //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }
4. Oltre ad aggiungere `FBSDKCoreKit.framework` al progetto, aggiungere nello stesso modo anche un riferimento a `FBSDKLoginKit.framework`.
5. Aggiungere il codice seguente all'applicazione, in base al linguaggio usato.

**Objective-C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {        
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Swift**:

    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Procedura: Autenticare gli utenti con Twitter Fabric for iOS
È possibile usare Fabric for iOS per consentire l'accesso degli utenti all'applicazione tramite Twitter. L'autenticazione del flusso client è preferibile all'uso del metodo `loginWithProvider:completion:` , perché garantisce un'esperienza utente più naturale e consente una maggiore personalizzazione.

1. Configurare il back-end dell'app per dispositivi mobili per l'accesso con l'account Twitter seguendo l'esercitazione [Come configurare un'applicazione del servizio app per usare l'account di accesso di Twitter](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md) .
2. Aggiungere Fabric al progetto seguendo le indicazioni della documentazione [Fabric for iOS - Getting Started] (Fabric for iOS: guida introduttiva) e configurando TwitterKit.

   > [!NOTE]
   > Per impostazione predefinita, Fabric crea automaticamente un'applicazione Twitter. È possibile evitare di creare un'applicazione registrando la chiave utente e il segreto utente creati in precedenza tramite i frammenti di codice seguenti.    In alternativa, è possibile sostituire i valori relativi alla chiave utente e al segreto utente forniti al servizio app con i valori visualizzati nel [dashboard di Fabric]. Se si sceglie questa opzione, assicurarsi di impostare l'URL di callback su un valore segnaposto, ad esempio `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.
   >
   >

    Se si sceglie di usare i segreti creati in precedenza, aggiungere il codice seguente al delegato dell'app:

    **Objective-C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }

    **Swift**:

        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
3. Aggiungere il codice seguente all'applicazione, in base al linguaggio usato.

**Objective-C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Swift**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Procedura: Autenticare gli utenti con Google Sign-In SDK for iOS
È possibile usare Google Sign-In SDK for iOS per consentire l'accesso degli utenti all'applicazione tramite un account Google.  Google ha annunciato recentemente modifiche ai criteri di sicurezza di OAuth.  Queste modifiche apportate ai criteri richiederanno l'uso di Google SDK in futuro.

1. Configurare il back-end dell'app per dispositivi mobili per l'accesso con l'account Google seguendo l'esercitazione [Come configurare un'applicazione del servizio app per usare l'account di accesso di Google](../app-service/app-service-mobile-how-to-configure-google-authentication.md) .
2. Installare Google SDK for iOS seguendo le istruzioni nel documento [Start integrating Google Sign-In into your iOS app](https://developers.google.com/identity/sign-in/ios/start-integrating) (Iniziare a integrare l'accesso di Google nell'app iOS). È possibile ignorare la sezione relativa all'autenticazione con un server di back-end.
3. Aggiungere quanto segue al metodo `signIn:didSignInForUser:withError:` di delegato, a seconda del linguaggio usato.

**Objective-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };

        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Swift**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

1. Aggiungere anche il codice seguente a `application:didFinishLaunchingWithOptions:` nel delegato dell'app, sostituendo "SERVER_CLIENT_ID" con lo stesso ID usato per configurare il servizio app nel passaggio 1.

**Objective-C**:

         [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";

 **Swift**:

        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"


1. Aggiungere il codice seguente all'applicazione in una classe UIViewController che implementi il protocollo `GIDSignInUIDelegate` , in base al linguaggio usato.  L'utente viene disconnesso prima di accedere nuovamente e anche se non è necessario immettere le credenziali una seconda volta, verrà visualizzata una finestra di dialogo di consenso.  Chiamare questo metodo solo quando il token della sessione è scaduto.

   **Objective-C**:

       #import <Google/SignIn.h>
       // ...
       - (void)authenticate
       {
               [GIDSignIn sharedInstance].uiDelegate = self;
               [[GIDSignIn sharedInstance] signOut];
               [[GIDSignIn sharedInstance] signIn];
        }

   **Swift**:

       // ...
       func authenticate() {
           GIDSignIn.sharedInstance().uiDelegate = self
           GIDSignIn.sharedInstance().signOut()
           GIDSignIn.sharedInstance().signIn()
       }

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Avvio rapido alle app per dispositivi mobili di Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Schema dinamico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[dashboard di Fabric]: https://www.fabric.io/home
[Fabric for iOS - Getting Started]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started

