---
title: Comportamento della richiesta nelle richieste interattive (Microsoft Authentication Library per JavaScript)
titleSuffix: Microsoft identity platform
description: Informazioni sulla personalizzazione del comportamento del prompt in chiamate interattive tramite Microsoft Authentication Library per JavaScript (MSAL. js).
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
ms.openlocfilehash: 42d6c4415a3eeb28c999d95b838c6dd7c0f6e606
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803018"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamento della richiesta nelle richieste interattive MSAL. js

Quando un utente ha stabilito una sessione di Azure AD attiva con più account utente, per impostazione predefinita nella pagina di accesso Azure AD verrà richiesto all'utente di selezionare un account prima di procedere con l'accesso. Gli utenti non vedranno un'esperienza di selezione dell'account se è presente una sola sessione autenticata con Azure AD.

La libreria MSAL. js (a partire da v 0.2.4) non invia un parametro di richiesta durante le richieste interattive (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` e `acquireTokenPopup`) e pertanto non applica alcun comportamento della richiesta. Per le richieste di token invisibile all'utente che usano il metodo `acquireTokenSilent`, MSAL. js passa un parametro prompt impostato su `none`.

In base allo scenario dell'applicazione, è possibile controllare il comportamento del prompt per le richieste interattive impostando il parametro prompt nei parametri della richiesta passati ai metodi. Ad esempio, se si desidera richiamare l'esperienza di selezione dell'account:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Quando si esegue l'autenticazione con Azure AD, è possibile passare i valori di richiesta seguenti:

**account di accesso:** Questo valore impone all'utente di immettere le credenziali per la richiesta di autenticazione.

**select_account:** Questo valore fornirà all'utente un'esperienza di selezione dell'account che elenca tutti gli account della sessione.

**consenso:** Questo valore richiama la finestra di dialogo di consenso di OAuth che consente agli utenti di concedere le autorizzazioni all'app.

**nessuno:** Questo valore garantisce che l'utente non visualizzi alcun prompt interattivo. Si consiglia di non passare questo valore ai metodi interattivi in MSAL. js poiché può presentare comportamenti imprevisti. Usare invece il metodo `acquireTokenSilent` per ottenere le chiamate invisibili.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul parametro `prompt`, vedere il protocollo di [concessione implicita OAuth 2,0](v2-oauth2-implicit-grant-flow.md) usato dalla libreria MSAL. js.
