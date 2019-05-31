---
title: Passare lo stato personalizzato nelle richieste di autenticazione (Microsoft Authentication Library per JavaScript) | Azure
description: Informazioni su come passare un valore di parametro di stato personalizzato nella richiesta di autenticazione usando Microsoft Authentication Library per JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420499"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passare lo stato personalizzato nelle richieste di autenticazione usando msal. js
Il *stato* parametro, come definito da OAuth 2.0, è incluso in una richiesta di autenticazione e viene anche restituito nella risposta del token per impedire attacchi di richiesta intersito falsa. Per impostazione predefinita, Microsoft Authentication Library per JavaScript (msal) passa un oggetto in modo casuale generato univoco *stato* valore del parametro nelle richieste di autenticazione.

Il parametro di stato è anche utilizzabile per codificare le informazioni di stato dell'app prima di reindirizzamento. È possibile passare lo stato dell'utente dell'App, ad esempio la pagina o la vista che si trovassero nelle, come input per questo parametro. La libreria msal. js consente di passare le informazioni sullo state personalizzate come parametro di stato nel `Request` oggetto:

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

Ad esempio:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

Lo stato passato viene aggiunto il GUID univoco impostato da msal. js quando l'invio della richiesta. Quando viene restituita la risposta, msal. js verifica la corrispondenza con lo stato e quindi restituisce l'oggetto personalizzato passato nello stato nel `Response` dell'oggetto come `accountState`.

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

Per altre informazioni, leggere l'argomento sulle [compilazione di un'applicazione a pagina singola (SPA)](scenario-spa-overview.md) usando msal. js.