---
title: Esempi di codice per Microsoft Identity Platform
description: Fornisce un indice di esempi di codice per Microsoft Identity Platform (endpoint versione 2.0) disponibili, organizzati in base allo scenario.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 06/01/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 948eea168b071813cf20e03d3a0744a824fd4c8c
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91773893"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Esempi di codice per Microsoft Identity Platform (endpoint versione 2.0)

È possibile usare Microsoft Identity Platform per:

- Aggiungere l'autenticazione e l'autorizzazione alle applicazioni Web e alle API Web.
- Richiedere un token di accesso per accedere a un'API Web protetta.

Questo articolo descrive brevemente gli esempi per l'endpoint Microsoft Identity Platform e fornisce i relativi collegamenti. Questi esempi descrivono la procedura da seguire e riportano frammenti di codice che è possibile usare nelle applicazioni. Nella pagina relativa agli esempi di codice sono disponibili argomenti dettagliati contenenti informazioni sui requisiti e le procedure di installazione e configurazione. I commenti presenti nel codice facilitano la comprensione delle sezioni critiche.

> [!NOTE]
> Per gli esempi della versione 1.0, vedere [Esempi di codice di Azure AD (endpoint versione 1.0)](../azuread-dev/sample-v1-code.md).

Per conoscere lo scenario di base di ogni tipo di esempio, vedere [Tipi di app per l'endpoint Microsoft Identity Platform](v2-app-types.md).

È possibile contribuire agli esempi su GitHub. Per sapere come, vedere [Microsoft Azure Active Directory samples and documentation](https://github.com/Azure-Samples?page=3&query=active-directory) (Esempi e documentazione su Microsoft Azure Active Directory).

## <a name="single-page-applications"></a>Applicazioni a pagina singola

Questi esempi illustrano come scrivere un'applicazione a pagina singola protetta tramite Microsoft Identity Platform. In questi esempi viene usato uno dei tipi di MSAL.js.

| Piattaforma | Descrizione | Collegamento |
| -------- | --------------------- | -------- |
| ![Questa immagine mostra il logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | L'applicazione a pagina singola chiama Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Questa immagine mostra il logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | L'applicazione a pagina singola chiama Microsoft Graph usando il flusso del codice di autenticazione con PKCE |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Questa immagine mostra il logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | L'applicazione a pagina singola chiama B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Questa immagine mostra il logo Angular](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| L'applicazione a pagina singola chiama Microsoft Graph  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Questa immagine mostra il logo Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| L'applicazione a pagina singola chiama l'API Web personalizzata | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Questa immagine mostra il logo Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | L'applicazione a pagina singola chiama B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Questa immagine mostra il logo React](media/sample-v2-code/logo_react.png) [React (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| L'applicazione a pagina singola chiama un'API Web personalizzata che a sua volta chiama Microsoft Graph  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Questa immagine mostra il logo Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | L'applicazione a pagina singola chiama l'API Web personalizzata con i ruoli dell'app e i gruppi di sicurezza |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>Applicazioni Web

Gli esempi seguenti illustrano le applicazioni Web che eseguono l'accesso degli utenti. Alcuni esempi illustrano anche l'applicazione che chiama Microsoft Graph o un'API Web con l'identità dell'utente.

| Piattaforma | Esegue solo l'accesso degli utenti | Esegue l'accesso degli utenti e chiama Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Esercitazione sull'accesso degli utenti all'app Web ASP.NET Core](https://aka.ms/aspnetcore-webapp-sign-in) | Stesso esempio della fase [L'app Web ASP.NET Core chiama Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Guida introduttiva di ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png)  | [ms-identity-python-flask-webapp-authentication](https://github.com/Azure-Samples/ms-identity-python-flask-webapp-authentication) | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Questa immagine mostra il logo Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>App client pubbliche per dispositivi mobili e desktop

Gli esempi seguenti mostrano applicazioni client pubbliche, per desktop o dispositivi mobili, che accedono all'API Microsoft Graph o alla propria API Web per conto di un utente. Ad eccezione dell'esempio *Desktop (Console) con Gestione account Web*, tutte queste applicazioni client usano Microsoft Authentication Library (MSAL).

| Applicazione client | Piattaforma | Flusso/concessione | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Desktop (WPF)      | ![Questa immagine mostra il logo .NET/C#](media/sample-v2-code/logo_NET.png) | [Codice di autorizzazione](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Desktop (Console)   | ![Questa immagine mostra il logo .NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | [Autenticazione integrata di Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Desktop (Console)   | ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | [Autenticazione integrata di Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Desktop (Console)   | ![Questa immagine mostra il logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Nome utente/password](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Desktop (Console) con Gestione account Web  | ![Questa immagine mostra il logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Interattivo con [Gestione account Web](/windows/uwp/security/web-account-manager) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Desktop (Console)   | ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | [Nome utente/password](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Desktop (Console)   | ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png) | [Nome utente/password](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Dispositivi mobili (Android, iOS, UWP)   | ![Questa immagine mostra il logo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Codice di autorizzazione](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Dispositivi mobili (iOS)       | ![Questa immagine mostra iOS/Objective-C o Swift](media/sample-v2-code/logo_iOS.png) | [Codice di autorizzazione](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [Codice di autorizzazione](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Dispositivi mobili (Android-Java)   | ![Questa immagine mostra il logo Android](media/sample-v2-code/logo_Android.png) | [Codice di autorizzazione](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Dispositivi mobili (Android-Kotlin)   | ![Questa immagine mostra il logo Android](media/sample-v2-code/logo_Android.png) | [Codice di autorizzazione](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Applicazioni daemon

L'esempio seguente mostra un'applicazione che accede all'API Microsoft Graph con la propria identità (senza utente).

| Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Questa immagine mostra il logo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenziali client](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| app Web | ![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenziali client](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | [Credenziali client](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png) | [Credenziali client](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Applicazioni headless

Gli esempi seguenti mostrano un'applicazione client pubblica eseguite in un dispositivo senza un Web browser. L'app può essere uno strumento da riga di comando, un'app in esecuzione in Linux/Mac oppure un'applicazione IoT. Gli esempi mostrano un'app che accede all'API Microsoft Graph per conto di un utente che esegue l'accesso in modo interattivo in un altro dispositivo, ad esempio un telefono cellulare. Questa applicazione client usa MicroSoft Authentication Library (MSAL).

| Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Desktop (Console)   | ![Questa immagine mostra il logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Flusso del codice del dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Desktop (Console)   | ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | [Flusso del codice del dispositivo](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Desktop (Console)   | ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png) | [Flusso del codice del dispositivo](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Applicazioni SaaS multi-tenant

Gli esempi seguenti illustrano come configurare l'applicazione in modo che accetti gli accessi da qualsiasi tenant di Azure Active Directory (Azure AD). La configurazione dell'applicazione in modo che sia *multi-tenant* significa che è possibile offrire un'applicazione **SaaS (Software as a Service)** a molte organizzazioni, consentendo ai loro utenti di accedervi dopo aver acconsentito a usare il proprio account.

| Piattaforma | Descrizione | Collegamento |
| -------- | --------------------- | -------- |
| ![Questa immagine mostra il logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | L'applicazione a pagina singola multi-tenant chiama l'API Graph |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Questa immagine mostra il logo Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | L'applicazione a pagina singola multi-tenant chiama un'API Web personalizzata multi-tenant |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | L'applicazione Web MVC ASP.NET Core chiama l'API Graph |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | L'applicazione Web MVC ASP.NET Core chiama l'API Web ASP.NET Core |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>API Web

Gli esempi seguenti illustrano come proteggere un'API Web con l'endpoint Microsoft Identity Platform e come chiamare un'API downstream dall'API Web.

| Piattaforma | Esempio |
| -------- | ------------------- |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | API Web (servizio) ASP.NET Core di [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | API Web (servizio) di [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | API Web (servizio) di [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Questa immagine mostra il logo Node.js](media/sample-v2-code/logo_nodejs.png) | API Web (servizio) di [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Questa immagine mostra il logo Node.js](media/sample-v2-code/logo_nodejs.png) | API Web B2C (servizio) di [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Funzioni di Azure come API Web

Gli esempi seguenti illustrano come proteggere una funzione di Azure tramite HttpTrigger ed esporre un'API Web con l'endpoint Microsoft Identity Platform e come chiamare un'API downstream dall'API Web.

| Piattaforma | Esempio |
| -------- | ------------------- |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | Funzione di Azure API Web (servizio) ASP.NET Core di [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Questa immagine mostra il logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | API Web (servizio) di [NodeJS e passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png)</p>Python | API Web (servizio) di [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Questa immagine mostra il logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | API Web (servizio) di [NodeJS e passport-azure-ad con on-behalf-of](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Altri esempi di Microsoft Graph

Per informazioni su [esempi](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) ed esercitazioni che mostrano modelli di utilizzo diversi per l'API Microsoft Graph, tra cui l'autenticazione con Azure AD, vedere [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Esempi ed esercitazioni della community di Microsoft Graph).

## <a name="see-also"></a>Vedere anche

- [Guida per gli sviluppatori di Azure Active Directory (versione 1.0)](../azuread-dev/v1-overview.md)
- [Concetti e riferimenti relativi all'API Microsoft Graph](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta)
