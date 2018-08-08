---
title: Eseguire l'autenticazione con Azure AD e ottenere un token JWT usando OAuth 2.0
description: Codice di esempio che mostra come eseguire l'autenticazione con Azure Active Directory usando OAuth 2.0 per accedere alle applicazioni Web protette e alle API Web dell'organizzazione.
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: cbc86eb6d13034fb3154ed8e9d021064f1d15ece
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39267111"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>Richiedere un token di accesso usando OAuth 2.0 per accedere alle API e alle applicazioni Web protette da Azure Active Directory

Questo articolo illustra come ottenere un token JSON Web, ovvero JWT, per accedere alle risorse protette da Azure AD. Si presuppone che l'utente abbia un [token di autorizzazione](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) ottenuto dall'autorizzazione concessa dall'utente o tramite un'[entità servizio](/azure/active-directory/develop/active-directory-application-objects).

## <a name="build-the-request"></a>Compilare la richiesta

Usare la richiesta HTTP `POST` seguente per ottenere un token JWT per accedere a una specifica applicazione o a un'API protetta da Azure AD.

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>Intestazioni della richiesta

Gli argomenti seguenti sono obbligatori:

|Intestazione della richiesta|DESCRIZIONE|  
|--------------------|-----------------|  
| *Host:* | https://login.microsoftonline.com |
| *Content-Type:*| application/x-www-form-urlencoded |
 

### <a name="uri-parameters"></a>Parametri URI

| Parametro     |                       | DESCRIZIONE                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant        | Obbligatoria              | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | Obbligatoria              | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) ha assegnato all'app.                                                                                                                                                                                                                             |
| grant_type    | Obbligatoria              | Deve essere `authorization_code` per il flusso del codice di autorizzazione.                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | Obbligatoria              | Elenco di ambiti separati da spazi. Gli ambiti richiesti in questa sezione devono essere equivalenti agli ambiti o a un sottoinsieme di questi ultimi nella chiamata a `/authorize`. Se gli ambiti specificati in questa richiesta si estendono su più server di risorse, l'endpoint v2.0 restituirà un token per la risorsa specificata nel primo ambito. Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](active-directory-v2-scopes.md). |
| code          | Obbligatoria              | Codice di autorizzazione acquisito durante la chiamata a `/authorize`                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | Obbligatoria              | Stesso valore redirect_uri usato per acquisire il codice di autorizzazione.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | Obbligatorio per app Web | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. Non usarlo in un'app nativa, perché i segreti client non possono essere archiviati in modo affidabile nei dispositivi. Il segreto è obbligatorio per le app Web e le API Web che possono archiviare in modo sicuro il segreto client sul lato server.  Prima di essere inviati, i segreti client devono essere codificati come URL.                                                                                 |
| code_verifier | Facoltativo              | Stesso code_verifier usato per ottenere il codice di autorizzazione. Obbligatorio se nella richiesta di concessione del codice di autorizzazione è stato usato PKCE. Per altre informazioni, vedere il [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>Gestire la risposta

Una risposta del token corretta conterrà un token JWT e avrà un aspetto simile:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametro     | DESCRIZIONE                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'.                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Validità del token di accesso (espressa in secondi).                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | Ambiti per i quali il token di accesso è valido.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token di accesso aggiuntivi dopo la scadenza del token di accesso corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare l'argomento [Anteprima Azure AD B2C: Riferimento al Token](active-directory-v2-tokens.md). <br> **Nota:** viene fornito solo è stato richiesto l'ambito `offline_access`.                                               |
| id_token      | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sui token ID, vedere le [informazioni di riferimento sui token dell'endpoint v2.0](active-directory-v2-tokens.md). <br> **Nota:** viene fornito solo è stato richiesto l'ambito `openid`. |



