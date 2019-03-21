---
title: Esempi di codice di Azure Active Directory | Microsoft Docs
description: Fornisce un indice di esempi di codice di Azure Active Directory (endpoint v1.0), organizzati in base allo scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c199df2012e6ce3b3de5f34989cf5cbc135fdc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101024"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Esempi di codice di Azure Active Directory (endpoint v1.0)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Microsoft Azure Active Directory (Azure AD) consente di aggiungere funzionalità di autenticazione e autorizzazione alle applicazioni e alle API Web.

Questa sezione riporta collegamenti a esempi che è possibile usare per ottenere altre informazioni relative all'endpoint v1.0 di Azure AD. Questi esempi descrivono la procedura da seguire e riportano frammenti di codice che è possibile usare nelle applicazioni. Nella pagina relativa agli esempi di codice sono disponibili argomenti readme dettagliati contenenti informazioni sui requisiti e le procedure di installazione e configurazione. Il codice è inoltre accompagnato da commenti per facilitare la comprensione delle sezioni critiche.

> [!NOTE]
> Se si è interessati a esempi di codice di Azure AD versione 2, vedere [Esempi di codice per scenario per la versione 2.0](sample-v2-code.md).

Per comprendere lo scenario di base di ogni tipo di esempio, vedere [Scenari di autenticazione per Azure AD](authentication-scenarios.md).

È possibile contribuire agli esempi su GitHub. Per sapere come, vedere [Microsoft Azure Active Directory samples and documentation](https://github.com/Azure-Samples?page=3&query=active-directory) (Esempi e documentazione su Microsoft Azure Active Directory).

## <a name="single-page-applications"></a>Applicazioni a pagina singola

Questo esempio illustra come scrivere un'applicazione a pagina singola protetta con Azure AD.

 Piattaforma | Chiama la propria API | Chiama un'altra API Web
 -------- |  --------------------- | ------------------ 
![JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Applicazioni Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Applicazioni Web che eseguono l'accesso degli utenti, chiamano Microsoft Graph o un'API Web con l'identità dell'utente

Gli esempi seguenti illustrano le applicazioni Web che eseguono l'accesso degli utenti. Alcune di queste applicazioni chiamano anche Microsoft Graph o un'API Web a nome dell'utente connesso.

 Piattaforma | Esegue solo l'accesso degli utenti | Chiama Microsoft Graph o AAD Graph| Chiama un'altra API Web ASP.NET o ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (AAD Graph) |
![Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Php](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Applicazioni Web che dimostrano il controllo degli accessi in base al ruolo (autorizzazione)

Gli esempi seguenti descrivono come implementare il controllo degli accessi in base al ruolo, usato per limitare le autorizzazioni di determinate funzionalità in un'applicazione Web a determinati utenti. Gli utenti vengono autorizzati in base al fatto che appartengano a un **gruppo di Azure AD** o abbiano un determinato **ruolo** applicazione.

Piattaforma | Esempio |
 -------- | ------------------- |
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Un'app Web MVC .NET 4.5 che usa **ruoli** di Azure AD per l'autorizzazione

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Applicazioni client pubbliche per desktop e dispositivi mobili che chiamano Microsoft Graph o un'API Web

Gli esempi seguenti illustrano applicazioni client pubbliche (per desktop o dispositivi mobili) che accedono a Microsoft Graph o a un'API Web a nome di un utente. A seconda dei dispositivi e delle piattaforme, le applicazioni possono consentire l'accesso degli utenti con modalità diverse (flussi/concessioni): 

- in modo interattivo
- in modo automatico (con l'autenticazione integrata di Windows in Windows o tramite nome utente/password) 
- o addirittura delegando l'accesso interattivo a un altro dispositivo (flusso del codice del dispositivo usato in dispositivi che non forniscono controlli Web).

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core 2.x o ASP.NET
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | ![.NET/C#](media/sample-v2-code/logo_NET.png)  | Interattività | Parte di [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Dispositivi mobili (UWP)            | .![.NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interattività | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Questo esempio usa [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager), non [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (applicazione UWP che usa ADAL.NET per chiamare un'API Web a tenant singolo) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (applicazione UWP che usa ADAL.NET per chiamare un'API Web multi-tenant)|
Dispositivi mobili (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interattività | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Dispositivi mobili (Android)           | ![Android/Java](media/sample-v2-code/logo_Android.png) | Interattività |   [android](https://github.com/Azure-Samples/active-directory-android) |
Dispositivi mobili (iOS)           | ![iOS/Objective C o swift](media/sample-v2-code/logo_iOS.png) | Interattività |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Desktop (Console)          | ![.NET / C#](media/sample-v2-code/logo_NET.png) | Nome utente/password </p>  Autenticazione integrata di Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Desktop (Console)          | ![Console Java](media/sample-v2-code/logo_Java.png) | Nome utente/password | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Desktop (Console)           | ![.NET Core/C#](media/sample-v2-code/logo_NETcore.png) | Flusso del codice del dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Applicazioni daemon (che accedono ad API Web con l'identità dell'applicazione)

Gli esempi seguenti mostrano applicazioni Web o desktop che accedono a Microsoft Graph o a un'API Web senza utente (con l'identità dell'applicazione).

Applicazione client | Piattaforma | Flusso/Concessione | Chiama un'API Web ASP.NET Core 2.0 o ASP.NET
------------------ | -------- | ---------- | -------------------- 
App daemon (Console)          | ![.NET](media/sample-v2-code/logo_NETframework.png) | Credenziali del client con certificato o segreto dell'app | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
App daemon (Console)         | ![.NET](media/sample-v2-code/logo_NETcore.png) | Credenziali del client con certificato| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
App Web ASP.NET  | ![.NET](media/sample-v2-code/logo_NETframework.png) | Credenziali del client | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>API Web

### <a name="web-api-protected-by-azure-active-directory"></a>API Web protetta da Azure Active Directory

L'esempio seguente mostra come proteggere un'API Web node.js con Azure AD.

Nelle sezioni precedenti di questo articolo sono disponibili anche altri esempi di un'applicazione client che **chiama** un'**API Web** ASP.NET o ASP.NET Core. Questi esempi non sono menzionati nuovamente in questa sezione, ma saranno disponibili nell'ultima colonna delle tabelle riportate in precedenza o di seguito.

Piattaforma | Esempio
 -------- | -------------------
![Php](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API Web che chiama Microsoft Graph o un'altra API Web

I seguenti esempi mostrano un'API Web che chiama un'altra API Web. Il secondo esempio mostra come gestire l'accesso condizionale.

 Piattaforma |  Chiama Microsoft Graph | Chiama un'altra API Web ASP.NET o ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>Altri esempi di Microsoft Graph

Per esempi ed esercitazioni che mostrano modelli di utilizzo diversi per l'API Microsoft Graph, tra cui l'autenticazione con Azure AD, vedere [gli esempi e le esercitazioni della community di Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Vedere anche 

[Guida per gli sviluppatori di Azure Active Directory](v1-overview.md)

[Librerie di autenticazione di Azure Active Directory](active-directory-authentication-libraries.md)

[Concetti e riferimenti relativi all'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Libreria helper dell'API Graph di Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
