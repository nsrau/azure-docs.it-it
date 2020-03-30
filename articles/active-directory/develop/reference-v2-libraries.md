---
title: Librerie di autenticazione della piattaforma di identità Microsoft Documenti Microsoft
description: Librerie client compatibili e librerie middleware server, insieme a collegamenti di libreria, origine ed esempio correlati per l'endpoint della piattaforma di identità Microsoft.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9d2df175fa9d1ed33eb17ae85e01a4fd7a24e728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611946"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Librerie di autenticazione della piattaforma di identità Microsoft

L'endpoint della [piattaforma di identità Microsoft](active-directory-v2-compare.md) supporta i protocolli OAuth 2.0 e OpenID Connect 1.0 standard del settore. Microsoft Authentication Library (MSAL) è progettato per funzionare con l'endpoint della piattaforma di identità Microsoft.The Microsoft Authentication Library (MSAL) is designed to work with the Microsoft identity platform endpoint. È inoltre possibile utilizzare librerie open source che supportano OAuth 2.0 e OpenID Connect 1.0.You can also use open-source libraries that support OAuth 2.0 and OpenID Connect 1.0.

Si consiglia di utilizzare librerie scritte da esperti di domini di protocollo che seguono una metodologia Security Development Lifecycle (SDL). Tali metodologie includono [quella che Microsoft segue][Microsoft-SDL]. Se si esegue il codice per i protocolli, è necessario seguire una metodologia come Microsoft SDL. Prestare particolare attenzione alle considerazioni sulla sicurezza nelle specifiche degli standard per ogni protocollo.

> [!NOTE]
> Si sta cercando Azure Active Directory Authentication Library (ADAL)? Consultare la guida alla [libreria ADAL](../azuread-dev/active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Tipi di librerie

L'endpoint della piattaforma di identità Microsoft funziona con due tipi di librerie:The Microsoft identity platform endpoint works with two types of libraries:

* **Librerie client:** client e server nativi utilizzano librerie client per acquisire token di accesso per chiamare una risorsa come Microsoft Graph.Client libraries : Native clients and servers use client libraries to acquire access tokens for calling a resource such as Microsoft Graph.
* **Librerie middleware server**: le app Web usano librerie middleware server per l'accesso degli utenti. e dalle API Web per convalidare i token inviati da client nativi o altri server.

## <a name="library-support"></a>Supporto per le librerie

Le librerie dispongono di due categorie di supporto:

* **Librerie supportate da Microsoft**: Microsoft offre correzioni per queste librerie e ha applicato la due diligence SDL a queste librerie.
* **Compatibile:** Microsoft ha testato queste librerie in scenari di base e ha confermato che funzionano con l'endpoint della piattaforma di identità Microsoft.Compatible : Microsoft has tested these libraries in basic scenarios and has confirmed that they work with the Microsoft identity platform endpoint. Microsoft non fornisce correzioni per queste librerie e non ha fatto una revisione di queste librerie. Le richieste relative a problemi e funzionalità devono essere indirizzate al progetto open source della libreria.

Per un elenco delle librerie che funzionano con l'endpoint della piattaforma di identità Microsoft, vedere le sezioni seguenti.

## <a name="microsoft-supported-client-libraries"></a>Librerie client supportate da Microsoft

Utilizzare le librerie di autenticazione client per acquisire un token per chiamare un'API Web protetta.

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Riferimento | Documenti di carattere concettuale | Roadmap |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [App a singola pagina](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Riferimento](https://azuread.github.io/microsoft-authentication-library-for-js/docs/msal/) | [Documenti concettuali](msal-overview.md)| [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angolare (anteprima) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[Nuget](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [App desktop](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Documenti concettuali](msal-overview.md) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Esempi](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [LetturaTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Esempi](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Riferimento](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS & macOS | MSAL iOS e macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [App iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [app macOS](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Riferimento](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Documenti concettuali](msal-overview.md) | |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Archivio centrale](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [App Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Documenti concettuali](msal-overview.md) |[Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Librerie middleware server supportate da Microsoft

Utilizzare librerie middleware per proteggere le applicazioni Web e le API Web. Le app Web o le API Web scritte con ASP.NET o ASP.NET Core usano le librerie middleware.

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Riferimento
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET Security |[Nuget](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[App MVC](quickstart-v2-aspnet-webapp.md) |[Informazioni di riferimento sulle API REST](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Estensioni IdentityModel per .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [App MVC](quickstart-v2-aspnet-webapp.md) |[Riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Passport Azure AD |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [App Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Librerie supportate da Microsoft per sistema operativo / linguaggio

In termini di sistemi operativi supportati rispetto ai linguaggi, il mapping è il seguente:

|             | WINDOWS    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (MacOS)       | MSAL.Net (Xamarin.iOS) | MSAL.Net (Xamarin.Android)|
| Swift <br> Objective-C |            |            | [MSAL per iOS e MacOS](msal-overview.md) | [MSAL per iOS e MacOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passaporto.node | Passaporto.node | Passaporto.node |

Vedi anche [Scenari per piattaforme e lingue supportate](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Librerie client compatibili

| Piattaforma | Nome libreria | Versione testata | Codice sorgente | Esempio |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Versione 1.13.5Version 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[Spa](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Versione 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Libreria Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Versione 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Libreria Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Richieste-OAuthlib](https://github.com/requests/requests-oauthlib) | [Versione 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Richieste-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [Versione 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Versione 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reagisci autenticazione nativa dell'appReact Native App Auth](https://github.com/FormidableLabs/react-native-app-auth) | [Versione 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reagisci autenticazione nativa dell'appReact Native App Auth](https://github.com/FormidableLabs/react-native-app-auth) | |

Per qualsiasi libreria conforme agli standard, è possibile usare l'endpoint della piattaforma di identità Microsoft.For any standards-compliant library, you can use the Microsoft identity platform endpoint. È importante sapere dove andare per il supporto:

* Per problemi e richieste di nuove funzionalità nel codice della libreria, contattare il proprietario della libreria.
* Per problemi e richieste di nuove funzionalità nell'implementazione del protocollo sul lato servizio, contattare Microsoft.
* [Presentare una richiesta](https://feedback.azure.com/forums/169401-azure-active-directory) di funzionalità per funzionalità aggiuntive che si desidera visualizzare nel protocollo.
* [Creare una richiesta](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) di supporto se si rileva un problema in cui l'endpoint della piattaforma di identità Microsoft non è conforme a OAuth 2.0 o OpenID Connect 1.0.Create a support request if you find an issue where the Microsoft identity platform endpoint isn't compliant with OAuth 2.0 or OpenID Connect 1.0.

## <a name="related-content"></a>Contenuti correlati

Per ulteriori informazioni sull'endpoint della piattaforma di identità Microsoft, vedere Panoramica della [piattaforma di identità Microsoft][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
