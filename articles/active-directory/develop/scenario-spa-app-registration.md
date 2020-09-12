---
title: Registrare applicazioni a pagina singola (SPA) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'applicazione a pagina singola (registrazione dell'app)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 2d596b6d0e291d197146bf5e00827d82b74c125c
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434538"
---
# <a name="single-page-application-app-registration"></a>Applicazione a pagina singola: Registrazione delle app

Per registrare un'applicazione a pagina singola (SPA) in Microsoft Identity Platform, seguire questa procedura. La procedura di registrazione è diversa tra MSAL.js 1.0, che supporta il flusso di concessione implicita, e MSAL.js 2.0, che supporta il flusso del codice di autorizzazione con PKCE.

[!INCLUDE [MSAL.js 2.0 and Azure AD B2C temporary incompatibility notice](../../../includes/msal-b2c-cors-compatibility-notice.md)]

## <a name="create-the-app-registration"></a>Creare la registrazione dell'app

Sia per le applicazioni basate su MSAL.js 1.0 che per quelle basate su MSAL.js 2.0, iniziare completando la procedura seguente per creare la registrazione iniziale dell'app.

1. Accedere al [portale di Azure](https://portal.azure.com). Se l'account ha accesso a più tenant, selezionare il filtro **Directory e sottoscrizione** nel menu principale e quindi selezionare il tenant che deve contenere la registrazione dell'app che si sta per creare.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Registrazioni app**.
1. Selezionare **Nuova registrazione**, immettere un **Nome** per l'applicazione e scegliere i **Tipi di account supportati** per l'applicazione. **NON** immettere un **URI di reindirizzamento**. Per la descrizione dei diversi tipi di account, vedere [Registrare una nuova applicazione mediante il portale di Azure](quickstart-register-app.md).
1. Selezionare **Registra** per creare la registrazione dell'app.

Configurare quindi la registrazione dell'app con un **URI di reindirizzamento** per specificare dove la piattaforma delle identità Microsoft deve reindirizzare il client insieme agli eventuali token di sicurezza. Usare la procedura appropriata per la versione di MSAL.js che si sta usando nell'applicazione:

- [MSAL.js 2.0 con flusso del codice di autenticazione](#redirect-uri-msaljs-20-with-auth-code-flow) (consigliata)
- [MSAL.js 1.0 con flusso implicito](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>URI di reindirizzamento: [MSAL.js 2,0 con flusso del codice di autenticazione](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

Seguire questa procedura per aggiungere un URI di reindirizzamento per un'app che usa MSAL.js 2.0 o versione successiva. MSAL. JS 2.0+ supporta il flusso del codice di autorizzazione con PKCE e CORS in risposta alle [restrizioni dei cookie di terze parti del browser](reference-third-party-cookies-spas.md). Il flusso di concessione implicito non è supportato in MSAL.js 2.0+.

1. Nel portale di Azure selezionare la registrazione dell'app creata in precedenza in [Creare la registrazione dell'app](#create-the-app-registration).
1. In **Gestisci** selezionare **Autenticazione**, quindi selezionare **Aggiungi una piattaforma**.
1. In **Applicazioni Web** selezionare il riquadro **Applicazione a pagina singola**.
1. In **URI di reindirizzamento** immettere un [URI di reindirizzamento](reply-url.md). **NON** selezionare nessuna delle due caselle di controllo in **Concessione implicita**.
1. Selezionare **Configura** per completare l'aggiunta dell'URI di reindirizzamento.

A questo punto è stata completata la registrazione dell'applicazione a pagina singola (SPA) ed è stato configurato un URI di reindirizzamento, a cui verrà reindirizzato il client e verranno inviati tutti i token di sicurezza. Se si configura l'URI di reindirizzamento tramite il riquadro **Applicazione a pagina singola** nell'area **Aggiungi una piattaforma**, la registrazione dell'applicazione è configurata in modo da supportare il flusso del codice di autorizzazione con PKCE e CORS.

Seguire l' [esercitazione](tutorial-v2-javascript-auth-code.md) per altre istruzioni.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>URI di reindirizzamento: [MSAL.js 1,0 con flusso implicito](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

Seguire questa procedura per aggiungere un URI di reindirizzamento per un'app a pagina singola che usa MSAL.js 1.3 o versioni precedenti e il flusso di concessione implicita. Le applicazioni che usano MSAL.js 1.3 o versioni precedenti non supportano il flusso del codice di autenticazione.

1. Nel portale di Azure selezionare la registrazione dell'app creata in precedenza in [Creare la registrazione dell'app](#create-the-app-registration).
1. In **Gestisci** selezionare **Autenticazione**, quindi selezionare **Aggiungi una piattaforma**.
1. In **Applicazioni Web** selezionare il riquadro **Applicazione a pagina singola**.
1. In **URI di reindirizzamento** immettere un [URI di reindirizzamento](reply-url.md).
1. Abilitare il **Flusso implicito**:
    - Se l'applicazione consente l'accesso agli utenti, selezionare **Token ID**.
    - Se l'applicazione deve anche chiamare un'API Web protetta, selezionare **Token di accesso**. Per altre informazioni su questi tipi di token, vedere [Token ID](id-tokens.md) e [Token di accesso](access-tokens.md).
1. Selezionare **Configura** per completare l'aggiunta dell'URI di reindirizzamento.

A questo punto è stata completata la registrazione dell'applicazione a pagina singola (SPA) ed è stato configurato un URI di reindirizzamento, a cui verrà reindirizzato il client e verranno inviati tutti i token di sicurezza. Selezionando uno o entrambi i **Token ID** e i **Token di accesso**, è stato abilitato il flusso di concessione implicita.

Seguire l' [esercitazione](tutorial-v2-javascript-spa.md) per altre istruzioni.

## <a name="note-about-authorization-flows"></a>Nota sui flussi di autorizzazione

Per impostazione predefinita, la registrazione di un'app creata usando la configurazione della piattaforma dell'applicazione a pagina singola abilita il flusso del codice di autorizzazione. Per sfruttare i vantaggi di questo flusso, l'applicazione deve usare MSAL.js 2.0 o versione successiva.

Come indicato in precedenza, le applicazioni a pagina singola che usano MSAL.js 1.3 sono limitate al flusso di concessione implicita. Le [procedure consigliate OAuth 2.0](v2-oauth2-auth-code-flow.md) correnti consigliano di usare il flusso del codice di autorizzazione anziché il flusso implicito per le applicazioni a pagina singola. La presenza di token di aggiornamento a durata limitata consente anche all'applicazione di adattarsi alle [limitazioni della privacy dei cookie del browser moderni](reference-third-party-cookies-spas.md), ad esempio Safari ITP.

Quando tutte le applicazioni a pagina singola di produzione rappresentate da una registrazione dell'app usano MSAL.js 2.0 e il flusso del codice di autorizzazione, deselezionare le impostazioni di concessione implicita nel riquadro **Autenticazione** della registrazione dell'app nel portale di Azure. Le applicazioni che usano MSAL.js 1.x e il flusso implicito possono tuttavia continuare a funzionare se si lascia il flusso implicito abilitato (selezionato).

## <a name="next-steps"></a>Passaggi successivi

Configurare quindi il codice dell'app per l'uso della registrazione dell'app creata nei passaggi precedenti:.

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-spa-app-configuration.md)
