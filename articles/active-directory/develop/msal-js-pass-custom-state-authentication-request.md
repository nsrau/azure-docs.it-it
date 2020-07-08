---
title: Passare lo stato personalizzato nelle richieste di autenticazione (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come passare un valore di parametro di stato personalizzato nella richiesta di autenticazione tramite Microsoft Authentication Library per JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 840c371e63aacf8ef410cbf84cc9f68137dd77df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477584"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passa lo stato personalizzato nelle richieste di autenticazione usando MSAL.js

Il parametro *state* , come definito da OAuth 2,0, è incluso in una richiesta di autenticazione e viene anche restituito nella risposta del token per evitare attacchi di richiesta intersito falsa. Per impostazione predefinita, Microsoft Authentication Library per JavaScript (MSAL.js) passa un valore di parametro di *stato* univoco generato in modo casuale nelle richieste di autenticazione.

Il parametro state può essere usato anche per codificare le informazioni dello stato dell'app prima del reindirizzamento. È possibile passare lo stato dell'utente nell'app, ad esempio la pagina o la vista in cui si trovavano, come input per questo parametro. La libreria MSAL.js consente di passare lo stato personalizzato come parametro di stato nell' `Request` oggetto:

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
> Se si vuole ignorare un token memorizzato nella cache e passare al server, passare il valore booleano nell' `forceRefresh` oggetto AuthenticationParameters usato per effettuare una richiesta di accesso/token.
> `forceRefresh`non deve essere usato per impostazione predefinita, a causa dell'effetto sulle prestazioni dell'applicazione.
> Affidarsi alla cache offrirà agli utenti un'esperienza migliore.
> Ignorare la cache deve essere utilizzata solo negli scenari in cui si sa che i dati attualmente memorizzati nella cache non contengono informazioni aggiornate.
> Ad esempio uno strumento di amministrazione che aggiunge ruoli a un utente che deve ottenere un nuovo token con i ruoli aggiornati.

Ad esempio:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Lo stato passato viene aggiunto al GUID univoco impostato da MSAL.js durante l'invio della richiesta. Quando viene restituita la risposta, MSAL.js controlla la presenza di una corrispondenza di stato e quindi restituisce lo stato personalizzato passato nell' `Response` oggetto come `accountState` .

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

Per altre informazioni, vedere [la pagina relativa alla creazione di un'applicazione a singola pagina (Spa)](scenario-spa-overview.md) con MSAL.js.