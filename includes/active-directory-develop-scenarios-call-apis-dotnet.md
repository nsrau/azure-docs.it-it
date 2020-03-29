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
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76308951"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Proprietà AuthenticationResult in MSAL.NET

I metodi per acquisire `AuthenticationResult`token restituiscono . Per i `Task<AuthenticationResult>` metodi asincroni, restituisce.

In MSAL.NET, `AuthenticationResult` espone:

- `AccessToken`per l'API web per accedere alle risorse. Questo parametro è una stringa, in genere un JWT con codifica Base 64.This parameter is a string, usually a Base-64-encoded JWT. Il client non deve mai cercare all'interno del token di accesso. Il formato non è garantito per rimanere stabile e può essere crittografato per la risorsa. La scrittura di codice che dipende dal contenuto del token di accesso sul client è una delle principali origini di errori e interruzioni di logica del client. Per ulteriori informazioni, vedere Token di [accesso](../articles/active-directory/develop/access-tokens.md).
- `IdToken`per l'utente. Questo parametro è un JWT codificato. Per ulteriori informazioni, vedere [Token ID](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn`indica la data e l'ora di scadenza del token.
- `TenantId`contiene il tenant in cui è stato trovato l'utente. Per gli utenti guest negli scenari B2B di Azure Active Directory (Azure AD), l'ID tenant è il tenant guest, non il tenant univoco.
Quando il token viene recapitato per un utente, `AuthenticationResult` contiene anche informazioni su questo utente. Per i flussi client riservati in cui i token vengono richiesti senza alcun utente per l'applicazione, queste informazioni utente sono null.
- Il `Scopes` per cui è stato emesso il token.
- ID univoco per l'utente.

### <a name="iaccount"></a>Account I

MSAL.NET definisce la nozione `IAccount` di account tramite l'interfaccia. Questa modifica di rilievo fornisce la semantica corretta. Lo stesso utente può avere più account, in diverse directory di Azure AD. Inoltre, MSAL.NET fornisce informazioni migliori nel caso di scenari guest perché vengono fornite informazioni sull'account home.
Il diagramma seguente mostra `IAccount` la struttura dell'interfaccia.

![Struttura dell'interfaccia IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

La `AccountId` classe identifica un account in un tenant specifico con le proprietà illustrate nella tabella seguente.

| Proprietà | Descrizione |
|----------|-------------|
| `TenantId` | Rappresentazione di stringa per un GUID, ovvero l'ID del tenant in cui risiede l'account. |
| `ObjectId` | Rappresentazione di stringa per un GUID, ovvero l'ID dell'utente proprietario dell'account nel tenant. |
| `Identifier` | Identificatore univoco dell'account. `Identifier`è la concatenazione di `ObjectId` e `TenantId` separata da una virgola. Non sono codificati in Base 64. |

L'interfaccia `IAccount` rappresenta informazioni su un singolo account. Lo stesso utente può essere presente in tenant diversi, il che significa che un utente può avere più account. I relativi membri sono riportati nella tabella seguente.

| Proprietà | Descrizione |
|----------|-------------|
| `Username` | Stringa contenente il valore visualizzabile in formato UserPrincipalName (UPN), john.doe@contoso.comad esempio . Questa stringa può essere null, a differenza di HomeAccountId e HomeAccountId.Identifier, che non sarà null. Questa proprietà sostituisce `DisplayableId` `IUser` la proprietà delle versioni precedenti di MSAL.NET. |
| `Environment` | Stringa contenente il provider di identità per `login.microsoftonline.com`questo account, ad esempio . Questa proprietà sostituisce `IdentityProvider` `IUser`la proprietà `IdentityProvider` di , ad eccezione del fatto che disponeva anche di informazioni sul tenant, oltre che sull'ambiente cloud. In questo caso, il valore è solo l'host. |
| `HomeAccountId` | ID dell'account home per l'utente. Questa proprietà identifica in modo univoco l'utente tra tenant di Azure AD. |

### <a name="use-the-token-to-call-a-protected-api"></a>Usare il token per chiamare un'API protettaUse the token to call a protected API

Dopo `AuthenticationResult` che viene restituito `result`da MSAL in , aggiungerlo all'intestazione di autorizzazione HTTP prima di effettuare la chiamata per accedere all'API Web protetta.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```