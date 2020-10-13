---
title: includere file
description: includere file
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
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76308951"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Proprietà di AuthenticationResult in MSAL.NET

I metodi per acquisire i token restituiscono `AuthenticationResult` . Per i metodi asincroni, `Task<AuthenticationResult>` restituisce.

In MSAL.NET `AuthenticationResult` espone:

- `AccessToken` per l'API Web per accedere alle risorse. Questo parametro è una stringa, in genere un JWT con codifica base 64. Il client non deve mai apparire all'interno del token di accesso. Il formato non è garantito che rimanga stabile e possa essere crittografato per la risorsa. La scrittura di codice che dipende dal contenuto del token di accesso nel client è una delle principali fonti di errori e interruzioni della logica del client. Per altre informazioni, vedere [token di accesso](../articles/active-directory/develop/access-tokens.md).
- `IdToken` per l'utente. Questo parametro è un JWT codificato. Per altre informazioni, vedere [token ID](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn` indica la data e l'ora di scadenza del token.
- `TenantId` contiene il tenant in cui è stato trovato l'utente. Per gli utenti Guest negli scenari B2B Azure Active Directory (Azure AD), l'ID tenant è il tenant Guest, non il tenant univoco.
Quando il token viene recapitato per un utente, `AuthenticationResult` contiene anche informazioni su questo utente. Per i flussi client riservati in cui i token vengono richiesti senza utenti per l'applicazione, le informazioni sull'utente sono null.
- Oggetto `Scopes` per il quale è stato emesso il token.
- ID univoco per l'utente.

### <a name="iaccount"></a>IAccount

MSAL.NET definisce la nozione di un account tramite l' `IAccount` interfaccia. Questa modifica di rilievo fornisce la semantica corretta. Lo stesso utente può avere più account, in Azure AD directory diverse. Inoltre, MSAL.NET fornisce informazioni migliori in caso di scenari Guest perché vengono fornite informazioni sull'account Home.
Il diagramma seguente illustra la struttura dell' `IAccount` interfaccia.

![Struttura dell'interfaccia IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

La `AccountId` classe identifica un account in un tenant specifico con le proprietà mostrate nella tabella seguente.

| Proprietà | Descrizione |
|----------|-------------|
| `TenantId` | Rappresentazione in forma di stringa di un GUID, che corrisponde all'ID del tenant in cui risiede l'account. |
| `ObjectId` | Rappresentazione in forma di stringa di un GUID, ovvero l'ID dell'utente proprietario dell'account nel tenant. |
| `Identifier` | Identificatore univoco per l'account. `Identifier` concatenazione di `ObjectId` e `TenantId` separati da una virgola. Non sono codificati in base 64. |

L' `IAccount` interfaccia rappresenta le informazioni su un singolo account. Lo stesso utente può essere presente in tenant diversi, il che significa che un utente può disporre di più account. I relativi membri sono riportati nella tabella seguente.

| Proprietà | Descrizione |
|----------|-------------|
| `Username` | Stringa che contiene il valore visualizzabile nel formato UserPrincipalName (UPN), ad esempio john.doe@contoso.com . Questa stringa può essere null, a differenza di HomeAccountId e HomeAccountId. Identifier, che non sarà null. Questa proprietà sostituisce la `DisplayableId` proprietà di `IUser` nelle versioni precedenti di MSAL.NET. |
| `Environment` | Stringa che contiene il provider di identità per l'account, ad esempio `login.microsoftonline.com` . Questa proprietà sostituisce la `IdentityProvider` proprietà di `IUser` , ad eccezione del fatto che include `IdentityProvider` anche informazioni sul tenant, oltre all'ambiente cloud. In questo caso, il valore è solo l'host. |
| `HomeAccountId` | ID account dell'account Home per l'utente. Questa proprietà identifica in modo univoco l'utente tra Azure AD tenant. |

### <a name="use-the-token-to-call-a-protected-api"></a>Usare il token per chiamare un'API protetta

Quando `AuthenticationResult` viene restituito da MSAL in `result` , aggiungerlo all'intestazione di autorizzazione HTTP prima di effettuare la chiamata per accedere all'API Web protetta.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```