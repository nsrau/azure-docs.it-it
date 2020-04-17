---
title: Personalizzare i browser & WebViews (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Scopri come personalizzare l'esperienza del browser MSAL iOS/macOS per accedere agli utenti.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 3b4362e4c5e69efddfbc99ef0f98ad3c5966165c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450876"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Procedura: personalizzare browser e webview per iOS/macOSHow to: Customize browsers and WebViews for iOS/macOS

Per l'autenticazione interattiva è necessario un browser Web. In iOS e macOS 10.15, Microsoft Authentication Library (MSAL) usa il browser Web di sistema per impostazione predefinita (che potrebbe essere visualizzato nella parte superiore dell'app) per eseguire l'autenticazione interattiva per accedere agli utenti. L'utilizzo del browser di sistema ha il vantaggio di condividere lo stato Single Sign-On (SSO) con altre applicazioni e con le applicazioni web.

È possibile modificare l'esperienza personalizzando la configurazione in altre opzioni per la visualizzazione di contenuto Web, ad esempio:

Solo per iOS:

- [SFAuthenticationSessionSFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Per iOS e macOS:

- [ASWebAuthenticationSession (ASWebAuthenticationSession)](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL per macOS `WKWebView` supporta solo versioni precedenti del sistema operativo. `ASWebAuthenticationSession`è supportato solo su macOS 10.15 e versioni successive. 

## <a name="system-browsers"></a>Browser di sistema

Per `ASWebAuthenticationSession`iOS, `SFAuthenticationSession`, `SFSafariViewController` e sono considerati browser di sistema. Per macOS, `ASWebAuthenticationSession` è disponibile solo. In generale, i browser di sistema condividono cookie e altri dati del sito web con l'applicazione browser Safari.

Per impostazione predefinita, MSAL rileverà dinamicamente la versione iOS e selezionerà il browser di sistema consigliato disponibile in tale versione. Su iOS 12 e `ASWebAuthenticationSession`versioni successive sarà . 

### <a name="default-configuration-for-ios"></a>Configurazione predefinita per iOS

| Versione | Web browser |
|:-------------:|:-------------:|
| iOS 12 e versioni successive | ASWebAuthenticationSession (ASWebAuthenticationSession) |
| iOS 11 | SFAuthenticationSessionSFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Configurazione predefinita per macOS

| Versione | Web browser |
|:-------------:|:-------------:|
| macOS 10.15 | ASWebAuthenticationSession (ASWebAuthenticationSession) |
| altre versioni | WKWebView (visualizzazione di file WKWebView) |

Gli sviluppatori possono anche selezionare un browser di sistema diverso per le applicazioni MSAL:

- `SFAuthenticationSession`è la versione iOS `ASWebAuthenticationSession`11 di .
- `SFSafariViewController`è uno scopo più generale e fornisce un'interfaccia per la navigazione sul web e può essere utilizzato anche per scopi di login. In iOS 9 e 10, i cookie e altri dati del sito web sono condivisi con Safari, ma non in iOS 11 e versioni successive.

## <a name="in-app-browser"></a>Browser in-app

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) è un browser in-app che visualizza contenuto Web. Non condivide cookie o dati del sito web con altre istanze **di WKWebView** o con il browser Safari. WKWebView è un browser multipiattaforma disponibile sia per iOS che per macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Condivisione dei cookie e implicazioni dell'accesso Single Sign-On (SSO)

Il browser che utilizzi influisce sull'esperienza SSO a causa del modo in cui condividono i cookie. Le tabelle seguenti riepilogano le esperienze SSO per browser.

| Tecnologia    | Tipo di browser  | Disponibilità iOS | disponibilità di macOS | Condivide cookie e altri dati  | Disponibilità MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession (ASWebAuthenticationSession)](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Sistema | iOS12 e fino | macOS 10.15 e fino | Sì | iOS e macOS 10.15 | w/ Istanze di Safari
| [SFAuthenticationSessionSFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Sistema | iOS11 e fino in | N/D | Sì | Solo iOS |  w/ Istanze di Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Sistema | iOS11 e fino in | N/D | No | Solo iOS | No**
| **SFSafariViewController** | Sistema | iOS10 | N/D | Sì | Solo iOS |  w/ Istanze di Safari
| **WKWebView (visualizzazione di file WKWebView)**  | In-app | iOS8 e fino in | macOS 10.10 e fino | No | iOS e macOS | No**

Affinché SSO funzioni, i token devono essere condivisi tra le app. Ciò richiede una cache di token o un'applicazione broker, ad esempio Microsoft Authenticator per iOS.This requires a token cache, or broker application, such as Microsoft Authenticator for iOS.

## <a name="change-the-default-browser-for-the-request"></a>Modificare il browser predefinito per la richiesta

Puoi usare un browser in-app o un browser di sistema specifico a `MSALWebviewParameters`seconda dei requisiti dell'esperienza utente, modificando la proprietà seguente in:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Modifica per richiesta interattiva

Ogni richiesta può essere configurata per `MSALInteractiveTokenParameters.webviewParameters.webviewType` eseguire l'override `acquireTokenWithParameters:completionBlock:` del browser predefinito modificando la proprietà prima di passarla all'API.

Inoltre, MSAL supporta il passaggio di un'impostazione personalizzata `WKWebView` impostando la `MSALInteractiveTokenParameters.webviewParameters.customWebView` proprietà.

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

Se si utilizza una visualizzazione Web personalizzata, le notifiche vengono utilizzate per indicare lo stato del contenuto Web visualizzato, ad esempio:

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

Tutti i tipi di browser Web supportati da MSAL sono dichiarati [nell'enumerazione MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

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
