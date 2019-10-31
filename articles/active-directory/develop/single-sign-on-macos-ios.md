---
title: Configurare SSO in macOS e iOS
titleSuffix: Microsoft identity platform
description: Informazioni su come configurare l'accesso Single Sign-on (SSO) in macOS e iOS.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd944af95f80cf456260beb072c703aab0d15ceb
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175288"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Procedura: configurare l'accesso SSO in macOS e iOS

Microsoft Authentication Library (MSAL) per macOS e iOS supporta l'accesso Single Sign-on (SSO) tra le app e i browser macOS/iOS. In questo articolo vengono illustrati gli scenari SSO seguenti:

- [Accesso SSO invisibile all'utente tra più app](#silent-sso-between-apps)

Questo tipo di SSO funziona tra più app distribuite dallo stesso sviluppatore Apple. Fornisce l'accesso SSO invisibile all'utente, ovvero l'utente non richiede le credenziali, leggendo i token di aggiornamento scritti da altre app dal keychain e scambiando i token di accesso in modo invisibile all'utente.  

- [SSO tramite il broker di autenticazione](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Questo flusso non è disponibile in macOS.

Microsoft fornisce app, denominate broker, che abilitano l'accesso SSO tra le applicazioni di fornitori diversi, purché il dispositivo mobile venga registrato con Azure Active Directory (AAD). Questo tipo di SSO richiede l'installazione di un'applicazione broker sul dispositivo dell'utente.

- **SSO tra MSAL e Safari**

L'accesso SSO viene effettuato tramite la classe [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) . USA lo stato di accesso esistente da altre app e dal browser Safari. Non è limitato alle app distribuite dallo stesso sviluppatore Apple, ma richiede una certa interazione con l'utente.

Se si usa la visualizzazione Web predefinita nell'app per accedere agli utenti, si otterrà l'accesso SSO automatico tra le applicazioni basate su MSAL e Safari. Per altre informazioni sulle visualizzazioni Web supportate da MSAL, vedere [personalizzare browser e WebView](customize-webviews.md).

> [!IMPORTANT]
> Questo tipo di SSO non è attualmente disponibile in macOS. MSAL in macOS supporta solo WKWebView che non dispongono del supporto SSO con Safari. 

- **Accesso SSO invisibile all'utente tra app ADAL e MSAL macOS/iOS**

MSAL Objective-C supporta la migrazione e l'accesso SSO con app basate su ADAL Objective-C. Le app devono essere distribuite dallo stesso sviluppatore Apple.

Vedere [SSO tra le app adal e MSAL in MacOS e iOS](sso-between-adal-msal-apps-macos-ios.md) per istruzioni sull'accesso SSO tra app tra adal e le app basate su MSAL.

## <a name="silent-sso-between-apps"></a>Accesso SSO invisibile all'utente tra le app

MSAL supporta la condivisione SSO tramite i gruppi di accesso a keychain iOS.

Per abilitare l'accesso SSO tra le applicazioni, è necessario eseguire i passaggi seguenti, descritti in modo più dettagliato di seguito:

1. Verificare che tutte le applicazioni usino lo stesso ID client o ID applicazione.
1. Verificare che tutte le applicazioni condividano lo stesso certificato di firma di Apple per poter condividere i portachiavi.
1. Richiedere lo stesso diritto per i portachiavi per ogni applicazione.
1. Informare gli SDK di MSAL sul Keychain condiviso che si vuole usare se è diverso da quello predefinito.

### <a name="use-the-same-client-id-and-application-id"></a>USA lo stesso ID client e ID applicazione

Per individuare le applicazioni che possono condividere i token da parte della piattaforma Microsoft Identity, è necessario che le applicazioni condividano lo stesso ID client o ID applicazione. Si tratta dell'identificatore univoco fornito al momento della registrazione della prima applicazione nel portale.

Il modo in cui la piattaforma di identità Microsoft comunica alle app che usano lo stesso ID applicazione a parte gli **URI di reindirizzamento**. Ogni applicazione può avere più URI di reindirizzamento registrati nel portale di caricamento. Ogni app della suite avrà un URI di reindirizzamento diverso. ad esempio:

URI di reindirizzamento App1: `msauth.com.contoso.mytestapp1://auth` URI di reindirizzamento App2: `msauth.com.contoso.mytestapp2://auth` URI di reindirizzamento App3: `msauth.com.contoso.mytestapp3://auth`

> [!IMPORTANT]
> Il formato degli URI di reindirizzamento deve essere compatibile con il formato supportato da MSAL, descritto in [requisiti del formato dell'URI di reindirizzamento di MSAL](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Configurare la condivisione del keychain tra le applicazioni

Per abilitare la condivisione keychain, vedere l'articolo relativo all' [aggiunta di funzionalità](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) di Apple. Ciò che è importante è che si decida cosa si vuole chiamare il keychain e aggiungere tale funzionalità a tutte le applicazioni che saranno necessarie per l'accesso SSO.

Quando i diritti sono configurati correttamente, nella directory del progetto verrà visualizzato un file di `entitlements.plist` che contiene qualcosa di simile a questo esempio:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Quando il diritto keychain è abilitato in ogni applicazione e si è pronti per usare SSO, configurare `MSALPublicClientApplication` con il gruppo di accesso a keychain come nell'esempio seguente:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"
        
do {
    let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



> [!WARNING]
> Quando si condivide un keychain tra le applicazioni, qualsiasi applicazione può eliminare gli utenti o anche tutti i token nell'applicazione.
> Questa operazione è particolarmente utile se sono presenti applicazioni che si basano su token per eseguire operazioni in background.
> La condivisione di un keychain significa che è necessario prestare molta attenzione quando l'app usa le operazioni di rimozione di Microsoft Identity SDK.

L'operazione è terminata. Microsoft Identity SDK ora condividerà le credenziali tra tutte le applicazioni. L'elenco di account verrà inoltre condiviso tra le istanze dell'applicazione.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO tramite il broker di autenticazione in iOS

MSAL fornisce il supporto per l'autenticazione negoziata con Microsoft Authenticator. Microsoft Authenticator fornisce l'accesso SSO per i dispositivi registrati AAD e consente inoltre all'applicazione di seguire i criteri di accesso condizionale.

La procedura seguente illustra come abilitare SSO usando un broker di autenticazione per l'app:

1. Registrare un formato URI di reindirizzamento compatibile con Service Broker per l'applicazione nel file INFO. plist dell'app. Il formato dell'URI di reindirizzamento compatibile con Service Broker è `msauth.<app.bundle.id>://auth`. Sostituire ' < app. bundle. ID >'' con l'ID bundle dell'applicazione. ad esempio:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Aggiungere gli schemi seguenti al file INFO. plist dell'app in `LSApplicationQueriesSchemes`:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Aggiungere quanto segue al file di `AppDelegate.m` per gestire i callback:

    Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Se si usa Xcode 11**, è necessario inserire il callback di MSAL nel file di `SceneDelegate`.
Se si supportano sia UISceneDelegate che UIApplicationDelegate per la compatibilità con le versioni precedenti di iOS, il callback MSAL deve essere inserito in entrambi i file.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```
    
## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
