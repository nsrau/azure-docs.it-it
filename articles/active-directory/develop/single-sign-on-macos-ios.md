---
title: Configurare SSO su macOS e iOS
titleSuffix: Microsoft identity platform
description: Scopri come configurare Single Sign-On (SSO) su macOS e iOS.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 91a55520b37c549c8f1d94ba6cf08ecd24db85b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262451"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Procedura: Configurare SSO in macOS e iOSHow to: Configure SSO on macOS and iOS

Microsoft Authentication Library (MSAL) per macOS e iOS supporta Single Sign-On (SSO) tra app e browser macOS/iOS. In questo articolo vengono illustrati i seguenti scenari SSO:

- [SSO silenzioso tra più app](#silent-sso-between-apps)

Questo tipo di SSO funziona tra più app distribuite dallo stesso sviluppatore Apple. Fornisce SSO invisibile all'utente (ovvero, all'utente non vengono richieste le credenziali) leggendo i token di aggiornamento scritti da altre app dal portachiavi e scambiandoli automaticamente per i token di accesso.  

- [SSO tramite il broker di autenticazione](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Questo flusso non è disponibile su macOS.

Microsoft fornisce app, denominate broker, che consentono SSO tra applicazioni di fornitori diversi, purché il dispositivo mobile sia registrato con Azure Active Directory (AAD). Questo tipo di SSO richiede l'installazione di un'applicazione broker sul dispositivo dell'utente.

- **SSO tra MSAL e Safari**

SSO si ottiene tramite la classe [ASWebAuthenticationSession.](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) Utilizza lo stato di accesso esistente da altre app e dal browser Safari. Non è limitato alle app distribuite dallo stesso sviluppatore Apple, ma richiede un'interazione dell'utente.

Se utilizzi la visualizzazione Web predefinita nella tua app per accedere agli utenti, otterrai sSO automatico tra le applicazioni basate su MSAL e Safari. Per ulteriori informazioni sulle visualizzazioni Web supportate da MSAL, vedere [Customize browsers and WebViews](customize-webviews.md).

> [!IMPORTANT]
> Questo tipo di SSO non è attualmente disponibile su macOS. MSAL su macOS supporta solo WKWebView che non dispone del supporto SSO con Safari. 

- **SSO silenzioso tra le app ADAL e MSAL per macOS/iOS**

MSAL Objective-C supporta la migrazione e SSO con le app basate su ADAL Objective-C. Le app devono essere distribuite dallo stesso sviluppatore Apple.

Vedere [SSO tra le app ADAL e MSAL su macOS e iOS](sso-between-adal-msal-apps-macos-ios.md) per istruzioni su SSO tra App a app incrociate tra Le app ADAL e basate su MSAL.

## <a name="silent-sso-between-apps"></a>SSO silenzioso tra le app

MSAL supporta la condivisione SSO tramite gruppi di accesso portachiavi iOS.

Per abilitare SSO tra le applicazioni, è necessario eseguire la procedura seguente, illustrati in dettaglio di seguito:

1. Assicurarsi che tutte le applicazioni utilizzino lo stesso ID client o ID applicazione.
1. Verificare che tutte le applicazioni condividano lo stesso certificato di firma di Apple per poter condividere i portachiavi.
1. Richiedere lo stesso diritto per i portachiavi per ogni applicazione.
1. Comunicare agli SDK di MSAL il portachiavi condiviso che si desidera utilizzare se è diverso da quello predefinito.

### <a name="use-the-same-client-id-and-application-id"></a>Utilizzare lo stesso ID client e lo stesso ID applicazione

Affinché la piattaforma di identità Microsoft sappia quali applicazioni possono condividere i token, tali applicazioni devono condividere lo stesso ID client o ID applicazione. Si tratta dell'identificatore univoco fornito al momento della registrazione della prima applicazione nel portale.

Il modo in cui la piattaforma di identità Microsoft indica alle app che utilizzano lo stesso ID applicazione a parte è tramite i relativi URI di **reindirizzamento.** Ogni applicazione può avere più URI di reindirizzamento registrati nel portale di caricamento. Ogni app della suite avrà un URI di reindirizzamento diverso. Ad esempio:

URI di reindirizzamento dell'app 1: `msauth.com.contoso.mytestapp1://auth`  
URI di reindirizzamento dell'app 2: `msauth.com.contoso.mytestapp2://auth`  
URI di reindirizzamento dell'app 3: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Il formato degli URI di reindirizzamento deve essere compatibile con il formato supportato da MSAL, documentato nei requisiti del [formato URI di reindirizzamento MSAL.](redirect-uris-ios.md#msal-redirect-uri-format-requirements)

### <a name="setup-keychain-sharing-between-applications"></a>Configurazione della condivisione del portachiavi tra le applicazioni

Consulta l'articolo [Aggiunta di funzionalità](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) di Apple per abilitare la condivisione del portachiavi. Ciò che è importante è decidere che cosa si desidera che il portachiavi da chiamare e aggiungere tale funzionalità a tutte le applicazioni che saranno coinvolti in SSO.

Quando i permessi sono impostati correttamente, `entitlements.plist` nella directory del progetto verrà visualizzato un file che contiene un file simile al seguente esempio:

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

#### <a name="add-a-new-keychain-group"></a>Aggiungere un nuovo gruppo di portachiavi

Aggiungere un nuovo gruppo di portachiavi al progetto **Funzionalità**. Il gruppo portachiavi deve essere:
* `com.microsoft.adalcache`su iOS 
* `com.microsoft.identity.universalstorage`su macOS.

![esempio portachiavi](media/single-sign-on-macos-ios/keychain-example.png)

Per ulteriori informazioni, consultate [Gruppi portachiavi.](howto-v2-keychain-objc.md)

## <a name="configure-the-application-object"></a>Configurare l'oggetto applicazione

Una volta abilitato il diritto del portachiavi in ciascuna delle applicazioni e `MSALPublicClientApplication` si è pronti per l'utilizzo di SSO, configurare con il gruppo di accesso portachiavi come nell'esempio seguente:

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
> Quando si condivide un portachiavi tra le applicazioni, qualsiasi applicazione può eliminare utenti o anche tutti i token nell'applicazione.
> Ciò è particolarmente importante se si dispone di applicazioni che si basano su token per eseguire operazioni in background.
> La condivisione di un portachiavi significa che è necessario prestare molta attenzione quando l'app usa Microsoft Identity SDK rimuovere le operazioni.

L'operazione è terminata. Microsoft Identity SDK condividerà ora le credenziali tra tutte le applicazioni. L'elenco degli account verrà condiviso anche tra le istanze dell'applicazione.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO tramite il broker di autenticazione su iOS

MSAL fornisce il supporto per l'autenticazione negoziata con Microsoft Authenticator. Microsoft Authenticator fornisce SSO per i dispositivi registrati AAD e consente inoltre all'applicazione di seguire i criteri di accesso condizionale.

I passaggi seguenti consentono di abilitare SSO usando un broker di autenticazione per l'app:The following steps are how you enable SSO using an authentication broker for your app:

1. Registrare un formato URI di reindirizzamento compatibile con il broker per l'applicazione nel file Info.plist dell'app. Il formato URI di `msauth.<app.bundle.id>://auth`reindirizzamento compatibile con il broker è . Sostituire '<app.bundle.id>'' con l'ID bundle dell'applicazione. Ad esempio:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Aggiungi i seguenti schemi al file Info.plist della tua app in: `LSApplicationQueriesSchemes`

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Aggiungere quanto segue `AppDelegate.m` al file per gestire i callback:

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
    
**Se si utilizza Xcode 11**, è necessario `SceneDelegate` inserire il callback MSAL nel file.
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