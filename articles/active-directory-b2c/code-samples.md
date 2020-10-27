---
title: Esempi di codice di Azure Active Directory B2C| Microsoft Docs
description: Esempi di codice per applicazioni per dispositivi mobili, desktop, Web e a singola pagina di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 2db64a950569e4a96a9a87bb86a5397a17b4f2ba
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92214941"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Esempi di codice di Azure Active Directory B2C

Le tabelle seguenti contengono collegamenti a esempi di applicazioni per diversi ambienti, tra cui iOS, Android, .NET e Node.js.

## <a name="mobile-and-desktop-apps"></a>App per dispositivi mobili e desktop

| Esempio | Descrizione |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Esempio iOS in Swift che autentica gli utenti di Azure Active Directory B2C e chiama un'API mediante OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Semplice app Android che illustra come usare MSAL per autenticare gli utenti tramite Azure Active Directory B2C e accedere a un'API Web con i token risultanti. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Esempio che mostra come usare una libreria di terze parti per compilare un'applicazione iOS in Objective-C che autentica gli utenti di Gestione identità Microsoft nel servizio di gestione delle identità di Azure Active Directory B2C. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Esempio che mostra come usare una libreria di terze parti per compilare un'applicazione Android che autentica gli utenti di Gestione identità Microsoft nel nostro servizio di gestione delle identità B2C e chiama un'API Web tramite i token di accesso OAuth 2.0. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Esempio che illustra come un'applicazione .NET desktop di Windows (WPF) può consentire l'accesso di un utente tramite Azure Active Directory B2C, ottenere un token di accesso tramite MSAL.NET e chiamare un'API. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Semplice app Xamarin.Forms che illustra come usare MSAL per autenticare gli utenti tramite Azure Active Directory B2C e accedere a un'API Web con i token risultanti. |

## <a name="web-apps-and-apis"></a>App Web e API

| Esempio | Descrizione |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Esempio combinato di un'applicazione Web .NET che chiama un'API Web .NET, entrambe protette tramite Azure Active Directory B2C. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | Applicazione Web ASP.NET Core che usa OpenID Connect per consentire l'accesso degli utenti in Azure AD B2C. |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Applicazione Web ASP.NET Core in grado di consentire l'accesso di un utente tramite Azure Active Directory B2C, ottenere un token di accesso tramite MSAL.NET e chiamare un'API. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | App Node.js che consente di configurare in modo rapido e semplice un'applicazione Web con Express usando OpenID Connect. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Piccola API Web Node.js per Azure AD B2C che mostra come proteggere la propria API Web e accettare token di accesso B2C usando passport.js. |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Illustra come integrare il componente B2C di Microsoft Identity Platform con un'applicazione Web Python.  |

## <a name="single-page-apps"></a>App a singola pagina

| Esempio | Descrizione |
|--------| ----------- |
| [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Applicazione a singola pagina (SPA) che chiama un'API Web. L'autenticazione viene eseguita con Azure Active Directory B2C usando MSAL.js. Questo esempio usa il flusso di codice di autorizzazione con PKCE. |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Applicazione a singola pagina (SPA) che chiama un'API Web. L'autenticazione viene eseguita con Azure Active Directory B2C usando MSAL.js. Questo esempio usa il flusso implicito.|

## <a name="saml-test-application"></a>Applicazione di test SAML

| Esempio | Descrizione |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | Applicazione di test SAML per testare Azure AD B2C configurato in modo da fungere da provider di identità SAML. |

## <a name="api-connectors"></a>Connettori API

Le tabelle seguenti forniscono collegamenti a esempi di codice per sfruttare le API Web nei flussi utente usando [connettori API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Argomenti di avvio rapido su Funzioni di Azure per connettori API

| Esempio                                                                                                                          | Descrizione                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Questo esempio di Funzioni di Azure per .NET Core illustra come limitare le iscrizioni a specifici domini di posta elettronica e convalidare le informazioni fornite dall'utente. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Questo esempio di Funzioni di Azure per Node.js illustra come limitare le iscrizioni a specifici domini di posta elettronica e convalidare le informazioni fornite dall'utente.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Questo esempio di Funzioni di Azure per Python illustra come limitare le iscrizioni a specifici domini di posta elettronica e convalidare le informazioni fornite dall'utente.    |