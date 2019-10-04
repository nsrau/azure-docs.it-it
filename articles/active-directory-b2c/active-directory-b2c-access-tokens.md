---
title: Richiedere un token di accesso-Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come richiedere un token di accesso da Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 042c4fa18ce583f714bbe71f522b1f8f1af3dfdb
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066114"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Richiedere un token di accesso in Azure Active Directory B2C

Un *token di accesso* contiene attestazioni che è possibile usare in Azure Active Directory B2C (Azure ad B2C) per identificare le autorizzazioni concesse alle API. Quando si chiama un server di risorse, nella richiesta HTTP deve essere presente un token di accesso. Un token di accesso è indicato come **access_token** nelle risposte da Azure ad B2C.

Questo articolo illustra come richiedere un token di accesso per un'applicazione Web e un'API Web. Per ulteriori informazioni sui token in Azure AD B2C, vedere la [Panoramica dei token in Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Le catene di API Web (On-Behalf-Of) non sono supportate da Azure Active Directory B2C.** -Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client che hanno un back-end dell'API Web, che a sua volta chiama un altro servizio. Questo scenario di API Web concatenate può essere supportato tramite la concessione di credenziali del bearer token JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Il flusso On-Behalf-Of, tuttavia, non è attualmente implementato in Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.
- Se non è già stato fatto, [aggiungere un'applicazione API Web al tenant di Azure Active Directory B2C](add-web-application.md).

## <a name="scopes"></a>Ambiti

Gli ambiti consentono di gestire le autorizzazioni per le risorse protette. Quando viene richiesto un token di accesso, l'applicazione client deve specificare le autorizzazioni desiderate nel parametro **scope** della richiesta. Ad esempio, per specificare il **valore** di `read` ambito per l'API con l' **URI ID app** di `https://contoso.onmicrosoft.com/api`, l'ambito sarà. `https://contoso.onmicrosoft.com/api/read`

Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Ad esempio, gli utenti dell'API Web possono avere accesso sia in lettura sia in scrittura oppure possono avere solo accesso in lettura. Per acquisire più autorizzazioni nella stessa richiesta, è possibile aggiungere più voci nel parametro singolo **ambito** della richiesta, separate da spazi.

Nell'esempio seguente vengono illustrati gli ambiti decodificati in un URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Nell'esempio seguente vengono illustrati gli ambiti codificati in un URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Se si richiedono più ambiti rispetto a quelli concessi per l'applicazione client, la chiamata ha esito positivo se almeno un'autorizzazione viene concessa. L'attestazione **SCP** nel token di accesso risultante viene popolata solo con le autorizzazioni concesse correttamente. Lo standard OpenID Connect specifica diversi valori di ambito speciali. Gli ambiti seguenti rappresentano l'autorizzazione per accedere al profilo dell'utente:

- **OpenID** : richiede un token ID.
- **offline_access** : richiede un token di aggiornamento usando [flussi del codice di autenticazione](active-directory-b2c-reference-oauth-code.md).

Se il parametro **response_type** in una `/authorize` richiesta include `token`, il parametro **scope** deve includere almeno un ambito di `openid` risorse diverso da e `offline_access` che verrà concesso. In caso contrario `/authorize` , la richiesta ha esito negativo.

## <a name="request-a-token"></a>Richiedere un token

Per richiedere un token di accesso, è necessario un codice di autorizzazione. Di seguito è riportato un esempio di una richiesta `/authorize` all'endpoint per un codice di autorizzazione. I domini personalizzati non sono supportati per l'uso con i token di accesso. Usare il dominio tenant-name.onmicrosoft.com nell'URL della richiesta.

Nell'esempio seguente, sostituire questi valori:

- `<tenant-name>`: il nome del tenant di Azure AD B2C.
- `<policy-name>`: il nome del flusso utente o dei criteri personalizzati.
- `<application-ID>`: Identificatore dell'applicazione Web registrato per supportare il flusso utente.
- `<redirect-uri>`: l'**URI di reindirizzamento** immesso al momento della registrazione dell'applicazione client.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

La risposta con il codice di autorizzazione dovrebbe essere simile a questo esempio:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Dopo aver ricevuto il codice di autorizzazione, è possibile usarlo per richiedere un token di accesso:

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Dovrebbe essere visualizzata una risposta simile alla seguente:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Quando si https://jwt.ms USA per esaminare il token di accesso restituito, verrà visualizzato un risultato simile all'esempio seguente:

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare i token in Azure ad B2C](configure-tokens.md)
