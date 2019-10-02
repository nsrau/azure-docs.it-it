---
title: Uso degli URI di reindirizzamento con Microsoft Authentication Library | Microsoft Identity Platform
description: Informazioni sulle differenze tra Microsoft Authentication Library per ObjectiveC (MSAL per iOS e macOS) e Azure AD Authentication Library per ObjectiveC (ADAL.ObjC) e su come eseguire la migrazione tra di essi.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: jak
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a84ea0acc165b236e2dbe17e62a84269a16eedb9
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269289"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Uso degli URI di reindirizzamento con Microsoft Authentication Library per iOS e macOS

Quando un utente esegue l'autenticazione, Azure Active Directory (Azure AD) invia il token all'app usando l'URI di reindirizzamento registrato con l'applicazione Azure AD.

Microsoft Authentication Library (MSAL) richiede che l'URI di reindirizzamento venga registrato con l'app Azure AD in un formato specifico. MSAL usa un URI di reindirizzamento predefinito, se non ne viene specificato uno. Il formato è `msauth.[Your_Bundle_Id]://auth`.

Il formato dell'URI di reindirizzamento predefinito funziona per la maggior parte delle app e degli scenari, tra cui l'autenticazione negoziata e la visualizzazione Web di sistema. Quando possibile, usare il formato predefinito.

Potrebbe tuttavia essere necessario modificare l'URI di reindirizzamento per gli scenari avanzati, come descritto di seguito.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scenari che richiedono un URI di reindirizzamento diverso

### <a name="cross-app-single-sign-on-sso"></a>Accesso Single Sign-On (SSO) tra app

Affinché Microsoft Identity Platform possa condividere i token tra le app, ogni app deve avere lo stesso ID client o ID applicazione. Si tratta dell'identificatore univoco fornito al momento della registrazione dell'app nel portale (non dell'ID bundle dell'applicazione registrato per ogni app con Apple).

Gli URI di reindirizzamento devono essere diversi per ogni app iOS. Questo consente al servizio di gestione delle identità Microsoft di identificare in modo univoco le diverse app che condividono un ID applicazione. Ogni applicazione può avere più URI di reindirizzamento registrati nel portale di Azure. Ogni app della suite avrà un URI di reindirizzamento diverso. Ad esempio:

Data la seguente registrazione dell'applicazione nella portale di Azure:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 usa l'URI di reindirizzamento `msauth.com.contoso.app1://auth` App2 usa `msauth.com.contoso.app2://auth` App3 usa `msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migrazione da ADAL a MSAL

Quando si esegue la migrazione del codice che ha usato Azure AD Authentication Library (ADAL) a MSAL, è possibile che sia già stato configurato un URI di reindirizzamento per l'app. È possibile continuare a usare lo stesso URI di reindirizzamento a condizione che l'app ADAL sia stata configurata per supportare gli scenari negoziati e che l'URI di reindirizzamento soddisfi i requisiti di formato dell'URI di reindirizzamento MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>Requisiti di formato dell'URI di reindirizzamento MSAL

* L'URI di reindirizzamento MSAL deve essere nel formato `<scheme>://host`

    Dove `<scheme>` è una stringa univoca che identifica l'app. Si basa principalmente sull'identificatore del bundle dell'applicazione per garantire l'univocità. Ad esempio, se l'ID bundle dell'app è `com.contoso.myapp`, l'URI di reindirizzamento avrà il formato seguente: `msauth.com.contoso.myapp://auth`.

    Se si sta eseguendo la migrazione da ADAL, l'URI di reindirizzamento avrà probabilmente questo formato: `<scheme>://[Your_Bundle_Id]`, dove `scheme` è una stringa univoca. Questo formato continuerà a funzionare quando si usa MSAL.

* `<scheme>` deve essere registrato nel file Info.plist dell'app in `CFBundleURLTypes > CFBundleURLSchemes`.  In questo esempio, Info.plist è stato aperto come codice sorgente:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

MSAL verificherà se l'URI di reindirizzamento viene registrato correttamente e restituisce un errore in caso contrario.
    
* Se si vogliono usare i collegamenti universali come URI di reindirizzamento, `<scheme>` deve essere `https` e non deve essere dichiarato in `CFBundleURLSchemes`. Al contrario, configurare l'app e il dominio secondo le istruzioni di Apple in [Universal Links for Developers](https://developer.apple.com/ios/universal-links/) e chiamare il metodo `handleMSALResponse:sourceApplication:` di `MSALPublicClientApplication` quando l'applicazione viene aperta tramite un collegamento universale.

## <a name="use-a-custom-redirect-uri"></a>Usare un URI di reindirizzamento personalizzato

Per usare un URI di reindirizzamento personalizzato, passare il parametro `redirectUri` a `MSALPublicClientApplicationConfig` e passare tale oggetto a `MSALPublicClientApplication` quando si inizializza l'oggetto. Se l'URI di reindirizzamento non è valido, l'inizializzatore restituirà `nil` e imposterà `redirectURIError` con informazioni aggiuntive.  Ad esempio:

Objective-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Gestire l'evento URL aperto

L'applicazione deve chiamare MSAL quando riceve una risposta tramite schemi URL o collegamenti universali. Chiamare il metodo `handleMSALResponse:sourceApplication:` di `MSALPublicClientApplication` quando l'applicazione viene aperta. Ecco un esempio di schemi personalizzati:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
