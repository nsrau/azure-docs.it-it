---
title: Passare lo stato personalizzato nelle richieste di autenticazione (Microsoft Authentication Library per JavaScript)
titleSuffix: Microsoft identity platform
description: Informazioni su come passare un valore di parametro di stato personalizzato nella richiesta di autenticazione tramite Microsoft Authentication Library per JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 527c1937da8634f7448c82ca8c0331fb5cfb85e3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150614"
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