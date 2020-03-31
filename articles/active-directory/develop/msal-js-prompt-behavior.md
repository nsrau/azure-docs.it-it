---
title: Comportamento della richiesta interattiva (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come personalizzare il comportamento dei prompt nelle chiamate interattive usando La libreria di autenticazione Microsoft per JavaScript (MSAL.js).
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
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695977"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamento di prompt nelle richieste interattive MSAL.jsPrompt behavior in MSAL.js interactive requests

Quando un utente ha stabilito una sessione di Azure AD attiva con più account utente, la pagina di accesso di Azure AD richiederà per impostazione predefinita all'utente di selezionare un account prima di procedere all'accesso. Gli utenti non vedranno un'esperienza di selezione dell'account se è presente una sola sessione autenticata con Azure AD.

La libreria MSAL.js (a partire dalla v0.2.4) non invia`loginRedirect` `loginPopup`un `acquireTokenRedirect` `acquireTokenPopup`parametro prompt durante le richieste interattive ( , e ) e perquesto non applica alcun comportamento di prompt. Per le richieste `acquireTokenSilent` di token invisibile all'utente che `none`utilizzano il metodo , MSAL.js passa un parametro prompt impostato su .

In base allo scenario dell'applicazione, è possibile controllare il comportamento di prompt per le richieste interattive impostando il parametro prompt nei parametri di richiesta passati ai metodi. Ad esempio, se si desidera richiamare l'esperienza di selezione dell'account:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Durante l'autenticazione con Azure AD è possibile passare i valori di prompt seguenti:The following prompt values can be passed when authenticating with Azure AD:

**login:** Questo valore forzerà l'utente a immettere le credenziali nella richiesta di autenticazione.

**select_account:** Questo valore fornirà all'utente un'esperienza di selezione dell'account che elenca tutti gli account in sessione.

**consenso:** Questo valore richiamerà la finestra di dialogo di consenso OAuth che consente agli utenti di concedere autorizzazioni per l'app.

**nessuno:** Questo valore garantirà che l'utente non visualizzi alcun prompt interattivo. Si consiglia di non passare questo valore ai metodi interattivi in MSAL.js in quanto può avere comportamenti imprevisti. Utilizzare invece `acquireTokenSilent` il metodo per ottenere chiamate silenziose.

## <a name="next-steps"></a>Passaggi successivi

Per altre `prompt` informazioni sul parametro, vedere il protocollo [di concessione implicita OAuth 2.0](v2-oauth2-implicit-grant-flow.md) utilizzato dalla libreria MSAL.js.
