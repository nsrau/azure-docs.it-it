---
title: Librerie 2.0 di Azure Active Directory | Microsoft Docs
description: Fornisce un elenco di tutte le librerie client e server middleware compatibili, link a libreire collegate, fonti ed esempi per l'endpoint 2.0 di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: skwan
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2016
ms.author: skwan;bryanla

---
# <a name="azure-active-directory-(ad)-v2.0-and-authentication-libraries"></a>Azure Active Directory (AD) 2.0 e librerie di autenticazione
L'endpoint di Azure AD 2.0 supporta i protocolli standard del settore OAuth 2.0 e OpenID Connect 1.0.  Con l'endpoint 2.0, è possibile usufruire di un'ampia gamma di librerie di Microsoft e di terzi.

Quando si compila un'applicazione che usa l'endpoint 2.0, è consigliabile usare librerie scritte da esperti del dominio del protocollo che seguono una metodologia di Security Development Lifecycle (SDL), [ad esempio quella seguita da Microsoft][Microsoft-SDL].  Se si decide di codificare manualmente il supporto per i protocolli, è consigliabile seguire la metodologia SDL e osservare con attenzione le considerazioni sulla sicurezza reperibili nelle specifiche degli standard di ogni protocollo.

## <a name="types-of-libraries"></a>Tipi di librerie
Esistono due tipi di librerie che funzionano con la versione 2.0: 

* **Librerie client**: le librerie client vengono usate sui client nativi e sui server per ottenere accesso ai token per la chiamata a una risorsa, ad esempio Microsoft Graph.
* **Librerie middleware server**: le librerie middleware del server sono usate dalle applicazioni Web per consentire l'accesso a un utente e dalle API Web per convalidare i token inviati dai client nativi o da altri server.

## <a name="library-support"></a>Supporto per le librerie
Poiché è possibile scegliere qualsiasi libreria conforme agli standard quando si usa l'endpoint 2.0, è importante sapere a chi rivolgersi per ottenere supporto.  I problemi e le richieste sulle funzionalità nel codice della libreria vengono indirizzate al proprietario della libreria. I problemi e le richieste sulle funzionalità nell'implementazione del protocollo sul lato servizio vengono indirizzate a Microsoft.

Le librerie dispongono di due categorie di supporto:

* **Con supporto di Microsoft**: Microsoft fornisce le correzioni per queste librerie.  Microsoft ha implementato su queste librerie la metodologia Security Development Lifecycle. 
* **Compatibili**: Microsoft ha testato un set di librerie in scenari di base e ha dichiarato che funzionano con l'endpoint 2.0.  Microsoft non fornisce correzioni per queste librerie e non ha eseguito una verifica su di esse.  I problemi e le richieste sulle funzionalità devono essere indirizzati al progetto open source della libreria.

Per un elenco di librerie che funzionano con l'endpoint 2.0, vedere le sezioni seguenti. 

## <a name="microsoft-supported-client-libraries"></a>Librerie client con supporto di Microsoft
| Piattaforma | Nome della libreria | Scaricare | Codice sorgente | Esempio |
|:---:|:---:|:---:|:---:|:---:|
| .NET, Windows Store, Xamarin |Microsoft Authentication Library (MSAL) per .NET |[Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] |[MSAL for .NET (GitHub)][ClientLib-NET-Repo] |[Esempio di client nativo desktop di Windows][ClientLib-NET-Sample] |
| Node.js |Componente aggiuntivo di Microsoft Azure Active Directory Passport.js |[Passport-Azure-AD (npm)][ClientLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] |Presto disponibile |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Librerie middleware server con supporto di Microsoft
| Piattaforma | Nome della libreria | Scaricare | Codice sorgente | Esempio |
|:---:|:---:|:---:|:---:|:---:|
| .NET 4.x |OWIN OpenID Connect Middleware per ASP.NET |[Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] |[Katana Project (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] |[Esempio di app Web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x |OWIN OAuth Bearer Middleware per ASP.Net |[Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] |[Katana Project (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] |[Esempio di API Web][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core |OWIN OpenID Connect Middleware per .Net Core |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.Net Security (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Esempio di app Web][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core |OWIN OAuth Bearer Middleware per .Net Core |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.Net Security (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |Presto disponibile |
| Node.js |Componente aggiuntivo di Microsoft Azure Active Directory Passport.js |[Passport-Azure-AD (npm)][ServerLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] |[Esempio di app Web][ServerLib-Node-Sample] |

<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .Net | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .Net (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Librerie client compatibili
| Piattaforma | Nome | Versione verificata | Codice sorgente | Esempio |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Esempio di app nativa](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Esempio di app nativa](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |Presto disponibile |
| Python - Flask |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |Presto disponibile |
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
Per altre informazioni sull'endpoint 2.0 di Azure AD, vedere [Panoramica del Modello app 2.0 di Azure AD][AAD-App-Model-V2-Overview]. 

La sezione dei commenti Disqus di seguito consente di fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:

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




<!--HONumber=Oct16_HO2-->


