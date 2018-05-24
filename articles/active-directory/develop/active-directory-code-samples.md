---
title: Esempi di codice di Azure Active Directory | Microsoft Docs
description: Fornisce un indice di esempi di codice di Azure Active Directory (endpoint versione 1), organizzati in base allo scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 299e0d4fa53f9b8a2aef2fc881b136aa41aacfe4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157776"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Esempi di codice di Azure Active Directory (endpoint versione 1)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) consente di aggiungere funzionalità di autenticazione e autorizzazione alle applicazioni e alle API Web.

Questa sezione riporta collegamenti a esempi che è possibile usare per altre informazioni relative all'endpoint di Azure AD versione 1. Questi esempi descrivono la procedura da seguire e riportano frammenti di codice che è possibile usare nelle applicazioni. Nella pagina relativa agli esempi di codice sono disponibili argomenti readme dettagliati contenenti informazioni sui requisiti e le procedure di installazione e configurazione. Il codice è inoltre accompagnato da commenti per facilitare la comprensione delle sezioni critiche.

> [!NOTE]
> Se si è interessati a esempi di codice di Azure AD versione 2, vedere [Esempi di codice per scenario per la versione 2.0](active-directory-v2-code-samples.md).

Per comprendere lo scenario di base di ogni tipo di esempio, vedere [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).

È possibile contribuire agli esempi su GitHub. Per sapere come, vedere [Esempi e documentazione su Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Applicazioni client pubbliche per desktop e dispositivi mobili che chiamano Microsoft Graph o un'API Web

Gli esempi seguenti mostrano applicazioni client pubbliche (per desktop o dispositivi mobili) che accedono a Microsoft Graph o un'API Web a nome di un utente.

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core 2.0 o ASP.NET
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | .NET / C# | Interattività | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Dispositivi mobili (UWP)            | .NET / C#  | Interattività | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (API Web tenant singolo) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (API Web multi-tenant)|
Dispositivi mobili (Android, iOS, UWP)   | .NET/C# (Xamarin) | Interattività | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Dispositivi mobili (Android)           | Android/Java | Interattività |   [android](https://github.com/Azure-Samples/active-directory-android) |
Dispositivi mobili (iOS)           | iOS/Objective C | Interattività |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Desktop (Console)          | .NET / C# | Nome utente/password </p> Autenticazione integrata di Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Desktop (Console)           | .NET Core/C# | Profilo del dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Applicazioni Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Applicazioni Web che eseguono l'accesso degli utenti, chiamano Microsoft Graph o un'API Web con l'identità dell'utente

 Piattaforma | Esegue solo l'accesso degli utenti | Chiama Microsoft Graph o AAD Graph| Chiama un'altra API Web ASP.NET o ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Applicazioni Web che dimostrano il controllo degli accessi in base al ruolo (autorizzazione)

Gli esempi seguenti descrivono come implementare il controllo degli accessi in base al ruolo, che consente di limitare le autorizzazioni di determinate funzionalità di un'applicazione Web a determinati utenti. Gli utenti vengono autorizzati in base alla loro appartenenza a un gruppo o a un ruolo di Azure AD.

Piattaforma | Esempio | DESCRIZIONE
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Un'app Web MVC .NET 4.5 che usa **gruppi** di Azure AD per l'autorizzazione
ASP.NET 4.5 | [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Un'app Web MVC .NET 4.5 che usa **ruoli** di Azure AD per l'autorizzazione

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Applicazioni daemon (che accedono ad API Web con l'identità dell'applicazione)

Gli esempi seguenti mostrano applicazioni Web o desktop che accedono a Microsoft Graph o a un'API Web senza utente (con l'identità dell'applicazione).

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core 2.0 o ASP.NET
------------------ | -------- | ---------- | -------------------- | -------------------------
App daemon (Console)          | .NET / C#  | Credenziali del client con certificato o segreto dell'app | | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
App daemon (Console)         | .NET Core/C# | Credenziali del client con certificato| | [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Desktop            | Java | Credenziali del client |   [java-native-headless](https://github.com/azure-samples/active-directory-java-native-headless) |
App Web ASP.NET  | .NET / C# | Credenziali del client |    | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>API Web

### <a name="web-api-protected-by-azure-active-directory"></a>API Web protetta da Azure Active Directory

L'esempio seguente mostra come proteggere un'API Web node.js con Azure AD.

Piattaforma | Esempio | DESCRIZIONE
 -------- | ------------------- | ---------------------
Node.js | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  API Web NodeJS protetta con Azure AD e i token di accesso OAuth 2.0.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API Web che chiama Microsoft Graph o un'altra API Web

I seguenti esempi mostrano un'API Web che chiama un'altra API Web. Il secondo esempio mostra come gestire l'accesso condizionale.

 Piattaforma |  Chiama Microsoft Graph | Chiama un'altra API Web ASP.NET o ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Applicazione a singola pagina

Questo esempio illustra come scrivere un'applicazione a singola pagina protetta con Azure AD.

 Piattaforma |  Chiama Microsoft Graph | Chiama la propria API
 -------- |  --------------------- | -------------------------
JavaScript (Angular) / ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)

## <a name="other-microsoft-graph-samples"></a>Altri esempi di Microsoft Graph

Per esempi ed esercitazioni che mostrano modelli di utilizzo diversi per l'API Microsoft Graph, tra cui l'autenticazione con Azure AD, vedere [gli esempi e le esercitazioni della community di Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Vedere anche 

[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Concetti e riferimenti relativi all'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Libreria helper dell'API Graph di Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
