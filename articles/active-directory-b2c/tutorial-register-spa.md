---
title: "Esercitazione: Registrare un'applicazione a pagina singola"
titleSuffix: Azure AD B2C
description: Seguire questa esercitazione per apprendere come registrare un'applicazione a pagina singola in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fc01fb4296226126b996840109d3bb305b042364
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275807"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>Esercitazione: Registrare un'applicazione a pagina singola in Azure Active Directory B2C

Prima che le [applicazioni](application-types.md) possano interagire con Azure Active Directory B2C (Azure AD B2C), devono essere registrate in un tenant gestito. Questa esercitazione illustra come registrare un'applicazione a pagina singola usando il portale di Azure.

## <a name="overview-of-authentication-options"></a>Panoramica delle opzioni di autenticazione

Molte applicazioni Web moderne vengono compilate come applicazioni a pagina singola sul lato client. Gli sviluppatori le scrivono usando JavaScript o un framework di applicazioni a pagina singola come Angular, Vue e React. Queste applicazioni vengono eseguite in un Web browser e hanno caratteristiche di autenticazione diverse rispetto alle applicazioni Web lato server tradizionali.

Azure AD B2C offre **due** opzioni che consentono alle applicazioni a pagina singola di concedere l'accesso agli utenti e ottenere i token per l'accesso a servizi back-end o API Web:

### <a name="authorization-code-flow-with-pkce"></a>Flusso di codice di autorizzazione (con PKCE)
- [Flusso di codice di autorizzazione OAuth 2.0 (con PKCE)](./authorization-code-flow.md). Il flusso di codice di autorizzazione consente all'applicazione di scambiare un codice di autorizzazione con i token **ID** per rappresentare l'utente autenticato e i token di **accesso** necessari per chiamare le API protette. Restituisce inoltre **token di aggiornamento** che consentono l'accesso a lungo termine alle risorse per conto degli utenti senza richiedere l'interazione con tali utenti. 

Questo è l'approccio **consigliato** . La presenza di token di aggiornamento a durata limitata consente all'applicazione di adattarsi alle [limitazioni della privacy dei cookie dei browser moderni](../active-directory/develop/reference-third-party-cookies-spas.md), ad esempio Safari ITP.

Per sfruttare i vantaggi di questo flusso, l'applicazione può usare una libreria di autenticazione che lo supporta, come ad esempio [MSAL.js 2.x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa). 

![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>Flusso di concessione implicita
- [Flusso implicito OAuth 2.0](implicit-flow-single-page-application.md). Alcuni framework, come [MSAL.js 1.x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp), supportano solo il flusso di concessione implicita. Questo tipo di flusso consente all'applicazione di ottenere i token **ID** e **di accesso** . A differenza del flusso di codice di autorizzazione, il flusso di concessione implicita non restituisce un **token di aggiornamento** . 

![Single-page applications-implicit](./media/tutorial-single-page-app/spa-app.svg)

Questo flusso di autenticazione non include scenari di applicazioni che usano framework JavaScript multipiattaforma, come Electron e React-Native. Questi scenari richiedono ulteriori funzionalità per l'interazione con le piattaforme native.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se non è ancora stato creato un [tenant Azure AD B2C](tutorial-create-tenant.md), crearlo ora. Usare un tenant Azure AD B2C esistente.

## <a name="register-the-spa-application"></a>Registrare l'applicazione a pagina singola

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C** .
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione** .
1. Immettere un **nome** per l'applicazione. Ad esempio, *spaapp1* .
1. In **Tipi di account supportati** selezionare **Account in qualsiasi provider di identità o directory dell'organizzazione (per l'autenticazione di utenti con flussi utente)** .
1. In **URI di reindirizzamento** selezionare **Applicazione a pagina singola** , quindi immettere `https://jwt.ms` nella casella di testo URL.

    L'URI di reindirizzamento è l'endpoint a cui l'utente viene inviato dal server di autorizzazione (in questo caso Azure AD B2C) dopo aver completato l'interazione con l'utente e a cui viene inviato un token di accesso o un codice di autorizzazione dopo l'autorizzazione. In un'applicazione di produzione, si tratta in genere di un endpoint accessibile pubblicamente in cui l'app è in esecuzione, ad esempio `https://contoso.com/auth-response`. A scopo di test come questa esercitazione, è possibile impostarlo su `https://jwt.ms`, un'applicazione Web di proprietà di Microsoft che visualizza il contenuto decodificato di un token (il contenuto del token non lascia mai il browser). Durante lo sviluppo di app, è possibile aggiungere l'endpoint in cui l'applicazione è in ascolto localmente, ad esempio `http://localhost:5000`. È possibile aggiungere e modificare gli URI di reindirizzamento nelle applicazioni registrate in qualsiasi momento.

    Agli URL di reindirizzamento si applicano le restrizioni seguenti:

    * L'URL di risposta deve iniziare con lo schema `https`, a meno che non si usi `localhost`.
    * L'URL di risposta rileva la distinzione tra maiuscole e minuscole. Le maiuscole e le minuscole devono corrispondere a quelle nel percorso URL dell'applicazione in esecuzione. Se, ad esempio, l'applicazione include come parte del percorso `.../abc/response-oidc`, non specificare `.../ABC/response-oidc` nell'URL di risposta. Poiché il Web browser rileva la distinzione tra maiuscole e minuscole nei percorsi, è possibile che i cookie associati a `.../abc/response-oidc` vengano esclusi se reindirizzati all'URL `.../ABC/response-oidc` senza la corrispondenza tra maiuscole e minuscole.

1. In **Autorizzazioni** , selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access* .
1. Selezionare **Registra** .


## <a name="enable-the-implicit-flow"></a>Abilitare il flusso implicito
Se si usa il flusso implicito, occorre abilitare il flusso di concessione implicita nella registrazione dell'app.

1. Nel menu a sinistra, in **Gestisci** , selezionare **Autenticazione** .
1. In **Concessione implicita** selezionare entrambe le caselle di controllo **Token di accesso** e **ID token** .
1. Selezionare **Salva** .

## <a name="migrate-from-the-implicit-flow"></a>Eseguire la migrazione dal flusso implicito

Se si ha un'applicazione che usa il flusso implicito, è consigliabile eseguirne la migrazione all'uso del flusso di codice di autorizzazione usando un framework che lo supporta, ad esempio [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

Quando tutte le applicazioni a pagina singola di produzione rappresentate da una registrazione dell'app usano il flusso di codice di autorizzazione, disabilitare le impostazioni del flusso di concessione implicita. 

1. Nel menu a sinistra, in **Gestisci** , selezionare **Autenticazione** .
1. In **Concessione implicita** deselezionare entrambe le caselle di controllo **Token di accesso** e **ID token** .
1. Selezionare **Salva** .

Le applicazioni che usano il flusso implicito possono continuare a funzionare se si lascia il flusso implicito abilitato (selezionato).

* * *

## <a name="next-steps"></a>Passaggi successivi

Successivamente, si apprenderà come creare flussi utente per consentire agli utenti di iscriversi, accedere e gestire i propri profili.

> [!div class="nextstepaction"]
> [Creare flussi utente in Azure Active Directory B2C >](tutorial-create-user-flows.md)
