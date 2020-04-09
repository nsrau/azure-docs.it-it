---
title: Esempi di codice per la piattaforma di identità MicrosoftCode samples for Microsoft identity platform
description: Fornisce un indice degli esempi di codice disponibili sulla piattaforma di identità Microsoft (endpoint 2.0), organizzata in base allo scenario.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5596f5f40e293a7bd11ac6a5f900e05c0b8d3b0e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883135"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Esempi di codice della piattaforma di identità Microsoft (endpoint v2.0)Microsoft identity platform code samples (v2.0 endpoint)

È possibile usare Microsoft Identity Platform per:

- Aggiungere l'autenticazione e l'autorizzazione alle applicazioni Web e alle API Web.
- Richiedere un token di accesso per accedere a un'API Web protetta.

Questo articolo descrive brevemente e fornisce collegamenti a esempi per l'endpoint della piattaforma di identità Microsoft.This article briefly describes and provides you with links to samples for the Microsoft identity platform endpoint. Questi esempi illustrano come viene eseguita e forniscono anche frammenti di codice che è possibile usare nelle applicazioni. Nella pagina dell'esempio di codice sono disponibili argomenti relativi al file Leggimi dettagliati che consentono di soddisfare i requisiti, l'installazione e l'installazione. I commenti all'interno del codice consentono di comprendere le sezioni critiche.

> [!NOTE]
> Se si è interessati agli esempi v1.0, vedere Esempi di codice di [Azure AD (endpoint v1.0).](../azuread-dev/sample-v1-code.md)

Per comprendere lo scenario di base per ogni tipo di esempio, vedere [Tipi di app per l'endpoint della piattaforma di identità Microsoft.](v2-app-types.md)

È possibile contribuire agli esempi su GitHub. Per sapere come, vedere [Microsoft Azure Active Directory samples and documentation](https://github.com/Azure-Samples?page=3&query=active-directory) (Esempi e documentazione su Microsoft Azure Active Directory).

## <a name="single-page-applications"></a>Applicazioni a pagina singola

Questi esempi mostrano come scrivere un'applicazione a pagina singola protetta con la piattaforma di identità Microsoft.These samples show how to write a single-page application secured with Microsoft identity platform. Questi esempi usano uno dei sapori di MSAL.js.

| Piattaforma | Descrizione | Collegamento |
| -------- | --------------------- | -------- |
| ![Questa immagine mostra il](media/sample-v2-code/logo_js.png) logo JavaScript [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chiama Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Questa immagine mostra il](media/sample-v2-code/logo_js.png) logo JavaScript [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chiamate B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Questa immagine mostra il](media/sample-v2-code/logo_js.png) logo JavaScript [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chiama la propria API Web |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Questa immagine mostra il](media/sample-v2-code/logo_angular.png) logo angolare [JavaScript (MSAL angolare)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Chiama Microsoft Graph  | [active-directory-javascript-singlepageapp-angolare](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Questa immagine mostra il](media/sample-v2-code/logo_angular.png) logo angolare [JavaScript (MSAL angolare)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Chiamate B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Applicazioni Web

Gli esempi seguenti illustrano le applicazioni Web che eseguono l'accesso degli utenti. Alcuni esempi illustrano anche l'applicazione che chiama Microsoft Graph o un'API Web con l'identità dell'utente.

| Piattaforma | Esegue solo l'accesso degli utenti | Esegue l'accesso degli utenti e chiama Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [ASP.NET esercitazione sull'accesso di WebApp di base](https://aka.ms/aspnetcore-webapp-sign-in) | Stesso esempio nella fase [di ASP.NET Core Web App chiama Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Guida introduttiva di ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Questa immagine mostra il logo Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>App client pubbliche per dispositivi mobili e desktop

Gli esempi seguenti mostrano le applicazioni client pubbliche (applicazioni desktop o mobili) che accedono all'API Microsoft Graph o la propria API Web a nome di un utente. Tutte queste applicazioni client utilizzano Microsoft Authentication Library (MSAL).

| Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph | Chiama un'API Web ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Desktop (WPF)      | ![In questa immagine viene illustrato il logo .NET/C](media/sample-v2-code/logo_NET.png) | [Interattiva](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Desktop (Console)   | ![In questa immagine viene illustrato il logo di .NET/C](media/sample-v2-code/logo_NET.png) | [Autenticazione integrata di Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Desktop (Console)   | ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | [Autenticazione integrata di Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Desktop (Console)   | ![In questa immagine viene illustrato il logo di .NET/C](media/sample-v2-code/logo_NETcore.png) | [Nome utente/password](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Desktop (Console) con WAM  | ![In questa immagine viene illustrato il logo di .NET/C](media/sample-v2-code/logo_NETcore.png) | [interattivo con WAM](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Desktop (Console)   | ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | [Nome utente/password](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Desktop (Console)   | ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png) | [Nome utente/password](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Dispositivi mobili (Android, iOS, UWP)   | ![In questa immagine viene illustrato il logo di .NET/C](media/sample-v2-code/logo_xamarin.png) | [Interattiva](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Dispositivi mobili (iOS)       | ![Questa immagine mostra iOS/Objective-C o Swift](media/sample-v2-code/logo_iOS.png) | [Interattiva](msal-authentication-flows.md#interactive) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [Interattiva](msal-authentication-flows.md#interactive) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Cellulare (Android-Java)   | ![Questa immagine mostra il logo Android](media/sample-v2-code/logo_Android.png) | [Interattiva](msal-authentication-flows.md#interactive) |  [androide-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Cellulare (Android-Kotlin)   | ![Questa immagine mostra il logo Android](media/sample-v2-code/logo_Android.png) | [Interattiva](msal-authentication-flows.md#interactive) |  [androide Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Applicazioni daemon

L'esempio seguente mostra un'applicazione che accede all'API Microsoft Graph con la propria identità (senza utente).

| Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Questa immagine mostra il logo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenziali client](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| app Web | ![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenziali client](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | [Credenziali client](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png) | [Credenziali client](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Applicazioni headless

Gli esempi seguenti mostrano un'applicazione client pubblica eseguite in un dispositivo senza un Web browser. L'app può essere uno strumento da riga di comando, un'app in esecuzione su Linux o Mac o un'applicazione IoT.The app can be a command-line tool, an app running on Linux or Mac, or an IoT application. L'esempio include un'app che accede all'API Microsoft Graph, nel nome di un utente che accede in modo interattivo su un altro dispositivo (ad esempio un telefono cellulare). Questa applicazione client utilizza Microsoft Authentication Library (MSAL).

| Applicazione client | Piattaforma | Flusso/Concessione | Chiama Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Desktop (Console)   | ![In questa immagine viene illustrato il logo di .NET/C](media/sample-v2-code/logo_NETcore.png) | [Flusso del codice del dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Desktop (Console)   | ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | [Flusso del codice del dispositivo](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Desktop (Console)   | ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png) | [Flusso del codice del dispositivo](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>API Web

The following samples show how to protect a web API with the Microsoft identity platform endpoint, and how to call a downstream API from the web API.

| Piattaforma | Esempio |
| -------- | ------------------- |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET API Web core (servizio) di [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Questa immagine mostra il logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | API Web (servizio) di [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Questa immagine mostra il logo Java](media/sample-v2-code/logo_java.png) | API Web (servizio) di [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Questa immagine mostra il logo Node.js](media/sample-v2-code/logo_nodejs.png) | API Web (servizio) di [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Questa immagine mostra il logo Node.js](media/sample-v2-code/logo_nodejs.png) | API Web B2C (servizio) di [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions as web APIs

The following samples show how to protect an Azure Function using HttpTrigger and exposing a web API with the Microsoft identity platform endpoint, and how to call a downstream API from the web API.

| Piattaforma | Esempio |
| -------- | ------------------- |
| ![Questa immagine mostra il logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | Funzione di Azure dell'API Web di base di ASP.NET di [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Questa immagine mostra il logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | API Web (servizio) di [NodeJS e passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Questa immagine mostra il logo Python](media/sample-v2-code/logo_python.png)</p>Python | API Web (servizio) di [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Questa immagine mostra il logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | API Web (servizio) di [NodeJS e passport-azure-ad utilizzando per conto di](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Altri esempi di Microsoft Graph

Per informazioni su [esempi](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) ed esercitazioni che mostrano modelli di utilizzo diversi per l'API Microsoft Graph, tra cui l'autenticazione con Azure AD, vedere [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Esempi ed esercitazioni della community di Microsoft Graph).

## <a name="see-also"></a>Vedere anche

- [Guida per gli sviluppatori di Azure Active Directory (v1.0)](../azuread-dev/v1-overview.md)
- [Concettuale e di riferimento dell'API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
