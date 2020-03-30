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
ms.openlocfilehash: 759f61860c62bcb668db6844df28c52fa28eac80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085915"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Procedura: personalizzare browser e webview per iOS/macOSHow to: Customize browsers and WebViews for iOS/macOS

Per l'autenticazione interattiva è necessario un browser Web. In iOS, Microsoft Authentication Library (MSAL) usa il Web browser di sistema per impostazione predefinita (che potrebbe essere visualizzato nella parte superiore dell'app) per eseguire l'autenticazione interattiva per accedere agli utenti. L'utilizzo del browser di sistema ha il vantaggio di condividere lo stato Single Sign-On (SSO) con altre applicazioni e con le applicazioni web.

È possibile modificare l'esperienza personalizzando la configurazione in altre opzioni per la visualizzazione di contenuto Web, ad esempio:

Solo per iOS:

- [ASWebAuthenticationSession (ASWebAuthenticationSession)](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSessionSFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Per iOS e macOS:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL per macOS `WKWebView`supporta solo .

## <a name="system-browsers"></a>Browser di sistema

Per `ASWebAuthenticationSession`iOS, `SFAuthenticationSession`, `SFSafariViewController` e sono considerati browser di sistema. In generale, i browser di sistema condividono cookie e altri dati del sito web con l'applicazione browser Safari.

Per impostazione predefinita, MSAL rileverà dinamicamente la versione iOS e selezionerà il browser di sistema consigliato disponibile in tale versione. Su iOS 12 e `ASWebAuthenticationSession`versioni successive sarà . 

| Versione | Web browser |
|:-------------:|:-------------:|
| iOS 12 e versioni successive | ASWebAuthenticationSession (ASWebAuthenticationSession) |
| iOS 11 | SFAuthenticationSessionSFAuthenticationSession |
| iOS 10 | SFSafariViewController |

Gli sviluppatori possono anche selezionare un browser di sistema diverso per le applicazioni MSAL:

- `SFAuthenticationSession`è la versione iOS `ASWebAuthenticationSession`11 di .
- `SFSafariViewController`è uno scopo più generale e fornisce un'interfaccia per la navigazione sul web e può essere utilizzato anche per scopi di login. In iOS 9 e 10, i cookie e altri dati del sito web sono condivisi con Safari, ma non in iOS 11 e versioni successive.

## <a name="in-app-browser"></a>Browser in-app

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) è un browser in-app che visualizza contenuto Web. Non condivide cookie o dati del sito web con altre istanze **di WKWebView** o con il browser Safari. WKWebView è un browser multipiattaforma disponibile sia per iOS che per macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Condivisione dei cookie e implicazioni dell'accesso Single Sign-On (SSO)

Il browser che utilizzi influisce sull'esperienza SSO a causa del modo in cui condividono i cookie. Le tabelle seguenti riepilogano le esperienze SSO per browser.

| Tecnologia    | Tipo di browser  | Disponibilità iOS | disponibilità di macOS | Condivide cookie e altri dati  | Disponibilità MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession (ASWebAuthenticationSession)](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | System | iOS12 e fino | macOS 10.15 e fino | Sì | Solo iOS | w/ Istanze di Safari
| [SFAuthenticationSessionSFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | System | iOS11 e fino in | N/D | Sì | Solo iOS |  w/ Istanze di Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | System | iOS11 e fino in | N/D | No | Solo iOS | No**
| **SFSafariViewController** | System | iOS10 | N/D | Sì | Solo iOS |  w/ Istanze di Safari
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
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
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
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
