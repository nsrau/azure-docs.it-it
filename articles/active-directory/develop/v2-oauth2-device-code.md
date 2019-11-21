---
title: Use Microsoft identity platform to sign in users on browser-less devices | Azure
description: Build embedded and browser-less authentication flows using the device authorization grant.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c948c59a90e0db17b4704188221cfc3c3d82310
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207612"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft identity platform and the OAuth 2.0 device authorization grant flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

The Microsoft identity platform supports the [device authorization grant](https://tools.ietf.org/html/rfc8628), which allows users to sign in to input-constrained devices such as a smart TV, IoT device, or printer.  Per abilitare questo flusso, il dispositivo richiede all'utente di visitare una pagina Web nel browser in un altro dispositivo per l'accesso.  Una volta che l'utente avrà eseguito l'accesso, il dispositivo potrà ottenere i token di accesso e di aggiornamento in base alle esigenze.  

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!NOTE]
> The Microsoft identity platform endpoint doesn't support all Azure Active Directory scenarios and features. To determine whether you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagramma di protocollo

L'intero flusso del codice del dispositivo ha un aspetto simile a quello illustrato nel diagramma seguente. Tutti i passaggi vengono descritti in seguito nell'articolo.

![Flusso del codice del dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Richiesta di autorizzazione del dispositivo

The client must first check with the authentication server for a device and user code that's used to initiate authentication. Il client raccoglie la richiesta dall'endpoint `/devicecode`. In questa richiesta, il client deve includere anche le autorizzazioni che deve acquisire dall'utente. Dal momento in cui la richiesta viene inviata, l'utente ha solo 15 minuti per eseguire l'accesso (il valore consueto per `expires_in`), quindi effettuare questa richiesta solo quando l'utente ha indicato di essere pronto a eseguire l'accesso.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametro | Condizione | Description |
| --- | --- | --- |
| `tenant` | Obbligatoria | Can be /common, /consumers, or /organizations.  It can also be the directory tenant that you want to request permission from in GUID or friendly name format.  |
| `client_id` | Obbligatoria | The **Application (client) ID** that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience assigned to your app. |
| `scope` | Consigliato | Elenco separato da spazi di [ambiti](v2-permissions-and-consent.md) a cui si vuole che l'utente dia il consenso.  |

### <a name="device-authorization-response"></a>Risposta di autorizzazione dispositivo

Una risposta con esito positivo sarà un oggetto JSON contenente le informazioni richieste per consentire all'utente di accedere.  

| Parametro | Format | Description |
| ---              | --- | --- |
|`device_code`     | Stringa | Una stringa lunga usata per verificare la sessione tra il client e il server di autorizzazione. The client uses this parameter to request the access token from the authorization server. |
|`user_code`       | Stringa | A short string shown to the user that's used to identify the session on a secondary device.|
|`verification_uri`| URI | L'URI a cui l'utente deve passare con il `user_code` per eseguire l'accesso. |
|`expires_in`      | int | Il numero di secondi prima della scadenza del `device_code` e del `user_code`. |
|`interval`        | int | Il numero di secondi di attesa del client tra le richieste di polling. |
| `message`        | Stringa | Una stringa leggibile dall'utente con le istruzioni per l'utente. Può essere localizzata includendo un **parametro di query** nella richiesta del form `?mkt=xx-XX`, compilando l'apposito codice della lingua di destinazione. |

> [!NOTE]
> The `verification_uri_complete` response field is not included or supported at this time.  We mention this because if you read the [standard](https://tools.ietf.org/html/rfc8628) you see that `verification_uri_complete` is listed as an optional part of the device code flow standard.

## <a name="authenticating-the-user"></a>Autenticazione dell'utente

After receiving the `user_code` and `verification_uri`, the client displays these to the user, instructing them to sign in using their mobile phone or PC browser.

If the user authenticates with a personal account (on /common or /consumers), they will be asked to sign in again in order to transfer authentication state to the device.  They will also be asked to provide consent, to ensure they are aware of the permissions being granted.  This does not apply to work or school accounts used to authenticate. 

Mentre l'utente esegue l'autenticazione all'`verification_uri`, il client deve eseguire il polling dell'endpoint `/token`per il token richiesto usando il `device_code`.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametro | Obbligatoria | Description|
| -------- | -------- | ---------- |
| `tenant`  | Obbligatoria | The same tenant or tenant alias used in the initial request. | 
| `grant_type` | Obbligatoria | Deve essere `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obbligatoria | Deve corrispondere al `client_id` usato nella richiesta iniziale. |
| `device_code`| Obbligatoria | Il `device_code` restituito nella richiesta di autorizzazione del dispositivo.  |

### <a name="expected-errors"></a>Errori previsti

The device code flow is a polling protocol so your client must expect to receive errors before the user has finished authenticating.  

| Errore | Description | Azione client |
| ------ | ----------- | -------------|
| `authorization_pending` | The user hasn't finished authenticating, but hasn't canceled the flow. | Ripetere la richiesta dopo almeno `interval` secondi. |
| `authorization_declined` | L'utente finale ha rifiutato la richiesta di autorizzazione.| Arrestare il polling e ripristinare uno stato non autenticato.  |
| `bad_verification_code`| The `device_code` sent to the `/token` endpoint wasn't recognized. | Verificare che il client stia inviando il `device_code` corretto nella richiesta. |
| `expired_token` | Sono trascorsi almeno `expires_in` secondi e l'autenticazione non è più possibile con questo `device_code`. | Stop polling and revert to an unauthenticated state. |   

### <a name="successful-authentication-response"></a>Risposta di autenticazione con esito positivo

Una risposta token con esito positivo ha un aspetto simile al seguente:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parametro | Format | Description |
| --------- | ------ | ----------- |
| `token_type` | Stringa| Sempre "Bearer". |
| `scope` | Stringhe separate da uno spazio | Se è stato restituito un token di accesso, verranno elencati gli ambiti per cui è valido. |
| `expires_in`| int | Numero di secondi per cui il token di accesso incluso verrà considerato valido. |
| `access_token`| Stringa opaca | Emessa per gli [ambiti](v2-permissions-and-consent.md) che sono stati richiesti.  |
| `id_token`   | Token JSON Web | Emessa nel parametro `scope` originale incluso nell'ambito `openid`.  |
| `refresh_token` | Stringa opaca | Emessa nel parametro `scope` originale incluso `offline_access`.  |

You can use the refresh token to acquire new access tokens and refresh tokens using the same flow documented in the [OAuth Code flow documentation](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
