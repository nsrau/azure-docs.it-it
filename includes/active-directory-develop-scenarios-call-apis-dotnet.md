---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075146"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Proprietà AuthenticationResult MSAL.NET

I metodi per acquisire i token restituiscono un `AuthenticationResult` (o, per i metodi async, un `Task<AuthenticationResult>`.

In MSAL.NET, `AuthenticationResult` espone:

- `AccessToken` per l'API Web per accedere alle risorse. Questo parametro è una stringa, in genere JWT con codifica base64 ma il client non dovrebbe mai essere all'interno del token di accesso. Il formato non è garantito rimane stabile e possono essere crittografati per la risorsa. Persone la scrittura di codice a seconda del contenuto di token di accesso nel client è una delle principali fonti di errori e interruzioni di client per la logica. Vedere anche [i token di accesso](../articles/active-directory/develop/access-tokens.md)
- `IdToken` per l'utente (questo parametro è un token JWT codificata). Vedere [i token ID](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` indica la data/ora di scadenza del token
- `TenantId` contiene il tenant in cui l'utente è stato trovato. Per gli utenti guest (scenari B2B di Azure AD), l'ID Tenant è il tenant di guest, non il tenant univoco.
Quando il token viene recapitato per un utente, `AuthenticationResult` contiene inoltre informazioni sull'utente. Per i flussi client riservato in cui vengono richiesti i token senza utente (per l'applicazione), queste informazioni utente sono null.
- Il `Scopes` per cui è stato emesso il token.
- Id univoco per l'utente.

### <a name="iaccount"></a>IAccount

MSAL.NET definisce la nozione di Account (tramite il `IAccount` interface). Questa modifica di rilievo fornisce la semantica di destra: il fatto che lo stesso utente può avere più account, in Azure diverse directory di AD. Anche MSAL.NET offre informazioni più complete in caso di scenari di guest, viene fornito informazioni sull'account principale.
Il diagramma seguente illustra la struttura del `IAccount` interfaccia:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Il `AccountId` classe identifica un account in un tenant specifico. Le proprietà sono le seguenti:

| Proprietà | DESCRIZIONE |
|----------|-------------|
| `TenantId` | Rappresentazione di stringa per un GUID, che è l'ID del tenant in cui risiede l'account. |
| `ObjectId` | Rappresentazione di stringa per un GUID, che è l'ID dell'utente che possiede l'account nel tenant. |
| `Identifier` | Identificatore univoco per l'account. `Identifier` è la concatenazione delle `ObjectId` e `TenantId` separati da una virgola e non con codificata base64. |

Il `IAccount` interfaccia rappresenta le informazioni su un singolo account. Lo stesso utente può essere presente in tenant diversi, vale a dire, un utente può avere più account. I relativi membri sono:

| Proprietà | DESCRIZIONE |
|----------|-------------|
| `Username` | Stringa contenente il valore visualizzabile in formato UserPrincipalName (UPN), ad esempio, john.doe@contoso.com. Questa stringa può essere null, mentre il HomeAccountId e HomeAccountId.Identifier sarà null. Questa proprietà sostituisce la `DisplayableId` proprietà di `IUser` nelle versioni precedenti di MSAL.NET. |
| `Environment` | Stringa contenente il provider di identità per tale account, ad esempio, `login.microsoftonline.com`. Questa proprietà sostituisce la `IdentityProvider` proprietà di `IUser`, ad eccezione del fatto che `IdentityProvider` disponeva anche informazioni sul tenant (oltre all'ambiente di cloud), mentre in questo caso il valore è solo l'host. |
| `HomeAccountId` | AccountId dell'account principale per l'utente. Questa proprietà identifica in modo univoco l'utente da un tenant di Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Usando il token per chiamare un'API protetta

Una volta il `AuthenticationResult` è stato restituito da MSAL (in `result`), è necessario aggiungerlo all'intestazione di autorizzazione HTTP, prima di effettuare la chiamata per accedere all'API Web protetta.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```