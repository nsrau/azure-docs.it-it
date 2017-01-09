---
title: Librerie di autenticazione di Azure Active Directory 2.0 | Microsoft Docs
description: Librerie client e middleware server compatibili e link a librerie, codice sorgente ed esempi correlati per l&quot;endpoint di Azure Active Directory 2.0.
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2016
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 5ba85485737a6f1ee8908834a74d34863429aeca
ms.openlocfilehash: e68a83936e6ae0b7349444cb24bafd6908166945


---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Librerie di autenticazione di Azure Active Directory 2.0
L'endpoint di Azure Active Directory (Azure AD) 2.0 supporta i protocolli standard del settore OAuth 2.0 e OpenID Connect 1.0. Con l'endpoint 2.0 è possibile usare varie librerie di Microsoft e altre organizzazioni.

Quando si compila un'applicazione che usa l'endpoint 2.0, è consigliabile usare raccolte scritte da esperti del dominio del protocollo che seguono una metodologia Security Development Lifecycle (SDL), come [quella seguita da Microsoft][Microsoft-SDL]. Se si decide di creare manualmente il codice per il supporto dei protocolli, è consigliabile seguire la metodologia SDL e prestare attenzione alle considerazioni sulla sicurezza contenute nelle specifiche degli standard di ogni protocollo.

## <a name="types-of-libraries"></a>Tipi di librerie
Azure AD 2.0 usa due tipi di librerie:

* **Librerie client**. I server e i client nativi usano le librerie client per ottenere i token di accesso per chiamare una risorsa, ad esempio Microsoft Graph.
* **Librerie middleware server**. Le librerie middleware server vengono usate dalle app Web per l'accesso degli utenti e dalle API Web per convalidare i token inviati da client nativi o altri server.

## <a name="library-support"></a>Supporto per le librerie
Dato che è possibile scegliere qualsiasi libreria conforme agli standard quando si usa l'endpoint 2.0, è importante sapere a chi rivolgersi per ottenere supporto. Per richieste relative a problemi e funzionalità nel codice della libreria, contattare il proprietario della libreria. Per richieste relative a problemi e funzionalità nell'implementazione del protocollo sul lato del servizio, contattare Microsoft.

Le librerie dispongono di due categorie di supporto:

* **Librerie supportate da Microsoft**. Microsoft offre aggiornamenti per queste librerie, a cui ha applicato la due diligence SDL.
* **Librerie compatibili**. Microsoft ha testato queste librerie in scenari di base e ne ha confermato il funzionamento con l'endpoint 2.0. Microsoft non fornisce correzioni per queste librerie e non ha eseguito una verifica su di esse. Le richieste relative a problemi e funzionalità devono essere indirizzate al progetto open source della libreria.

Per un elenco delle librerie che funzionano con l'endpoint 2.0, vedere le sezioni successive di questo articolo.

## <a name="microsoft-supported-client-libraries"></a>Librerie client supportate da Microsoft
| Piattaforma | Nome libreria | Scaricare | Codice sorgente | Esempio |
|:---:|:---:|:---:|:---:|:---:|
| .NET, Windows Store, Xamarin |Microsoft Authentication Library (MSAL) per .NET |[Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] |[MSAL per .NET (GitHub)][ClientLib-NET-Repo] |[Esempio di client nativo desktop di Windows][ClientLib-NET-Sample] |
| Node.js |Componente aggiuntivo di Microsoft Azure Active Directory Passport.js |[Passport-Azure-AD (npm)][ClientLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] |Presto disponibile |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Librerie middleware server supportate da Microsoft
| Piattaforma | Nome libreria | Scaricare | Codice sorgente | Esempio |
|:---:|:---:|:---:|:---:|:---:|
| .NET 4.x |OWIN OpenID Connect Middleware per ASP.NET |[Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] |[Katana Project (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] |[Esempio di App Web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x |OWIN OAuth Bearer Middleware per ASP.NET |[Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] |[Katana Project (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] |[Esempio di API Web][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core |OWIN OpenID Connect Middleware per .NET Core |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.NET Security (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Esempio di App Web][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core |OWIN OAuth Bearer Middleware per .NET Core |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.NET Security (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |Presto disponibile |
| Node.js |Plug-in Microsoft Azure Active Directory Passport.js |[Passport-Azure-AD (npm)][ServerLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] |[Esempio di App Web][ServerLib-Node-Sample] |

<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Librerie client compatibili
| Piattaforma | Nome libreria | Versione testata | Codice sorgente | Esempio |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Esempio di app nativa](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Esempio di app nativa](active-directory-v2-devquickstarts-ios.md) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [Versione 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | Presto disponibile |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |Presto disponibile |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Versione 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | Presto disponibile |
| Python-Flask |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |Presto disponibile |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |Presto disponibile |

<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Librerie middleware server compatibili
Presto disponibile

## <a name="related-content"></a>Contenuti correlati
Per altre informazioni sull'endpoint di Azure AD 2.0, vedere la [panoramica del modello app di Azure AD 2.0][AAD-App-Model-V2-Overview].

Per contribuire al miglioramento e all'organizzazione dei contenuti, usare la funzionalità dei commenti Disqus alla fine dell'articolo per fornire commenti e suggerimenti.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
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



<!--HONumber=Nov16_HO5-->


