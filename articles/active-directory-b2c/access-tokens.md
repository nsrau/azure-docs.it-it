---
title: Richiedere un token di accesso - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come richiedere un token di accesso da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b6adb06f22013e68987f3315d52e3594fba63907
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309024"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Richiedere un token di accesso in Azure Active Directory B2C

Un *token di accesso* contiene attestazioni che è possibile usare in Azure Active Directory B2C (Azure AD B2C) per identificare le autorizzazioni concesse alle API. Quando si chiama un server delle risorse, deve essere presente un token di accesso nella richiesta HTTP. Un token di accesso è indicato come **access_token** nelle risposte di Azure AD B2C.

Questo articolo illustra come richiedere un token di accesso per un'applicazione Web e un'API Web. Per altre informazioni sui token in Azure AD B2C, vedere la [panoramica dei token in Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Le catene di API Web (On-Behalf-Of) non sono supportate da Azure Active Directory B2C.** - Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client che hanno un back-end dell'API Web, che a sua volta chiama un altro servizio. Questo scenario di API Web concatenate può essere supportato tramite la concessione di credenziali del bearer token JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Il flusso On-Behalf-Of, tuttavia, non è attualmente implementato in Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.
- Se non è già stato fatto, [aggiungere un'applicazione API Web al tenant di Azure Active Directory B2C](add-web-api-application.md).

## <a name="scopes"></a>Ambiti

Gli ambiti consentono di gestire le autorizzazioni alle risorse protette. Quando si richiede un token di accesso, l'applicazione client deve specificare le autorizzazioni desiderate nel parametro **ambito** della richiesta. Ad esempio, per specificare il **Valore ambito** di `read` per l'API con **URI ID app** impostato su `https://contoso.onmicrosoft.com/api`, l'ambito sarà `https://contoso.onmicrosoft.com/api/read`.

Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Ad esempio, gli utenti dell'API Web possono avere accesso sia in lettura sia in scrittura oppure possono avere solo accesso in lettura. Per acquisire più autorizzazioni nella stessa richiesta, è possibile aggiungere più voci nell'unico parametro **ambito** della richiesta, separato da spazi.

Nell'esempio seguente vengono illustrati gli ambiti decodificati in un URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Nell'esempio seguente vengono illustrati gli ambiti codificati in un URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Se si richiedono più ambiti rispetto a quelli concessi per l'applicazione client, la chiamata ha esito positivo se viene concessa almeno un'autorizzazione. L'attestazione **scp** del token di accesso risultante viene popolata con solo le autorizzazioni concesse. 

### <a name="openid-connect-scopes"></a>Ambiti di OpenID Connect

Lo standard OpenID Connect consente di specificare valori speciali diversi per l'ambito. Gli ambiti seguenti rappresentano l'autorizzazione per accedere al profilo dell'utente:

- **openid** - Richiede un token ID.
- **offline_access** - Richiede un token di aggiornamento mediante i [flussi del codice di autorizzazione](authorization-code-flow.md).
- **00000000-0000-0000-0000-000000000000** : l'uso dell'ID client come ambito indica che l'app necessita di un token di accesso che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client.

Se il parametro **response_type** in una richiesta `/authorize` include `token`, il parametro **ambito** deve includere almeno un ambito delle risorse diverso da `openid` e `offline_access` che verrà concesso. In caso contrario, la richiesta `/authorize` ha esito negativo.

## <a name="request-a-token"></a>Richiedere un token

Per richiedere un token di accesso è necessario un codice di autorizzazione. Di seguito è riportato un esempio di richiesta di un codice di autorizzazione all'endpoint `/authorize`. I domini personalizzati non sono supportati per l'uso insieme ai token di accesso. Usare il dominio tenant-name.onmicrosoft.com nell'URL della richiesta.

Nell'esempio seguente, sostituire questi valori:

- `<tenant-name>`: il nome del tenant di Azure AD B2C.
- `<policy-name>`: il nome del flusso utente o dei criteri personalizzati.
- `<application-ID>`: l'identificatore applicazione dell'applicazione Web registrata per supportare il flusso utente.
- `<redirect-uri>`: l'**URI di reindirizzamento** immesso al momento della registrazione dell'applicazione client.

```http
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

La risposta con il codice di autorizzazione dovrebbe essere simile a questo esempio:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Dopo aver ricevuto correttamente il codice di autorizzazione, è possibile usarlo per richiedere un token di accesso:

```http
POST <tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Verrà visualizzato un testo simile alla seguente risposta:

```json
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

Quando si usa https://jwt.ms per esaminare il token di accesso restituito, verrà visualizzato un testo simile all'esempio seguente:

```json
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
