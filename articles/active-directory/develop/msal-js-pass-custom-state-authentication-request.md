---
title: Passare lo stato personalizzato nelle richieste di autenticazione (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come passare un valore di parametro di stato personalizzato nella richiesta di autenticazione tramite Microsoft Authentication Library per JavaScript (MSAL. js).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4cb0f3d054f9afd0c606f80fd6fc5d553eff806
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916316"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passare lo stato personalizzato nelle richieste di autenticazione con MSAL. js
Il parametro *state* , come definito da OAuth 2,0, è incluso in una richiesta di autenticazione e viene anche restituito nella risposta del token per evitare attacchi di richiesta intersito falsa. Per impostazione predefinita, Microsoft Authentication Library per JavaScript (MSAL. js) passa un valore di parametro di *stato* univoco generato in modo casuale nelle richieste di autenticazione.

Il parametro state può essere usato anche per codificare le informazioni dello stato dell'app prima del reindirizzamento. È possibile passare lo stato dell'utente nell'app, ad esempio la pagina o la vista in cui si trovavano, come input per questo parametro. La libreria MSAL. js consente di passare lo stato personalizzato come parametro di stato nell'oggetto `Request`:

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
};
```

ad esempio:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Lo stato passato viene aggiunto al GUID univoco impostato da MSAL. js quando si invia la richiesta. Quando viene restituita la risposta, MSAL. js verifica la presenza di una corrispondenza di stato e quindi restituisce lo stato personalizzato passato nell'oggetto `Response` come `accountState`.

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