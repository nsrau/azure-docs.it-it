---
title: Esempi di codice per la piattaforma di identità Microsoft | Microsoft Docs
description: Fornisce un indice degli esempi di codice di Microsoft Identity Platform (endpoint v 2.0) disponibili, organizzati in base allo scenario.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f9b6a6139f329a27914eff7a61d0f59c7f48ad
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678062"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Esempi di codice della piattaforma Microsoft Identity (endpoint v 2.0)

È possibile usare Microsoft Identity Platform per:

- Aggiungere l'autenticazione e l'autorizzazione alle applicazioni Web e alle API Web.
- Richiedere un token di accesso per accedere a un'API Web protetta.

Questo articolo descrive brevemente e fornisce collegamenti a esempi per l'endpoint della piattaforma di identità Microsoft. Questi esempi illustrano il modo in cui viene eseguita e forniscono anche frammenti di codice che è possibile usare nelle applicazioni. Nella pagina dell'esempio di codice sono disponibili argomenti Leggimi dettagliati che consentono di soddisfare i requisiti, l'installazione e la configurazione. I commenti nel codice consentono di comprendere le sezioni critiche.

> [!NOTE]
> Se si è interessati agli esempi v 1.0, vedere [esempi di codice Azure ad (endpoint v 1.0)](sample-v1-code.md).

Per comprendere lo scenario di base per ogni tipo di esempio, vedere [tipi di app per l'endpoint della piattaforma Microsoft Identity](v2-app-types.md).

È possibile contribuire agli esempi su GitHub. Per sapere come, vedere [Microsoft Azure Active Directory samples and documentation](https://github.com/Azure-Samples?page=3&query=active-directory) (Esempi e documentazione su Microsoft Azure Active Directory).

## <a name="single-page-applications"></a>Applicazioni a pagina singola

Questi esempi illustrano come scrivere un'applicazione a singola pagina protetta con la piattaforma di identità Microsoft. Questi esempi usano una delle versioni di MSAL. js.

| Piattaforma | Descrizione | Collegamento |
| -------- | --------------------- | -------- |
| ![Questa immagine mostra il logo](media/sample-v2-code/logo_js.png) JavaScript [JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chiama Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Questa immagine mostra il logo](media/sample-v2-code/logo_js.png) JavaScript [JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chiama B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Questa immagine mostra il logo](media/sample-v2-code/logo_js.png) JavaScript [JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chiama l'API Web |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Questa immagine mostra il logo](media/sample-v2-code/logo_angular.png) JavaScript angolare JS [(MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Chiama Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Questa immagine mostra il logo](media/sample-v2-code/logo_angular.png) angolare [JavaScript (angolare MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Chiama Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Applicazioni Web

Gli esempi seguenti illustrano le applicazioni Web che eseguono l'accesso degli utenti. Alcuni esempi illustrano anche l'applicazione che chiama Microsoft Graph o un'API Web con l'identità dell'utente.

| Piattaforma | Esegue solo l'accesso degli utenti | Esegue l'accesso degli utenti e chiama Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | [Esercitazione sugli utenti di WebApp per l'accesso a ASP.NET Core](https://aka.ms/aspnetcore-webapp-sign-in) | Lo stesso esempio in [ASP.NET Core app Web chiama Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fase |
| ![Questa immagine mostra il logo di ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Guida introduttiva di ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp): un'app Web MSAL4J che chiama Microsoft Graph |
| ![Questa immagine mostra il logo node. js](media/sample-v2-code/logo_nodejs.png)  |                   | [Guida introduttiva di Node. js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Questa immagine mostra il logo Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>App client pubbliche per dispositivi mobili e desktop

Gli esempi seguenti mostrano le applicazioni client pubbliche (applicazioni desktop o per dispositivi mobili) che accedono all'API Microsoft Graph o all'API Web nel nome di un utente. Tutte queste applicazioni client utilizzano Microsoft Authentication Library (MSAL).

| Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core 2,0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Desktop (WPF)      | ![Questa immagine mostra il logo .NETC# /](media/sample-v2-code/logo_NET.png) | [interactive](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Desktop (Console)   | ![Questa immagine mostra il logo .NETC# /(desktop)](media/sample-v2-code/logo_NET.png) | [Autenticazione integrata di Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Desktop (Console)   | ![Questa immagine mostra il logo .NETC# /(desktop)](media/sample-v2-code/logo_NETcore.png) | [Username/Password](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Dispositivi mobili (Android, iOS, UWP)   | ![Questa immagine mostra il logo .NETC# /(Novell)](media/sample-v2-code/logo_xamarin.png) | [interactive](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Dispositivi mobili (iOS)       | ![Questa immagine mostra iOS/Objective-C o SWIFT](media/sample-v2-code/logo_iOS.png) | [interactive](msal-authentication-flows.md#interactive) |[iOS-Swift-objc-native-V2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [interactive](msal-authentication-flows.md#interactive) |[macOS-Swift-objc-native-V2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Dispositivi mobili (Android-Java)   | ![Questa immagine mostra il logo Android](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [Android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Dispositivi mobili (Android-Kotlin)   | ![Questa immagine mostra il logo Android](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [Android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Applicazioni daemon

L'esempio seguente mostra un'applicazione che accede all'API Microsoft Graph con la propria identità (senza utente).

| Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Questa immagine mostra il logo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenziali client](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| App Web | ![Questa immagine mostra il logo di ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenziali client](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Applicazioni headless

Gli esempi seguenti mostrano un'applicazione client pubblica eseguite in un dispositivo senza un Web browser. L'app può essere uno strumento da riga di comando, un'app in esecuzione su Linux o Mac o un'applicazione Internet. L'esempio include un'app che accede all'API Microsoft Graph, nel nome di un utente che esegue l'accesso in modo interattivo in un altro dispositivo, ad esempio un telefono cellulare. Questa applicazione client utilizza Microsoft Authentication Library (MSAL).

| Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Desktop (Console)   | ![Questa immagine mostra il logo .NETC# /(desktop)](media/sample-v2-code/logo_NETcore.png) | [Flusso del codice del dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>API Web

Gli esempi seguenti illustrano come proteggere un'API Web con l'endpoint della piattaforma di identità Microsoft e come chiamare un'API downstream dall'API Web.

| Piattaforma | Esempio |
| -------- | ------------------- |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core API Web (servizio) di [DotNet-native-aspnetcore-V2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Questa immagine mostra il logo di ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | API Web (servizio) di [MS-Identity-ASPNET-WebAPI-OnBehalfOf](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Altri esempi di Microsoft Graph

Per informazioni su [esempi](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) ed esercitazioni che mostrano modelli di utilizzo diversi per l'API Microsoft Graph, tra cui l'autenticazione con Azure AD, vedere [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Esempi ed esercitazioni della community di Microsoft Graph).

## <a name="see-also"></a>Vedere anche

- [Guida per gli sviluppatori di Azure Active Directory (v 1.0)](v1-overview.md)
- [Concetti e riferimenti relativi all'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Libreria helper dell'API Graph di Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
