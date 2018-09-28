---
title: App per le API Web in Azure Active Directory
description: Descrive che cosa sono le applicazioni API Web e presenta le informazioni di base su flusso del protocollo, registrazione e scadenza del token per questo tipo di app.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: e4ed0db3a08937c3c8b51e2c8af5e566b59df4c4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970958"
---
# <a name="web-api"></a>API Web

Le app per le API Web sono applicazioni Web che devono ottenere risorse da un'API Web. In questo scenario esistono due tipi di identità che l'applicazione Web può usare per autenticare e chiamare l'API Web:

- **Identità applicazione**: questo scenario usa la concessione di credenziali client OAuth 2.0 per l'autenticazione come applicazione e l'accesso all'API Web. Quando si usa un'identità applicazione, l'API Web può solo rilevare la chiamata dell'applicazione Web, perché non riceve informazioni relative all'utente. Se l'applicazione riceve informazioni relative all'utente, queste vengono inviate tramite il protocollo applicativo e non sono firmate da Azure AD. L'API Web confida che l'applicazione Web abbia autenticato l'utente. Per questo motivo il modello è definito sottosistema attendibile.
- **Identità utente delegato**: questo scenario può essere gestito in due modi, ovvero con OpenID Connect e con la concessione di codice di autorizzazione OAuth 2.0 con un client riservato. L'applicazione Web ottiene un token di accesso per l'utente, dimostrando all'API Web che l'utente è stato autenticato nell'applicazione Web e che l'applicazione Web è riuscita a ottenere un'identità utente delegato per chiamare l'API Web. Questo token di accesso viene inviato nella richiesta all'API Web, che autorizza l'utente e restituisce la risorsa desiderata.

Nel flusso che segue sono illustrati i tipi di identità applicazione e identità utente delegato. La differenza fondamentale consiste nel fatto che l'identità utente delegato deve acquisire un codice di autorizzazione prima che l'utente possa ottenere l'accesso all'API Web.

## <a name="diagram"></a>Diagramma

![Diagramma Da applicazione Web ad API Web](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Flusso del protocollo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identità applicazione con concessione delle credenziali client OAuth 2.0

1. Un utente esegue l'accesso ad Azure AD nell'applicazione Web (vedere la sezione **App Web** per altre informazioni).
1. L'applicazione Web deve acquisire un token di accesso per l'autenticazione nell'API Web e il recupero della risorsa desiderata. Effettua una richiesta all'endpoint di token di Azure AD, fornendo le credenziali, l'ID applicazione e l'URI ID applicazione dell'API Web.
1. Azure AD autentica l'applicazione e restituisce un token di accesso JWT usato per chiamare l'API Web.
1. Su HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.

### <a name="delegated-user-identity-with-openid-connect"></a>Identità utente delegato con OpenID Connect

1. Un utente esegue l'accesso all'applicazione Web usando Azure AD (vedere la sezione precedente [Da Web browser ad applicazione Web](#web-browser-to-web-application) ). Se l'utente dell'applicazione Web non ha ancora concesso il consenso perché l'applicazione Web chiami l'API Web per suo conto, dovrà acconsentire. L'applicazione visualizzerà le autorizzazioni richieste e, se si tratta di autorizzazione a livello amministratore, un utente normale della directory non potrà concedere il consenso. Questo processo di consenso è valido solo per le applicazioni multi-tenant, non per le applicazioni con un singolo tenant, perché l'applicazione avrà già le autorizzazioni necessarie. Quando l'utente ha eseguito l'accesso, l'applicazione Web ha ricevuto un token ID con le informazioni relative all'utente, nonché un codice di autorizzazione.
1. Con il codice di autorizzazione rilasciato da Azure AD, l'applicazione Web invia una richiesta all'endpoint token di Azure AD che include il codice di autorizzazione, dettagli sull'applicazione client (ID applicazione e URI di reindirizzamento) e la risorsa desiderata (URI ID applicazione per l'API Web).
1. Il codice di autorizzazione e le informazioni sull'applicazione Web e l'API Web vengono convalidati da Azure AD. Se la convalida riesce, Azure AD restituisce tue token: un token di accesso JWT e un token di aggiornamento JWT.
1. Su HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identità utente delegato con concessione del codice di autorizzazione OAuth 2.0

1. Un utente ha già eseguito l'accesso all'applicazione Web, il cui meccanismo di autenticazione è indipendente da Azure AD.
1. L'applicazione Web richiede un codice di autorizzazione per acquisire un token di accesso, quindi invia una richiesta tramite il browser all'endpoint di autorizzazione di Azure AD, fornendo l'ID applicazione e l'URI di reindirizzamento per l'applicazione Web al termine dell'autenticazione. L'utente accede ad Azure AD.
1. Se l'utente dell'applicazione Web non ha ancora concesso il consenso perché l'applicazione Web chiami l'API Web per suo conto, dovrà acconsentire. L'applicazione visualizzerà le autorizzazioni richieste e, se si tratta di autorizzazione a livello amministratore, un utente normale della directory non potrà concedere il consenso. Il consenso si applica sia all'applicazione a tenant singolo che multi-tenant. In un'applicazione a tenant singolo l'amministratore può eseguire il consenso dell'amministratore per acconsentire per conto degli utenti. A questo scopo, è possibile usare il pulsante `Grant Permissions` nel [portale di Azure](https://portal.azure.com). 
1. Quando l'utente concede il consenso, l'applicazione Web riceve il codice di autorizzazione necessario per acquisire un token di accesso.
1. Con il codice di autorizzazione rilasciato da Azure AD, l'applicazione Web invia una richiesta all'endpoint token di Azure AD che include il codice di autorizzazione, dettagli sull'applicazione client (ID applicazione e URI di reindirizzamento) e la risorsa desiderata (URI ID applicazione per l'API Web).
1. Il codice di autorizzazione e le informazioni sull'applicazione Web e l'API Web vengono convalidati da Azure AD. Se la convalida riesce, Azure AD restituisce tue token: un token di accesso JWT e un token di aggiornamento JWT.
1. Su HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.

## <a name="code-samples"></a>Esempi di codice

Vedere gli esempi di codice per gli scenari Da applicazione Web ad API Web. Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi: [Da applicazione Web ad API Web](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Registrazione delle app

Per registrare un'applicazione con l'endpoint Azure AD v1.0, vedere [Register an app with the Azure AD v1.0 endpoint](quickstart-v1-add-azure-ad-app.md) (Registrare un'app con l'endpoint Azure AD v1.0).

* Tenant singolo: in entrambi i casi (identità applicazione e identità utente delegato), l'applicazione Web e l'API Web devono essere registrate nella stessa directory in Azure AD. L'API Web può essere configurata per esporre un set di autorizzazioni, che vengono usate per limitare l'accesso dell'applicazione Web alle relative risorse. Se viene usato il tipo di identità utente delegato, l'applicazione Web deve selezionare le autorizzazioni desiderate dal menu a discesa **Autorizzazioni per altre applicazioni** nel portale di Azure. Questo passaggio non è necessario se viene usato il tipo di identità applicazione.
* Multi-tenant: per prima cosa, l'applicazione Web viene configurata per indicare le autorizzazioni necessarie per il funzionamento. Questo elenco di autorizzazioni richieste viene visualizzato in una finestra di dialogo quando un utente o amministratore nella directory di destinazione concede il consenso all'applicazione, rendendola disponibile per la propria organizzazione. Alcune applicazioni richiedono solo autorizzazioni a livello utente, che possono essere concesse da qualsiasi utente dell'organizzazione. Altre applicazioni richiedono autorizzazioni a livello amministratore, che non possono essere concesse dagli utenti dell'organizzazione. Solo un amministratore di directory può concedere il consenso alle applicazioni che richiedono questo livello di autorizzazione. Quando l'utente o l'amministratore acconsente, l'applicazione Web e l'API Web vengono registrate nella directory.

## <a name="token-expiration"></a>Scadenza del token

Quando l'applicazione Web usa il proprio codice di autorizzazione per ottenere un token di accesso JWT, riceve anche un token di aggiornamento JWT. Quando il token di accesso scade, è possibile usare il token di aggiornamento per autenticare di nuovo l'utente senza richiedere di ripetere l'accesso. Questo token di aggiornamento viene quindi usato per autenticare l'utente, con conseguente emissione di un nuovo token di accesso e un nuovo token di aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su altri [tipi di applicazione e scenari](app-types.md)
- [Nozioni di base sull'autenticazione](authentication-scenarios.md) in Azure AD