---
title: Richiedere un token di accesso - Azure Active Directory B2C | Microsoft Docs
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
ms.openlocfilehash: 1a545f1e0fd1360d9147280454fb8b75bf216152
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66507383"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Richiedere un token di accesso in Azure Active Directory B2C

Un' *token di accesso* contiene le attestazioni che è possibile usare in Azure Active Directory (Azure AD) B2C per identificare le autorizzazioni concesse alle API. Quando si chiama un server di risorse, un token di accesso deve essere presente nella richiesta HTTP. Un token di accesso è indicato come **access_token** nelle risposte da Azure AD B2C. 

Questo articolo illustra come richiedere un token di accesso per un'applicazione web e API web. Per altre informazioni sui token in Azure AD B2C, vedere la [panoramica dei token in Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Le catene di API Web (On-Behalf-Of) non sono supportate da Azure Active Directory B2C.** -Molte architetture includono un'API web che deve chiamare un'altra API web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client che dispongono di un'API web back-end, che a sua volta chiama un altro servizio. Questo scenario di API Web concatenate può essere supportato tramite la concessione di credenziali del bearer token JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Il flusso On-Behalf-Of, tuttavia, non è attualmente implementato in Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.
- Se non è già fatto, [aggiungere un'applicazione API al tenant di Azure Active Directory B2C web](add-web-application.md).

## <a name="scopes"></a>Ambiti

Gli ambiti consentono di gestire le autorizzazioni alle risorse protette. Quando viene richiesto un token di accesso, l'applicazione client deve specificare le autorizzazioni desiderate nel **ambito** parametro della richiesta. Ad esempio, per specificare il **valore ambito** dei `read` per l'API con il **URI ID App** di `https://contoso.onmicrosoft.com/api`, l'ambito sarà `https://contoso.onmicrosoft.com/api/read`.

Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Ad esempio, gli utenti dell'API Web possono avere accesso sia in lettura sia in scrittura oppure possono avere solo accesso in lettura. Per acquisire più autorizzazioni nella stessa richiesta, è possibile aggiungere più voci nell'unico **ambito** parametro della richiesta, separata da spazi.

Nell'esempio seguente mostra gli ambiti in un URL decodificati:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Nell'esempio seguente mostra gli ambiti con codificati in un URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Se si richiedono più ambiti di quella concessa per l'applicazione client, la chiamata ha esito positivo se almeno un'autorizzazione viene concessa. Il **scp** attestazione nel token di accesso risultante viene popolata con solo le autorizzazioni che sono state concesse correttamente. Lo standard OpenID Connect consente di specificare diversi valori di ambito speciali. Gli ambiti seguenti rappresentano l'autorizzazione per accedere al profilo dell'utente:

- **openid** -richiede un token ID.
- **offline_access** -richiede un token di aggiornamento utilizzando [flussi di codice di autenticazione](active-directory-b2c-reference-oauth-code.md).

Se il **response_type** parametro in un `/authorize` richiesta include `token`, il **ambito** parametro deve includere almeno una risorsa ambito diverso da `openid` e`offline_access`che verrà concesso. In caso contrario, il `/authorize` richiesta ha esito negativo.

## <a name="request-a-token"></a>Richiedere un token

Per richiedere un token di accesso, è necessario un codice di autorizzazione. Di seguito è riportato un esempio di una richiesta per il `/authorize` endpoint per un codice di autorizzazione. Domini personalizzati non sono supportati per l'uso con i token di accesso. Usare il dominio tenant name.onmicrosoft.com nell'URL della richiesta.

Nell'esempio seguente, sostituire questi valori:

- `<tenant-name>`: il nome del tenant di Azure AD B2C.
- `<policy-name>`: il nome del flusso utente o dei criteri personalizzati.
- `<application-ID>` -L'identificatore dell'applicazione dell'applicazione web che sono state registrate per supportare il flusso utente.
- `<redirect-uri>`: l'**URI di reindirizzamento** immesso al momento della registrazione dell'applicazione client.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

La risposta con codice di autorizzazione dovrebbe essere simile a questo esempio:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Dopo aver ricevuto correttamente il codice di autorizzazione, è possibile usarla per richiedere un token di accesso:

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Verrà visualizzata una schermata simile alla risposta seguente:

```
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

Quando si usa https://jwt.ms per esaminare il token di accesso che è stato restituito, si dovrebbe vedere qualcosa di simile all'esempio seguente:

```
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

- Informazioni su come [configurare i token in Azure AD B2C](configure-tokens.md)
