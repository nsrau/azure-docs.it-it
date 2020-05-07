---
title: Flusso di accesso all'app con la piattaforma di identità Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sul flusso di accesso di app Web, desktop e per dispositivi mobili in Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 7b326e17611b5f4b9520d8218a28a67afe9a851a
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584350"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>Flusso di accesso all'app con la piattaforma di identità Microsoft

Questo argomento illustra il flusso di accesso di base per app Web, desktop e per dispositivi mobili con la piattaforma di identità Microsoft. Vedere [flussi di autenticazione e scenari di app](authentication-flows-app-scenarios.md) per informazioni sugli scenari di accesso supportati dalla piattaforma di identità Microsoft.

## <a name="web-app-sign-in-flow"></a>Flusso di accesso all'app Web

Quando un utente accede al browser a un'app Web, si verifica quanto segue:

* L'app Web determina se l'utente è autenticato.
* Se l'utente non è autenticato, l'app Web delega a Azure AD per l'accesso dell'utente. Tale accesso sarà conforme ai criteri dell'organizzazione, che possono significare richiedere all'utente di immettere le proprie credenziali, usando l'autenticazione a più fattori o non usando una password, ad esempio usando Windows Hello.
* All'utente viene richiesto di fornire il consenso per l'accesso necessario all'app client. Questo è il motivo per cui le app client devono essere registrate con Azure AD, in modo che la piattaforma di identità Microsoft possa fornire token che rappresentano l'accesso a cui l'utente ha acconsentito.

Quando l'utente è stato autenticato correttamente:

* Microsoft Identity Platform Invia un token all'app Web.
* Un cookie viene salvato, associato al dominio Azure AD, che contiene l'identità dell'utente nel file jar del browser. La volta successiva che un'app usa il browser per passare all'endpoint di autorizzazione di Microsoft Identity Platform, il browser presenta il cookie in modo che l'utente non debba eseguire di nuovo l'accesso. Questo è anche il modo in cui si raggiunge SSO. Il cookie viene generato da Azure AD e può essere compreso solo da Azure AD.
* Il token viene quindi convalidato dall'app Web. Se la convalida ha esito positivo, l'app Web Visualizza la pagina protetta e salva un cookie di sessione nel file jar dei cookie del browser. Quando l'utente passa a un'altra pagina, l'app Web sa che l'utente è autenticato in base al cookie di sessione.

Il diagramma di sequenza seguente riepiloga questa interazione:

![processo di autenticazione dell'app Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Modo in cui un'app Web determina se l'utente è autenticato

Gli sviluppatori di app Web possono indicare se tutte o solo determinate pagine richiedono l'autenticazione. Ad esempio, in ASP.NET/ASP.NET Core questa operazione viene eseguita aggiungendo l' `[Authorize]` attributo alle azioni del controller.

Questo attributo fa in modo che ASP.NET verifichi la presenza di un cookie di sessione contenente l'identità dell'utente. Se non è presente un cookie, ASP.NET reindirizza l'autenticazione al provider di identità specificato. Se il provider di identità è Azure AD, l'app Web reindirizza l'autenticazione a `https://login.microsoftonline.com`, che visualizza una finestra di dialogo di accesso.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Delega dell'accesso a Microsoft Identity Platform da un'app Web e ottenimento di un token

L'autenticazione utente avviene tramite il browser. Il protocollo OpenID utilizza messaggi di protocollo HTTP standard.

* L'app Web invia un HTTP 302 (Reindirizzamento) al browser per usare la piattaforma di identità Microsoft.
* Quando l'utente viene autenticato, la piattaforma Microsoft Identity invia il token all'app Web usando un reindirizzamento tramite il browser.
* Il reindirizzamento viene fornito dall'app Web sotto forma di URI di reindirizzamento. Questo URI di reindirizzamento viene registrato con l'oggetto applicazione Azure AD. Possono essere presenti diversi URI di reindirizzamento perché l'applicazione può essere distribuita in diversi URL. Quindi, anche l'app Web dovrà specificare l'URI di Reindirizzamento da usare.
* Azure AD verifica che l'URI di reindirizzamento inviato dall'app Web sia uno degli URI di reindirizzamento registrati per l'app.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Flusso di accesso all'app desktop e per dispositivi mobili

Il flusso descritto in precedenza si applica, con lievi differenze, alle applicazioni desktop e per dispositivi mobili.

Le applicazioni desktop e per dispositivi mobili possono utilizzare un controllo Web incorporato o un browser di sistema per l'autenticazione. Il diagramma seguente illustra il modo in cui un'app desktop o per dispositivi mobili USA Microsoft Authentication Library (MSAL) per acquisire i token di accesso e chiamare le API Web.

![App desktop come sembra](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL usa un browser per ottenere i token. Come per le app Web, l'autenticazione viene delegata alla piattaforma di identità Microsoft.

Poiché Azure AD Salva lo stesso cookie di identità nel browser come per le app Web, se l'app nativa o per dispositivi mobili usa il browser di sistema, riceverà immediatamente SSO con l'app Web corrispondente.

Per impostazione predefinita, MSAL usa il browser del sistema. L'eccezione è .NET Framework applicazioni desktop in cui viene usato un controllo incorporato per offrire un'esperienza utente più integrata.

## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti relativi alle nozioni di base sull'autenticazione e sull'autorizzazione:

* Vedere [autenticazione e autorizzazione](authentication-vs-authorization.md) per informazioni sui concetti di base di autenticazione e autorizzazione nella piattaforma di identità Microsoft.
* Vedere [token di sicurezza](security-tokens.md) per informazioni sull'uso di token di accesso, token di aggiornamento e token ID nell'autenticazione e nell'autorizzazione.
* Vedere [modello di applicazione](application-model.md) per informazioni sul processo di registrazione dell'applicazione in modo che possa integrarsi con la piattaforma di identità Microsoft.

Per altre informazioni sul flusso di accesso all'app:

* Vedere [flussi di autenticazione e scenari di app](authentication-flows-app-scenarios.md) per altre informazioni su altri scenari per l'autenticazione degli utenti supportati dalla piattaforma di identità Microsoft.
* Per informazioni sulle librerie Microsoft che consentono di sviluppare applicazioni che interagiscono con account Microsoft, Azure AD account e Azure AD B2C utenti in un unico modello di programmazione semplificato, vedere [librerie MSAL](msal-overview.md) .
