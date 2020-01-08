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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423743"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Proprietà di AuthenticationResult in MSAL.NET

I metodi per acquisire i token restituiscono un `AuthenticationResult` (o, per i metodi asincroni, una `Task<AuthenticationResult>`.

In MSAL.NET `AuthenticationResult` espone:

- `AccessToken` per l'API Web per accedere alle risorse. Questo parametro è una stringa, in genere un JWT con codifica Base64, ma il client non deve mai apparire all'interno del token di accesso. Non è garantito che il formato rimanga stabile e può essere crittografato per la risorsa. La scrittura di codice dipendente dal contenuto del token di accesso nel client è una delle principali fonti di errori e interruzioni per la logica client. Vedere anche [token di accesso](../articles/active-directory/develop/access-tokens.md)
- `IdToken` per l'utente (questo parametro è un JWT codificato). Vedere [token ID](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` indica la data e l'ora di scadenza del token
- `TenantId` contiene il tenant in cui è stato trovato l'utente. Per gli utenti Guest (Azure AD scenari B2B), l'ID tenant è il tenant Guest, non il tenant univoco.
Quando il token viene recapitato per un utente, `AuthenticationResult` contiene anche informazioni su questo utente. Per i flussi client riservati in cui i token vengono richiesti senza utente (per l'applicazione), le informazioni sull'utente sono null.
- `Scopes` per cui è stato emesso il token.
- ID univoco per l'utente.

### <a name="iaccount"></a>IAccount

MSAL.NET definisce la nozione di account (tramite l'interfaccia `IAccount`). Questa importante modifica fornisce la semantica appropriata: il fatto che lo stesso utente può avere più account, in diverse directory di Azure AD. Inoltre, MSAL.NET fornisce informazioni migliori in caso di scenari Guest, perché vengono fornite informazioni sull'account Home.
Il diagramma seguente illustra la struttura dell'interfaccia `IAccount`:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

La classe `AccountId` identifica un account in un tenant specifico. Le proprietà sono le seguenti:

| Proprietà | Description |
|----------|-------------|
| `TenantId` | Rappresentazione in forma di stringa di un GUID, che corrisponde all'ID del tenant in cui risiede l'account. |
| `ObjectId` | Rappresentazione in forma di stringa di un GUID, ovvero l'ID dell'utente proprietario dell'account nel tenant. |
| `Identifier` | Identificatore univoco per l'account. `Identifier` è la concatenazione di `ObjectId` e `TenantId` separati da una virgola e non sono codificati in base 64. |

L'interfaccia `IAccount` rappresenta le informazioni su un singolo account. Lo stesso utente può essere presente in tenant diversi, ovvero un utente può disporre di più account. I relativi membri sono:

| Proprietà | Description |
|----------|-------------|
| `Username` | Stringa che contiene il valore visualizzabile nel formato UserPrincipalName (UPN), ad esempio john.doe@contoso.com. Questa stringa può essere null, mentre HomeAccountId e HomeAccountId. Identifier non saranno null. Questa proprietà sostituisce la proprietà `DisplayableId` di `IUser` nelle versioni precedenti di MSAL.NET. |
| `Environment` | Stringa contenente il provider di identità per l'account, ad esempio `login.microsoftonline.com`. Questa proprietà sostituisce la proprietà `IdentityProvider` di `IUser`, ad eccezione del fatto che `IdentityProvider` ha anche informazioni sul tenant (oltre all'ambiente cloud), mentre in questo caso il valore è solo l'host. |
| `HomeAccountId` | AccountId dell'account Home per l'utente. Questa proprietà identifica in modo univoco l'utente tra Azure AD tenant. |

### <a name="using-the-token-to-call-a-protected-api"></a>Uso del token per chiamare un'API protetta

Una volta che il `AuthenticationResult` è stato restituito da MSAL (in `result`), è necessario aggiungerlo all'intestazione di autorizzazione HTTP, prima di effettuare la chiamata per accedere all'API Web protetta.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```