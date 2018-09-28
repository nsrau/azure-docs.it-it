---
title: Esempi di codice di Azure Active Directory | Microsoft Docs
description: Fornisce un indice di esempi di codice di Azure Active Directory (endpoint versione 2) disponibili, organizzati in base allo scenario.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e76097c0d0cbaf14f2fc2b1a407bc2d320a2091d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964402"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Esempi di codice di Azure Active Directory (endpoint versione 2)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

È possibile usare Microsoft Azure Active Directory (Azure AD) per:

- Aggiungere l'autenticazione e l'autorizzazione alle applicazioni Web e alle API Web.
- Richiedere un token di accesso per accedere a un'API Web protetta.

Questo articolo descrive brevemente gli esempi per l'endpoint versione 2 di Azure AD e fornisce i relativi collegamenti. Questi esempi descrivono la procedura da seguire e riportano frammenti di codice che è possibile usare nelle applicazioni. Nella pagina relativa agli esempi di codice sono disponibili importanti argomenti dettagliati contenenti informazioni sui requisiti e le procedure di installazione e configurazione. I commenti presenti nel codice facilitano la comprensione delle sezioni critiche.

> [!NOTE]
> Per gli esempi della versione 1, vedere [Esempi di codice di Azure AD (endpoint versione 1)](sample-v1-code.md).

Per conoscere lo scenario di base di ogni tipo di esempio, vedere [Tipi di app per l'endpoint v2.0 di Azure Active Directory](v2-app-types.md).

È possibile contribuire agli esempi su GitHub. Per sapere come, vedere [Microsoft Azure Active Directory samples and documentation](https://github.com/Azure-Samples?page=3&query=active-directory) (Esempi e documentazione su Microsoft Azure Active Directory).

## <a name="single-page-applications-spa"></a>Applicazione a pagina singola

Questo esempio illustra come scrivere un'applicazione a pagina singola protetta con Azure AD. Questi esempi usano uno dei tipi di MSAL.js: [Microsoft Authentication Library per JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core), [Microsoft Authentication Library per Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular), [Microsoft Authentication Library per AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

 Piattaforma |  Chiama Microsoft Graph
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (msal AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Angular](media/sample-v2-code/logo_angular.png) JavaScript (msal Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Applicazioni Web

Gli esempi seguenti illustrano le applicazioni Web che eseguono l'accesso degli utenti. Alcuni esempi illustrano anche l'applicazione che chiama Microsoft Graph o un'API Web con l'identità dell'utente.

 Piattaforma | Esegue solo l'accesso degli utenti | Esegue l'accesso degli utenti e chiama Microsoft Graph
 -------- | ------------------- | ---------------------------------
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [aspnet-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)

## <a name="desktop-and-mobile-public-client-apps"></a>App client pubbliche per dispositivi mobili e desktop

Gli esempi seguenti mostrano applicazioni client pubbliche (per desktop o dispositivi mobili) che accedono a Microsoft Graph o a un'API Web a nome di un utente, usando l'accesso interattivo. Tutte queste applicazioni client usano MicroSoft Authentication Library (MSAL).

Applicazione client | Piattaforma | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core 2.0
------------------ | -------- |  -------------------- | -------------------------
Desktop (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Dispositivi mobili (UWP)   | ![.NET/C# (UWP)](media/sample-v2-code/logo_windows.png) | [dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Dispositivi mobili (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Dispositivi mobili (iOS)       | ![iOS/Objective C o swift](media/sample-v2-code/logo_iOS.png) | [ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Dispositivi mobili (Android)   | ![Android/Java](media/sample-v2-code/logo_Android.png) |   [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Applicazioni daemon

L'esempio seguente mostra un'applicazione che accede a Microsoft Graph con la propria identità (senza utente).

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph
------------------ | -------- | ---------- | --------------------
app Web | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | Client Credentials | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

> Un esempio che illustra un'applicazione daemon per desktop è nel backlog.

## <a name="web-apis"></a>API Web

L'esempio seguente mostra come proteggere un'API Web con l'endpoint versione 2 di Azure AD. Questa API viene esercitata da un'applicazione WPF (ma potrebbe essere effettivamente chiamata da qualsiasi applicazione)

Piattaforma | Esempio
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Altri esempi di Microsoft Graph

Per informazioni su [esempi](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) ed esercitazioni che mostrano modelli di utilizzo diversi per l'API Microsoft Graph, tra cui l'autenticazione con Azure AD, vedere [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Esempi ed esercitazioni della community di Microsoft Graph).

## <a name="see-also"></a>Vedere anche 

[Guida per gli sviluppatori di Azure Active Directory](azure-ad-developers-guide.md)

[Concetti e riferimenti relativi all'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Libreria helper dell'API Graph di Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
