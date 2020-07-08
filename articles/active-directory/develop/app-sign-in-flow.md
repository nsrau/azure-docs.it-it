---
title: Flusso di accesso alle app con Microsoft Identity Platform | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sul flusso di accesso alle app Web, desktop e per dispositivi mobili in Microsoft Identity Platform (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: af5b27dc85a276c731a61135ab59ab81f5aaf3c2
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772200"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>Flusso di accesso alle app con Microsoft Identity Platform

Questo argomento illustra il flusso di accesso di base per app Web, desktop e per dispositivi mobili con Microsoft Identity Platform. Per informazioni sugli scenari di accesso supportati da Microsoft Identity Platform, vedere [Flussi di autenticazione e scenari di app](authentication-flows-app-scenarios.md).

## <a name="web-app-sign-in-flow"></a>Flusso di accesso alle app Web

Quando un utente accede dal browser a un'app Web, si verifica quanto segue:

* L'app Web determina se l'utente è autenticato.
* Se l'utente non è autenticato, l'app Web delega ad Azure AD la procedura di accesso dell'utente. Tale accesso sarà conforme ai criteri dell'organizzazione, che possono ad esempio richiedere all'utente di immettere le proprie credenziali, usando l'[autenticazione a più fattori](../authentication/concept-mfa-howitworks.md) (talvolta definita autenticazione a due fattori o 2FA), oppure non richiedere l'immissione di alcuna password, ad esempio usando Windows Hello.
* All'utente viene richiesto di fornire il consenso per l'accesso necessario per l'app client. Questo è il motivo per cui le app client devono essere registrate con Azure AD, affinché Microsoft Identity Platform possa fornire i token che rappresentano l'accesso per cui l'utente ha fornito il consenso.

Dopo l'autenticazione dell'utente:

* Microsoft Identity Platform invia un token all'app Web.
* Viene salvato un cookie, associato al dominio di Azure AD, che contiene l'identità dell'utente nell'archivio cookie del browser. La volta successiva che un'app usa il browser per passare all'endpoint di autorizzazione di Microsoft Identity Platform, il browser presenta il cookie in modo che l'utente non debba eseguire di nuovo l'accesso. Questo è anche il modo in cui funziona l'accesso SSO. Il cookie viene generato da Azure AD e può essere riconosciuto solo da Azure AD.
* L'app Web convalida quindi il token. Se la convalida ha esito positivo, l'app Web visualizza la pagina protetta e salva un cookie di sessione nell'archivio cookie del browser. Quando l'utente passa a un'altra pagina, l'app Web sa che l'utente è autenticato in base al cookie di sessione.

Il diagramma della sequenza seguente riepiloga questa interazione:

![processo di autenticazione dell'app Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>In che modo l'app Web determina se l'utente è autenticato

Gli sviluppatori di app Web possono indicare se tutte o solo determinate pagine richiedono l'autenticazione. Ad esempio, in ASP.NET/ASP.NET Core questa operazione viene eseguita aggiungendo l'attributo `[Authorize]` alle azioni del controller.

Questo attributo fa in modo che ASP.NET verifichi la presenza di un cookie di sessione contenente l'identità dell'utente. Se non è presente un cookie, ASP.NET reindirizza l'autenticazione al provider di identità specificato. Se il provider di identità è Azure AD, l'app Web reindirizza l'autenticazione a `https://login.microsoftonline.com`, che visualizza una finestra di dialogo di accesso.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>In che modo un'app Web delega l'accesso a Microsoft Identity Platform e ottiene un token

L'autenticazione utente avviene tramite il browser. Il protocollo OpenID usa messaggi di protocollo HTTP standard.

* L'app Web invia una richiesta HTTP 302 (reindirizzamento) al browser per usare Microsoft Identity Platform.
* Quando l'utente viene autenticato, Microsoft Identity Platform invia il token all'app Web usando un reindirizzamento tramite il browser.
* Il reindirizzamento viene fornito dall'app Web sotto forma di URI di reindirizzamento. Questo URI di reindirizzamento viene registrato con l'oggetto applicazione Azure AD. Possono essere presenti diversi URI di reindirizzamento perché l'applicazione potrebbe essere stata distribuita in numerosi URL. Di conseguenza, l'app Web dovrà specificare l'URI di reindirizzamento da usare.
* Azure AD verifica che l'URI di reindirizzamento inviato dall'app Web sia uno degli URI di reindirizzamento registrati per l'app.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Flusso di accesso alle app desktop e per dispositivi mobili

Il flusso descritto in precedenza si applica, con lievi differenze, anche alle applicazioni desktop e per dispositivi mobili.

Per l'autenticazione, le applicazioni desktop e per dispositivi mobili possono usare un controllo Web incorporato o un browser di sistema. Il diagramma seguente illustra il modo in cui un'app desktop o per dispositivi mobili usa Microsoft Authentication Library (MSAL) per acquisire i token di accesso e chiamare le API Web.

![Flusso dell'app desktop](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL usa un browser per ottenere i token. Come per le app Web, l'autenticazione viene delegata a Microsoft Identity Platform.

Poiché Azure AD salva lo stesso cookie di identità nel browser come per le app Web, se l'app nativa o per dispositivi mobili usa il browser di sistema, otterrà immediatamente l'accesso SSO all'app Web corrispondente.

Per impostazione predefinita, MSAL usa il browser di sistema. Fanno eccezione le applicazioni destkop .NET Framework in cui viene usato un controllo incorporato per offrire un'esperienza utente più integrata.

## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti relativi alle informazioni di base su autenticazione e autorizzazione:

* Vedere [Confronto tra autenticazione e autorizzazione](authentication-vs-authorization.md) per informazioni sulle nozioni di base di autenticazione e autorizzazione in Microsoft Identity Platform.
* Vedere [Token di sicurezza](security-tokens.md) per informazioni su come vengono usati i token di accesso, i token di aggiornamento e i token ID nell'autenticazione e nell'autorizzazione.
* Vedere [Modello applicativo](application-model.md) per informazioni sul processo di registrazione dell'applicazione in modo che possa integrarsi con Microsoft Identity Platform.

Per altre informazioni sul flusso di accesso alle app:

* Per altre informazioni su altri scenari di autenticazione degli utenti supportati da Microsoft Identity Platform, vedere [Flussi di autenticazione e scenari di app](authentication-flows-app-scenarios.md).
* Per informazioni sulle librerie Microsoft che consentono di sviluppare applicazioni che funzionano con account Microsoft, account Azure AD e utenti di Azure AD B2C in un unico modello di programmazione semplificato, vedere [librerie MSAL](msal-overview.md).
