---
title: Guida alla migrazione da ADAL a MSAL per Python | Azure
description: Informazioni su come eseguire la migrazione dell'app Python ADAL (Azure Active Directory Authentication Library) a Microsoft Authentication Library (MSAL) per Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f4f19b1a9f6ee77fef2ca2634fb346025f41a6d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152749"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Guida alla migrazione da ADAL a MSAL per Python

Questo articolo evidenzia le modifiche che è necessario eseguire per eseguire la migrazione di un'app che usa la libreria di autenticazione Azure Active Directory (ADAL) per usare Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Evidenziazioni differenze

ADAL funziona con l'endpoint Azure Active Directory versione 1.0. Microsoft Authentication Library (MSAL) funziona con la piattaforma di identità Microsoft, nota in precedenza come endpoint Azure Active Directory v 2.0. La piattaforma di identità Microsoft differisce da Azure Active Directory v 1.0 in quanto:

Supporta:
  - Account aziendali e degli istituti di istruzione (di cui Azure AD ha effettuato il provisioning)
  - Account personali (ad esempio, di Outlook.com o Hotmail.com)
  - Clienti che portano il loro indirizzo di posta elettronica o la loro identità di social networking (ad esempio, di LinkedIn, Facebook o Google) tramite l'offerta Azure AD B2C

- È compatibile con gli standard:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

Per ulteriori informazioni, vedere la pagina relativa [all'endpoint della piattaforma Microsoft Identity (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) .

### <a name="scopes-not-resources"></a>Ambiti e non risorse

ADAL Python acquisisce i token per le risorse, ma MSAL Python acquisisce i token per gli ambiti. La superficie dell'API in MSAL Python non dispone più del parametro Resource. È necessario specificare gli ambiti come un elenco di stringhe che dichiarano le autorizzazioni e le risorse desiderate richieste. Per vedere alcuni esempi di ambiti, vedere [ambiti di Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Gestione degli errori

Azure Active Directory Authentication Library (ADAL) per Python usa l'eccezione `AdalError` per indicare che si è verificato un problema. MSAL per Python usa in genere i codici di errore. Per altre informazioni, vedere [MSAL per la gestione degli errori Python](msal-handling-exceptions.md#msal-for-python-error-handling).

### <a name="api-changes"></a>Modifiche all'API

La tabella seguente elenca un'API in ADAL per Python e quella da usare al suo posto in MSAL per Python:

| ADAL per l'API Python  | MSAL per l'API Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication o ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/D  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/d (vedere la sezione precedente) |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) e [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) e [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/D | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/D | Cache con persistenza, disponibile dalle [estensioni MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrare i token di aggiornamento esistenti per MSAL Python

Microsoft Authentication Library (MSAL) astrae il concetto di token di aggiornamento. MSAL Python fornisce una cache dei token in memoria per impostazione predefinita, in modo che non sia necessario archiviare, ricercare o aggiornare i token di aggiornamento. Gli utenti visualizzeranno anche un minor numero di richieste di accesso perché i token di aggiornamento possono in genere essere aggiornati senza l'intervento dell'utente. Per altre informazioni sulla cache dei token, vedere [serializzazione della cache dei token personalizzati in MSAL per Python](msal-python-token-cache-serialization.md).

Il codice seguente consentirà di eseguire la migrazione dei token di aggiornamento gestiti da un'altra libreria OAuth2 (incluso, ma non limitato a ADAL Python), per la gestione da parte di MSAL per Python. Uno dei motivi per cui si esegue la migrazione di questi token di aggiornamento è impedire che gli utenti esistenti debbano accedere di nuovo quando si esegue la migrazione dell'app a MSAL per Python.

Il metodo per la migrazione di un token di aggiornamento consiste nell'usare MSAL per Python per acquisire un nuovo token di accesso usando il token di aggiornamento precedente. Quando viene restituito il nuovo token di aggiornamento, MSAL per Python lo archivia nella cache. Di seguito è riportato un esempio di come eseguire questa operazione:

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
