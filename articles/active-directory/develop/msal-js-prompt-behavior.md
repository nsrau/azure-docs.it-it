---
title: Richiedere il comportamento nelle richieste interattive (Microsoft Authentication Library per JavaScript) | Azure
description: Informazioni sulla personalizzazione dei messaggi di richiesta comportamento nelle chiamate interattive usando Microsoft Authentication Library per JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544278"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamento delle richieste in richieste interattive msal. js

Quando un utente ha stabilito una Azure active sessione AD più account utente, nella pagina di accesso di Azure AD richiederà per impostazione predefinita all'utente di selezionare un account prima di procedere per l'accesso. Gli utenti non visualizzeranno una selezione account verificano se c'è solo una singola sessione autenticata con Azure AD.

La libreria msal. js (a partire v0.2.4) non viene inviato un parametro di richiesta durante le richieste interattive (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` e `acquireTokenPopup`) e in tal modo, non comporta alcun comportamento della richiesta. Per le richieste di token invisibile all'utente tramite il `acquireTokenSilent` metodo, msal. js passa un parametro di richiesta impostato su `none`.

A seconda dello scenario dell'applicazione, è possibile controllare il comportamento della richiesta per le richieste interattive impostando il parametro prompt nei parametri di richiesta passati ai metodi. Ad esempio, se si vuole richiamare l'esperienza di selezione account:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


I valori dei messaggi di richiesta seguenti possono essere passati durante l'autenticazione con Azure AD:

**login:** Questo valore si imporrà all'utente di immettere le credenziali su richiesta di autenticazione.

**select_account:** Questo valore fornirà all'utente un'esperienza di selezione account Elenca tutti gli account nella sessione.

**consent:** Questo valore verrà richiamato il dialogo di consenso di OAuth che consente agli utenti di concedere le autorizzazioni per l'app.

**None:** Questo valore assicura che l'utente non visualizza alcuna richiesta interattiva. È consigliabile non passare questo valore a metodi interattivi in msal. js, come possono avere comportamenti imprevisti. Usare invece il `acquireTokenSilent` metodo per ottenere le chiamate invisibile all'utente.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni, vedere la `prompt` parametro il [concessione implicita OAuth 2.0](v2-oauth2-implicit-grant-flow.md) protocollo Usa la libreria msal. js.
