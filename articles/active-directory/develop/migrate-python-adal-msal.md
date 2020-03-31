---
title: Guida alla migrazione da Python ADAL a MSAL Azure
description: Informazioni su come eseguire la migrazione dell'app Python di Azure Active Directory Authentication Library (ADAL) a Microsoft Authentication Library (MSAL) per Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696558"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Guida alla migrazione da ADAL a MSAL per Python

Questo articolo evidenzia le modifiche che è necessario apportare per eseguire la migrazione di un'app che usa Azure Active Directory Authentication Library (ADAL) per usare microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Punti salienti della differenza

ADAL funziona con l'endpoint Azure Active Directory (Azure AD) v1.0. Microsoft Authentication Library (MSAL) funziona con la piattaforma di identità Microsoft, precedentemente nota come endpoint di Azure Active Directory 2.0. La piattaforma di identità Microsoft è diversa da Azure AD v1.0 in quanto:The Microsoft identity platform differs from Azure AD v1.0 in that it:

Supporto:
  - Account aziendali e degli istituti di istruzione (di cui Azure AD ha effettuato il provisioning)
  - Account personali (ad esempio, di Outlook.com o Hotmail.com)
  - Clienti che portano il loro indirizzo di posta elettronica o la loro identità di social networking (ad esempio, di LinkedIn, Facebook o Google) tramite l'offerta Azure AD B2C

- Gli standard sono compatibili con:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Per altre informazioni, vedere [Qual è la differenza nell'endpoint Microsoft Identity Platform (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

### <a name="scopes-not-resources"></a>Ambiti e non risorse

ADAL Python acquisisce i token per le risorse, ma MSAL Python acquisisce i token per gli ambiti. La superficie API in MSAL Python non dispone più del parametro di risorsa. È necessario fornire ambiti come un elenco di stringhe che dichiarano le autorizzazioni e le risorse desiderate richieste. Per visualizzare alcuni esempi di ambiti, vedere [gli ambiti di Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Gestione degli errori

Azure Active Directory Authentication Library (ADAL) `AdalError` per Python usa l'eccezione per indicare che si è verificato un problema. MSAL per Python utilizza in genere i codici di errore, invece. Per ulteriori informazioni, vedere [MSAL per Python gestione degli errori](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>Modifiche all'API

La tabella seguente elenca un'API in ADAL per Python e quella da utilizzare al suo posto in MSAL per Python:

| API ADAL per Python  | API MSAL per Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication o ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/D  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/D |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) e [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) e [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/D | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/D | Cache con persistenza, disponibile dalle [estensioni MSALCache](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) with persistence, available from MSAL Extensions |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Eseguire la migrazione di token di aggiornamento esistenti per MSAL PythonMigrate existing refresh tokens for MSAL Python

La libreria di autenticazione Microsoft (MSAL) astrae il concetto di token di aggiornamento. MSAL Python fornisce una cache dei token in memoria per impostazione predefinita in modo che non sia necessario archiviare, cercare o aggiornare i token di aggiornamento. Gli utenti vedranno anche un numero inferiore di richieste di accesso perché i token di aggiornamento possono in genere essere aggiornati senza l'intervento dell'utente. Per ulteriori informazioni sulla cache dei token, vedere [Serializzazione della cache dei token personalizzata in MSAL per Python](msal-python-token-cache-serialization.md).

Il codice seguente consente di eseguire la migrazione dei token di aggiornamento gestiti da un'altra libreria OAuth2 (incluso ma non limitato ad ADAL Python) per essere gestiti da MSAL per Python. Uno dei motivi per la migrazione di tali token di aggiornamento consiste nell'impedire agli utenti esistenti di dover accedere di nuovo quando si esegue la migrazione dell'app a MSAL per Python.

Il metodo per la migrazione di un token di aggiornamento consiste nell'utilizzare MSAL per Python per acquisire un nuovo token di accesso usando il token di aggiornamento precedente. Quando viene restituito il nuovo token di aggiornamento, MSAL per Python lo memorizzerà nella cache. Ecco un esempio di come farlo:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere il [confronto tra la versione 1.0 e la versione 2.0](active-directory-v2-compare.md).
