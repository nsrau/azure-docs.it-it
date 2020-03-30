---
title: Esempi di codice per Azure Active Directory v1.0 Documenti Microsoft
description: Fornisce un indice di esempi di codice di Azure Active Directory (endpoint v1.0), organizzati in base allo scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ae283529abb3b71ee50fc710dd1ebe0d17a12be0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154832"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Esempi di codice di Azure Active Directory (endpoint v1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Active Directory (Azure AD) consente di aggiungere funzionalità di autenticazione e autorizzazione alle applicazioni e alle API Web.

Questa sezione riporta collegamenti a esempi che è possibile usare per ottenere altre informazioni relative all'endpoint v1.0 di Azure AD. Questi esempi descrivono la procedura da seguire e riportano frammenti di codice che è possibile usare nelle applicazioni. Nella pagina relativa agli esempi di codice sono disponibili argomenti readme dettagliati contenenti informazioni sui requisiti e le procedure di installazione e configurazione. Il codice è inoltre accompagnato da commenti per facilitare la comprensione delle sezioni critiche.

> [!NOTE]
> Se si è interessati a esempi di codice di Azure AD versione 2, vedere [Esempi di codice per scenario per la versione 2.0](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Per comprendere lo scenario di base di ogni tipo di esempio, vedere [Scenari di autenticazione per Azure AD](v1-authentication-scenarios.md).

È possibile contribuire agli esempi su GitHub. Per sapere come, vedere [Microsoft Azure Active Directory samples and documentation](https://github.com/Azure-Samples?page=3&query=active-directory) (Esempi e documentazione su Microsoft Azure Active Directory).

## <a name="single-page-applications"></a>Applicazioni a pagina singola

Questo esempio illustra come scrivere un'applicazione a pagina singola protetta con Azure AD.

 Piattaforma | Chiama la propria API | Chiama un'altra API Web
 -------- |  --------------------- | ------------------ 
![Questa immagine mostra il logo JavaScript](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Questa immagine mostra il logo Angular JS](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Applicazioni Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Applicazioni Web che eseguono l'accesso degli utenti, chiamano Microsoft Graph o un'API Web con l'identità dell'utente

Gli esempi seguenti illustrano le applicazioni Web che eseguono l'accesso degli utenti. Alcune di queste applicazioni chiamano anche Microsoft Graph o un'API Web a nome dell'utente connesso.

 Piattaforma | Esegue solo l'accesso degli utenti | Chiama Microsoft Graph | Chiama un'altra API Web ASP.NET o ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
![Questa immagine mostra il logo Python](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Questa immagine mostra il registro Java](media/sample-v2-code/logo-java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Questa immagine mostra il logo PHP](media/sample-v2-code/logo-php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Applicazioni Web che dimostrano il controllo degli accessi in base al ruolo (autorizzazione)

Gli esempi seguenti descrivono come implementare il controllo degli accessi in base al ruolo, usato per limitare le autorizzazioni di determinate funzionalità in un'applicazione Web a determinati utenti. Gli utenti vengono autorizzati in base al fatto che appartengano a un **gruppo di Azure AD** o abbiano un determinato **ruolo** applicazione.

Piattaforma | Esempio |
 -------- | ------------------- |
![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Un'app Web MVC .NET 4.5 che usa **ruoli** di Azure AD per l'autorizzazione

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Applicazioni client pubbliche per desktop e dispositivi mobili che chiamano Microsoft Graph o un'API Web

Negli esempi seguenti vengono illustrate le applicazioni client pubbliche (applicazioni deskto/pmobile) che accedono a Microsoft Graph o a un'API Web a nome di un utente. A seconda dei dispositivi e delle piattaforme, le applicazioni possono consentire l'accesso degli utenti con modalità diverse (flussi/concessioni):

- In modo interattivo
- In silenzio (con autenticazione integrata di Windows in Windows o nome utente/password)
- Delegando l'accesso interattivo a un altro dispositivo (flusso di codice del dispositivo usato nei dispositivi che non forniscono controlli Web)By delegating the interactive sign-in to another device (device code flow used on devices don't provide web controls)

Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core 2.x o ASP.NET
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | ![In questa immagine viene illustrato il logo .NET/C](media/sample-v2-code/logo-net.png)  | Interattività | Parte di [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Dispositivi mobili (UWP)            | .![In questa immagine viene illustrata la .NET/C](media/sample-v2-code/logo-windows.png)   | Interattività | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Questo esempio usa [WAM](/windows/uwp/security/web-account-manager), non [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (applicazione UWP che usa ADAL.NET per chiamare un'API Web a tenant singolo) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (applicazione UWP che usa ADAL.NET per chiamare un'API Web multi-tenant)|
Dispositivi mobili (Android, iOS, UWP)   | ![In questa immagine viene illustrato il file .NET/C](media/sample-v2-code/logo-xamarin.png) | Interattività | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Dispositivi mobili (Android)           | ![Questa immagine mostra il logo Android](media/sample-v2-code/logo-android.png) | Interattività |   [android](https://github.com/Azure-Samples/active-directory-android) |
Dispositivi mobili (iOS)           | ![Questa immagine mostra iOS / Obiettivo C o Swift](media/sample-v2-code/logo-ios.png) | Interattività |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Desktop (Console)          | ![In questa immagine viene illustrato il logo .NET/C](media/sample-v2-code/logo-net.png) | Nome utente/password </p>  Autenticazione integrata di Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Desktop (Console)          | ![Questa immagine mostra il logo Java](media/sample-v2-code/logo-java.png) | Nome utente/password | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Desktop (Console)           | ![In questa immagine viene illustrato il logo di .NET Core/C](media/sample-v2-code/logo-netcore.png) | Flusso del codice del dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Applicazioni Daemon (accesso alle API Web con l'identità dell'applicazione)

Gli esempi seguenti mostrano applicazioni Web o desktop che accedono a Microsoft Graph o a un'API Web senza utente (con l'identità dell'applicazione).

Applicazione client | Piattaforma | Flusso/Concessione | Chiama un'API Web ASP.NET Core 2.0 o ASP.NET
------------------ | -------- | ---------- | -------------------- 
App daemon (Console)          | ![Questa immagine mostra il logo .NET](media/sample-v2-code/logo-netframework.png) | Credenziali del client con certificato o segreto dell'app | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
App daemon (Console)         | ![Questa immagine mostra il logo .NET](media/sample-v2-code/logo-netcore.png) | Credenziali del client con certificato| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
App Web ASP.NET  | ![Questa immagine mostra il logo .NET](media/sample-v2-code/logo-netframework.png) | Credenziali del client | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>API Web

### <a name="web-api-protected-by-azure-active-directory"></a>API Web protetta da Azure Active Directory

L'esempio seguente mostra come proteggere un'API Web node.js con Azure AD.

Nelle sezioni precedenti di questo articolo sono disponibili anche altri esempi di un'applicazione client che **chiama** un'**API Web** ASP.NET o ASP.NET Core. Questi esempi non sono menzionati nuovamente in questa sezione, ma saranno disponibili nell'ultima colonna delle tabelle riportate in precedenza o di seguito.

| Piattaforma | Esempio |
|--------|-------------------|
| ![Questa immagine mostra il logo Node.js](media/sample-v2-code/logo-nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API Web che chiama Microsoft Graph o un'altra API Web

I seguenti esempi mostrano un'API Web che chiama un'altra API Web. Nel secondo esempio viene illustrato come gestire l'accesso condizionale.

| Piattaforma |  Chiama Microsoft Graph | Chiama un'altra API Web ASP.NET o ASP.NET Core 2.0 |
| -------- |  --------------------- | ------------------------- |
| ![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Altri esempi di Microsoft Graph

Per esempi ed esercitazioni che mostrano modelli di utilizzo diversi per l'API Microsoft Graph, tra cui l'autenticazione con Azure AD, vedere [gli esempi e le esercitazioni della community di Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Vedere anche

- [Azure Active Directory Developer's Guide](v1-overview.md)
- [Librerie di autenticazione di Azure Active Directory](active-directory-authentication-libraries.md)
- [Concettuale e di riferimento dell'API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)
