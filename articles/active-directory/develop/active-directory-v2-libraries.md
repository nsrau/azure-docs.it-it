---
title: Librerie di autenticazione di Azure Active Directory 2.0 | Microsoft Docs
description: Librerie client e middleware server compatibili e link a librerie, codice sorgente ed esempi correlati per l'endpoint di Azure Active Directory 2.0.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 60abada0a6683ee5cc8118c37336fe67cd51b2f0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Librerie di autenticazione di Azure Active Directory 2.0
L'[endpoint di Azure Active Directory (Azure AD) 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) supporta i protocolli standard del settore OAuth 2.0 e OpenID Connect 1.0. Con l'endpoint 2.0 è possibile usare varie librerie di Microsoft e altre organizzazioni.

Quando si compila un'applicazione che usa l'endpoint 2.0, è consigliabile usare raccolte scritte da esperti del dominio del protocollo che seguono una metodologia Security Development Lifecycle (SDL), come [quella seguita da Microsoft][Microsoft-SDL]. Se si decide di creare manualmente il codice per il supporto dei protocolli, è consigliabile seguire la metodologia SDL e prestare attenzione alle considerazioni sulla sicurezza contenute nelle specifiche degli standard di ogni protocollo.

> [!NOTE]
> Si cercano le librerie v1.0 di Azure AD (ASAL)? Controllare la [Guida alle librerie ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Tipi di librerie
L'endpoint di Azure AD v2.0 usa due tipi di librerie:

* **Librerie client**. I server e i client nativi usano le librerie client per ottenere i token di accesso per chiamare una risorsa, ad esempio Microsoft Graph.
* **Librerie middleware server**. Le librerie middleware server vengono usate dalle app Web per l'accesso degli utenti e dalle API Web per convalidare i token inviati da client nativi o altri server.

## <a name="library-support"></a>Supporto per le librerie
Dato che è possibile scegliere qualsiasi libreria conforme agli standard quando si usa l'endpoint 2.0, è importante sapere a chi rivolgersi per ottenere supporto. Per richieste relative a problemi e funzionalità nel codice della libreria, contattare il proprietario della libreria. Per richieste relative a problemi e funzionalità nell'implementazione del protocollo sul lato del servizio, contattare Microsoft.

Le librerie dispongono di due categorie di supporto:

* **Librerie supportate da Microsoft**. Microsoft offre aggiornamenti per queste librerie, a cui ha applicato la due diligence SDL.
* **Librerie compatibili**. Microsoft ha testato queste librerie in scenari di base e ne ha confermato il funzionamento con l'endpoint 2.0. Microsoft non fornisce correzioni per queste librerie e non ha eseguito una verifica su di esse. Le richieste relative a problemi e funzionalità devono essere indirizzate al progetto open source della libreria.

Per un elenco delle librerie che funzionano con l'endpoint 2.0, vedere le sezioni successive di questo articolo.


## <a name="microsoft-supported-client-libraries"></a>Librerie client supportate da Microsoft

> [!IMPORTANT]
> Le librerie di anteprima MSAL sono adatte per essere usate in ambienti di produzione. Per queste librerie viene fornito lo stesso supporto di livello produzione offerto per le librerie di produzione correnti (ADAL). In fase di anteprima è possibile apportare modifiche all'API MSAL, al formato della cache interna e ad altri meccanismi delle librerie senza preavviso, ma queste modifiche dovranno essere replicate durante le correzioni di bug o i miglioramenti delle funzionalità. Possono infatti incidere sull'applicazione. Una modifica al formato della cache, ad esempio, può incidere sugli utenti chiedendo loro di accedere di nuovo. Per aggiornare il codice è possibile che sia necessario modificare l'API. Quando si fornisce la release di disponibilità generale, agli utenti viene chiesto di eseguire l'aggiornamento alla versione di disponibilità generale entro sei mesi, poiché le applicazioni scritte usando una versione di anteprima possono non funzionare più.

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | riferimento
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS e Android | MSAL .NET (anteprima) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [App desktop](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (anteprima) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [App a pagina singola](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (anteprima) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [App iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (anteprima) | [Repository centrale](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [App Android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Librerie middleware server supportate da Microsoft

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | riferimento
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | Middleware OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Applicazione MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Middleware OWIN OAuth Bearer per Azure AD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |  | |
| .NET 4.x | Gestore token JWT per .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Middleware ASP.NET OpenID Connect |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.NET Security (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[App MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Middleware ASP.NET OAuth Bearer |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.NET Security (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Gestore token JWT per .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Passport Azure AD |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [App Web](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>Librerie client compatibili
| Piattaforma | Nome libreria | Versione testata | Codice sorgente | Esempio |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Esempio di app nativa](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Esempio di app nativa](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [Versione 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Versione 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Contenuti correlati
Per altre informazioni sull'endpoint di Azure AD 2.0, vedere la [panoramica del modello app di Azure AD 2.0][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
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

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
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
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
