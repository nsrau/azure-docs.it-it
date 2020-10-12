---
title: Personalizzare i browser & Webviews (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come personalizzare l'esperienza del browser iOS/macOS MSAL per l'accesso degli utenti.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: a8486ec87b5198231a33b1dab382ba457c8c8066
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478128"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Procedura: personalizzare browser e WebView per iOS/macOS

Per l'autenticazione interattiva è necessario un Web browser. In iOS e macOS 10.15 +, Microsoft Authentication Library (MSAL) usa il Web browser di sistema per impostazione predefinita (che potrebbe apparire sopra l'app) per eseguire l'autenticazione interattiva per l'accesso degli utenti. L'utilizzo del browser di sistema offre il vantaggio di condividere lo stato di Single Sign-on (SSO) con altre applicazioni e con applicazioni Web.

È possibile modificare l'esperienza personalizzando la configurazione con altre opzioni per la visualizzazione del contenuto Web, ad esempio:

Solo per iOS:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Per iOS e macOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL per macOS supporta solo le `WKWebView` versioni precedenti del sistema operativo. `ASWebAuthenticationSession` è supportato solo in macOS 10,15 e versioni successive. 

## <a name="system-browsers"></a>Browser di sistema

Per iOS, `ASWebAuthenticationSession` , `SFAuthenticationSession` e `SFSafariViewController` sono considerati browser di sistema. Per macOS `ASWebAuthenticationSession` è disponibile solo. In generale, i browser di sistema condividono i cookie e altri dati del sito Web con l'applicazione Safari browser.

Per impostazione predefinita, MSAL rileverà in modo dinamico la versione di iOS e selezionerà il browser di sistema consigliato disponibile in tale versione. In iOS 12 + sarà `ASWebAuthenticationSession` . 

### <a name="default-configuration-for-ios"></a>Configurazione predefinita per iOS

| Versione | Web browser |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Configurazione predefinita per macOS

| Versione | Web browser |
|:-------------:|:-------------:|
| macOS 10.15 + | ASWebAuthenticationSession |
| Altre versioni | WKWebView |

Gli sviluppatori possono anche selezionare un browser di sistema diverso per le app MSAL:

- `SFAuthenticationSession` è la versione iOS 11 di `ASWebAuthenticationSession` .
- `SFSafariViewController` è più generico e fornisce un'interfaccia per l'esplorazione del Web e può essere usato anche per scopi di accesso. In iOS 9 e 10 i cookie e altri dati del sito Web vengono condivisi con Safari, ma non in iOS 11 e versioni successive.

## <a name="in-app-browser"></a>Browser in-app

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) è un browser in-app che Visualizza contenuto Web. Non condivide i cookie o i dati del sito Web con altre istanze di **WKWebView** o con il browser Safari. WKWebView è un browser multipiattaforma disponibile sia per iOS che per macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Implicazioni di condivisione di cookie e Single Sign-on (SSO)

Il browser utilizzato influisca sull'esperienza SSO grazie alla modalità di condivisione dei cookie. Nelle tabelle seguenti vengono riepilogate le esperienze SSO per ogni browser.

| Tecnologia    | Tipo di browser  | disponibilità di iOS | disponibilità di macOS | Condivide i cookie e altri dati  | Disponibilità MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Sistema | iOS12 | macOS 10,15 e su | Sì | iOS e macOS 10.15 + | istanze di w/Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Sistema | iOS11 | N/D | Sì | Solo iOS |  istanze di w/Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Sistema | iOS11 | N/D | No | Solo iOS | No**
| **SFSafariViewController** | Sistema | iOS10 | N/D | Sì | Solo iOS |  istanze di w/Safari
| **WKWebView**  | In-app | iOS 8 | macOS 10,10 e su | No | iOS e macOS | No**

* * Per il funzionamento dell'accesso SSO, è necessario condividere i token tra le app. Questa operazione richiede una cache di token o un'applicazione broker, ad esempio Microsoft Authenticator per iOS.

## <a name="change-the-default-browser-for-the-request"></a>Modificare il browser predefinito per la richiesta

È possibile usare un browser in-app o un browser di sistema specifico in base ai requisiti dell'esperienza utente, modificando la proprietà seguente in `MSALWebviewParameters` :

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Modifica per richiesta interattiva

Ogni richiesta può essere configurata in modo da eseguire l'override del browser predefinito modificando la `MSALInteractiveTokenParameters.webviewParameters.webviewType` proprietà prima di passarla all' `acquireTokenWithParameters:completionBlock:` API.

Inoltre, MSAL supporta il passaggio di un oggetto personalizzato impostando `WKWebView` la `MSALInteractiveTokenParameters.webviewParameters.customWebView` Proprietà.

Ad esempio:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Se si usa una visualizzazione Web personalizzata, le notifiche vengono usate per indicare lo stato del contenuto Web visualizzato, ad esempio:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Opzioni

Tutti i tipi di Web browser supportati da MSAL sono dichiarati nell' [enumerazione MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
