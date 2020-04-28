---
title: Passare lo stato personalizzato nelle richieste di autenticazione (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come passare un valore di parametro di stato personalizzato nella richiesta di autenticazione tramite Microsoft Authentication Library per JavaScript (MSAL. js).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084942"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passare lo stato personalizzato nelle richieste di autenticazione con MSAL. js

Il parametro *state* , come definito da OAuth 2,0, è incluso in una richiesta di autenticazione e viene anche restituito nella risposta del token per evitare attacchi di richiesta intersito falsa. Per impostazione predefinita, Microsoft Authentication Library per JavaScript (MSAL. js) passa un valore di parametro di *stato* univoco generato in modo casuale nelle richieste di autenticazione.

Il parametro state può essere usato anche per codificare le informazioni dello stato dell'app prima del reindirizzamento. È possibile passare lo stato dell'utente nell'app, ad esempio la pagina o la vista in cui si trovavano, come input per questo parametro. La libreria MSAL. js consente di passare lo stato personalizzato come parametro di stato nell' `Request` oggetto:

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
> Se si vuole ignorare un token memorizzato nella cache e passare al server, passare il valore booleano `forceRefresh` nell'oggetto AuthenticationParameters usato per effettuare una richiesta di accesso/token.
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

Lo stato passato viene aggiunto al GUID univoco impostato da MSAL. js quando si invia la richiesta. Quando viene restituita la risposta, MSAL. js verifica la presenza di una corrispondenza di stato e quindi restituisce lo stato personalizzato `Response` passato nell' `accountState`oggetto come.

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

Per altre informazioni, vedere [la pagina relativa alla creazione di un'applicazione a singola pagina (Spa)](scenario-spa-overview.md) con MSAL. js.