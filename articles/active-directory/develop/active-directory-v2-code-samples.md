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
ms.date: 04/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 51a50693a97a011d00d055eff8d3a0846512bce5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441409"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Esempi di codice di Azure Active Directory (endpoint versione 2)

È possibile usare Microsoft Azure Active Directory (Azure AD) per:

- Aggiungere l'autenticazione e l'autorizzazione alle applicazioni Web e alle API Web.
- Richiedere un token di accesso per accedere a un'API Web protetta.

Questo articolo descrive brevemente gli esempi per l'endpoint versione 2 di Azure AD e fornisce i relativi collegamenti. Questi esempi descrivono la procedura da seguire e riportano frammenti di codice che è possibile usare nelle applicazioni. Nella pagina relativa agli esempi di codice sono disponibili importanti argomenti dettagliati contenenti informazioni sui requisiti e le procedure di installazione e configurazione. I commenti presenti nel codice facilitano la comprensione delle sezioni critiche.

> [!NOTE]
> Per gli esempi della versione 1, vedere [Esempi di codice di Azure AD (endpoint versione 1)](active-directory-code-samples.md).

Per conoscere lo scenario di base di ogni tipo di esempio, vedere [Tipi di app per l'endpoint v2.0 di Azure Active Directory](active-directory-v2-flows.md).

È possibile contribuire agli esempi su GitHub. Per sapere come, vedere [Microsoft Azure Active Directory samples and documentation](https://github.com/Azure-Samples?page=3&query=active-directory) (Esempi e documentazione su Microsoft Azure Active Directory).

## <a name="desktop-and-mobile-public-client-apps"></a>App client pubbliche per dispositivi mobili e desktop

Gli esempi seguenti mostrano applicazioni client pubbliche (per desktop o dispositivi mobili) che accedono a Microsoft Graph o a un'API Web a nome di un utente.

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)      | .NET/C#  | Interattività | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Dispositivi mobili (UWP)   | .NET/C# (UWP) | Interattività | [dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Dispositivi mobili (Android, iOS, UWP)   | .NET/C# (Xamarin) | Interattività | [xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Dispositivi mobili (iOS)       | iOS/Objective C o swift | Interattività | [ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Dispositivi mobili (Android)   | Android/Java | Interattività |   [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Applicazioni Web

Gli esempi seguenti illustrano le applicazioni Web per l'accesso degli utenti, la chiamata a Microsoft Graph o la chiamata a un'API Web con l'identità dell'utente.

 Piattaforma | Esegue solo l'accesso degli utenti | Esegue l'accesso degli utenti e chiama Microsoft Graph 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [aspnet-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Applicazioni daemon

Gli esempi seguenti mostrano applicazioni Web o desktop che accedono a Microsoft Graph o a un'API Web con l'identità di applicazione (senza utente).

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph 
------------------ | -------- | ---------- | -------------------- 
app Web | .NET/C#  | Client Credentials | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Applicazione a pagina singola

Questo esempio illustra come scrivere un'applicazione a singola pagina protetta con Azure AD.

 Piattaforma |  Chiama Microsoft Graph 
 -------- |  --------------------- 
JavaScript (msal.js)  | [javascript-graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [angular-connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [javascript-graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + Angular 4) | [angular4-connect-sample](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>API Web

### <a name="web-api-protected-by-azure-ad"></a>API Web protetta da Azure AD

L'esempio seguente mostra come proteggere un'API Web con l'endpoint versione 2 di Azure AD.

Piattaforma | Esempio | DESCRIZIONE
 -------- | ------------------- | ---------------------
Node.js | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Chiama un'API Web ASP.NET Core da un'applicazione WPF usando Azure AD versione 2.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API Web che chiama Microsoft Graph o un'altra API Web

Questo esempio non è ancora disponibile.

## <a name="other-microsoft-graph-samples"></a>Altri esempi di Microsoft Graph

Per informazioni su [esempi](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) ed esercitazioni che mostrano modelli di utilizzo diversi per l'API Microsoft Graph, tra cui l'autenticazione con Azure AD, vedere [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Esempi ed esercitazioni della community di Microsoft Graph).

## <a name="see-also"></a>Vedere anche 

[Guida per gli sviluppatori di Azure Active Directory](azure-ad-developers-guide.md)

[Concetti e riferimenti relativi all'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Libreria helper dell'API Graph di Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
