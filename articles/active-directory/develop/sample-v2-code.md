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
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b518235efa4cd236afd8f79cb70cf30696931fdb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086769"
---
# <a name="azure-active-directory-code-samples-v20-endpoint"></a>Esempi di codice di Azure Active Directory (endpoint v2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

È possibile usare Microsoft Identity Platform per:

- Aggiungere l'autenticazione e l'autorizzazione alle applicazioni Web e alle API Web.
- Richiedere un token di accesso per accedere a un'API Web protetta.

Questo articolo descrive brevemente gli esempi per l'endpoint versione 2.0 di Azure AD e fornisce i relativi collegamenti. Questi esempi descrivono la procedura da seguire e riportano frammenti di codice che è possibile usare nelle applicazioni. Nella pagina relativa agli esempi di codice sono disponibili importanti argomenti dettagliati contenenti informazioni sui requisiti e le procedure di installazione e configurazione. I commenti presenti nel codice facilitano la comprensione delle sezioni critiche.

> [!NOTE]
> Per gli esempi della versione 1.0, vedere [Esempi di codice di Azure AD (endpoint versione 1.0)](sample-v1-code.md).

Per conoscere lo scenario di base di ogni tipo di esempio, vedere [Tipi di app per l'endpoint v2.0 di Azure Active Directory](v2-app-types.md).

È possibile contribuire agli esempi su GitHub. Per sapere come, vedere [Microsoft Azure Active Directory samples and documentation](https://github.com/Azure-Samples?page=3&query=active-directory) (Esempi e documentazione su Microsoft Azure Active Directory).

## <a name="single-page-applications-spa"></a>Applicazione a pagina singola

Questi esempi illustrano come scrivere un'applicazione a pagina singola protetta con Azure AD. In questi esempi viene usato uno dei tipi di MSAL.js:

* [Authentication Library di Microsoft per JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)
* [Authentication Library di Microsoft per Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)
* [Authentication Library di Microsoft per AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

  Piattaforma |  Chiama Microsoft Graph
  -------- |  ---------------------
  ![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
  ![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
  ![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Applicazioni Web

Gli esempi seguenti illustrano le applicazioni Web che eseguono l'accesso degli utenti. Alcuni esempi illustrano anche l'applicazione che chiama Microsoft Graph o un'API Web con l'identità dell'utente.

 Piattaforma | Esegue solo l'accesso degli utenti | Esegue l'accesso degli utenti e chiama Microsoft Graph
 -------- | ------------------- | ---------------------------------
![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.1 | [Esercitazione su ASP.NET Core App Web esegue l'accesso degli utenti](https://aka.ms/aspnetcore-webapp-sign-in) | Stessa di esempio nel [App Web ASP.NET Core chiama Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fase
![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Guida introduttiva di ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p>[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Guida introduttiva di Node. js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)

## <a name="desktop-and-mobile-public-client-apps"></a>App client pubbliche per dispositivi mobili e desktop

Gli esempi seguenti mostrano applicazioni client pubbliche, per desktop o dispositivi mobili, che accedono all'API Microsoft Graph o alla propria API Web a nome di un utente. Tutte queste applicazioni client usano Microsoft Authentication Library (MSAL).

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core 2.0
------------------ | -------- |  ----------| ---------- | -------------------------
Desktop (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | interattivo | [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://github.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Desktop (Console)   | ![.NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | Autenticazione integrata di Windows |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Desktop (Console)   | ![.NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Nome utente/password |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)
Dispositivi mobili (UWP)   | ![.NET/C# (UWP)](media/sample-v2-code/logo_windows.png) | interattivo |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Dispositivi mobili (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | interattivo |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |
Dispositivi mobili (iOS)       | ![iOS/Objective C o swift](media/sample-v2-code/logo_iOS.png) | interattivo |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Dispositivi mobili (Android)   | ![Android/Java](media/sample-v2-code/logo_Android.png) | interattivo |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Applicazioni daemon

L'esempio seguente mostra un'applicazione che accede all'API Microsoft Graph con la propria identità (senza utente).

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph
------------------ | -------- | ---------- | --------------------
Console | ![.NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | Client Credentials | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
app Web | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | Client Credentials | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Applicazioni headless

Gli esempi seguenti mostrano un'applicazione client pubblica eseguite in un dispositivo senza un Web browser. L'app può essere uno strumento da riga di comando o eseguita su Linux/Mac oppure un'applicazione IoT. Gli esempi presentano app che accede all'API Microsoft Graph a nome di un utente che esegue l'accesso in modo interattivo in un altro dispositivo, ad esempio un telefono cellulare. Quest'applicazione client usa MicroSoft Authentication Library (MSAL).

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph
------------------ | -------- |  ----------| ----------
Desktop (Console)   | ![.NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Flusso del codice del dispositivo |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>API Web

L'esempio seguente mostra come proteggere un'API Web con l'endpoint versione 2.0 di Azure AD. Questa API viene esercitata da un'applicazione WPF ma può essere chiamata da qualsiasi applicazione.

Piattaforma | Esempio
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | WebAPI (servizio) di [dotnet-native-aspnetcore-v2](https://github.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Altri esempi di Microsoft Graph

Per informazioni su [esempi](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) ed esercitazioni che mostrano modelli di utilizzo diversi per l'API Microsoft Graph, tra cui l'autenticazione con Azure AD, vedere [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Esempi ed esercitazioni della community di Microsoft Graph).

## <a name="see-also"></a>Vedere anche 

[Guida per gli sviluppatori di Azure Active Directory](v1-overview.md)

[Concetti e riferimenti relativi all'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Libreria helper dell'API Graph di Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
