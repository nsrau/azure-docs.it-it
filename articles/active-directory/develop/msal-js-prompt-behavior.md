---
title: Comportamento della richiesta interattiva delle richieste (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come personalizzare il comportamento della richiesta nelle chiamate interattive usando Microsoft Authentication Library per JavaScript (MSAL. js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6654b523cef4623a55bf9f857fd5a723fae2dc47
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921960"
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
