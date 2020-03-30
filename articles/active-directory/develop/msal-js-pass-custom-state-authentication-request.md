---
title: Passare lo stato personalizzato nelle richieste di autenticazione (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come passare un valore di parametro di stato personalizzato nella richiesta di autenticazione utilizzando la libreria di autenticazione Microsoft per JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084942"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passare lo stato personalizzato nelle richieste di autenticazione usando MSAL.jsPass custom state in authentication requests using MSAL.js

Il parametro *state,* come definito da OAuth 2.0, viene incluso in una richiesta di autenticazione e viene restituito anche nella risposta del token per impedire attacchi di contraffazione delle richieste tra siti. Per impostazione predefinita, Microsoft Authentication Library per JavaScript (MSAL.js) passa un valore di parametro *di stato* univoco generato casualmente nelle richieste di autenticazione.

Il parametro state può essere usato anche per codificare le informazioni sullo stato dell'app prima del reindirizzamento. Puoi passare lo stato dell'utente nell'app, ad esempio la pagina o la visualizzazione in cui si trovava, come input per questo parametro. La libreria MSAL.js consente di passare lo stato `Request` personalizzato come parametro state nell'oggetto:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Se si desidera ignorare un token memorizzato nella cache e `forceRefresh` passare al server, passare il valore booleano nell'oggetto AuthenticationParameters utilizzato per effettuare una richiesta di accesso/token.
> `forceRefresh`non deve essere utilizzato per impostazione predefinita, a causa dell'impatto sulle prestazioni dell'applicazione.
> Affidarsi alla cache offrirà agli utenti un'esperienza migliore.
> Ignorare la cache deve essere usato solo in scenari in cui si sa che i dati attualmente memorizzati nella cache non dispone di informazioni aggiornate.
> Ad esempio uno strumento di amministrazione che aggiunge ruoli a un utente che deve ottenere un nuovo token con ruoli aggiornati.

Ad esempio:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Lo stato passato viene aggiunto al GUID univoco impostato da MSAL.js quando si invia la richiesta. Quando viene restituita la risposta, MSAL.js verifica la corrispondenza dello `Response` stato `accountState`e quindi restituisce lo stato passato personalizzato nell'oggetto come .

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Per ulteriori informazioni, leggere sulla creazione di [un'applicazione a pagina singola (SPA)](scenario-spa-overview.md) utilizzando MSAL.js.