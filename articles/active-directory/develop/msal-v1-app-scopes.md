---
title: Ambiti per le app v1.0 (MSAL) Azure
description: Informazioni sugli ambiti per un'applicazione v1.0 con Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 61d07c1ba912a0e24b2f4e5fa67243b4525db367
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536183"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Ambiti per un'API Web che accetta token v1.0Scopes for a web API accepting v1.0 tokens

Le autorizzazioni OAuth2 sono ambiti di autorizzazione che un'applicazione API Web (risorsa) di Azure Active Directory (Azure AD) per sviluppatori (v1.0) espone alle applicazioni client. Questi ambiti di autorizzazione possono essere concessi alle applicazioni client durante il consenso. Vedere la sezione relativa a `oauth2Permissions` nelle [informazioni di riferimento sul manifesto dell'applicazione in Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Ambiti per richiedere l'accesso a specifiche autorizzazioni OAuth2 di un'applicazione v1.0

Per acquisire token per ambiti specifici di un'applicazione v1.0 (ad https://graph.microsoft.com)esempio l'API Microsoft Graph, ovvero, creare ambiti concatenando un identificatore di risorsa desiderato con un'autorizzazione OAuth2 desiderata per tale risorsa.

Ad esempio, per accedere per conto dell'utente a un'API Web v1.0 dove l'URI dell'ID app è `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Per leggere e scrivere con MSAL.NET Azure AD\/usando l'API Microsoft Graph (https: /graph.microsoft.com/), è necessario creare un elenco di ambiti come illustrato negli esempi seguenti:To read and write with Azure AD using the Microsoft Graph API (https: /graph.microsoft.com/), you need to create a list of scopes as shown in the following examples:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Per scrivere l'ambito corrispondente all'API\/di Azure Resource Manager (https: /management.core.windows.net/), è necessario richiedere l'ambito seguente (notare le due barre):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Le due barre sono necessarie perché l'API di Azure Resource Manager prevede una barra nell'attestazione dei destinatari (aud) e quindi è presente una barra per separare il nome dell'API dall'ambito.

La logica usata da Azure AD è la seguente:

- Per l'endpoint ADAL (Azure AD v1.0) con un token di accesso v1.0 (l'unico possibile), aud
- Per l'endpoint MSAL (Microsoft identity platform (v2.0)) che richiede un token di accesso per una risorsa che accetta token v2.0,`aud=resource.AppId`
- Per MSAL (endpoint v2.0) che richiede un token di accesso per una risorsa che accetta un token di accesso v1.0 (come in precedenza), Azure AD analizza il gruppo di destinatari desiderato dall'ambito richiesto prendendo tutto prima dell'ultima barra e usandolo come identificatore di risorsa. Pertanto, se\/https: /database.windows.net prevede un\/pubblico di "https: /database.windows.net/", è\/necessario richiedere un ambito di "https: /database.windows.net//.default". Vedere anche problema GitHub [#747: barra finale dell'URL della risorsa viene omessa, che ha causato](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)un errore di autenticazione sql .

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Ambiti per richiedere l'accesso a tutte le autorizzazioni di un'applicazione v1.0

Per acquisire un token per tutti gli ambiti statici di un'applicazione v1.0, accodare ".default" all'URI dell'ID app dell'API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Ambiti per richiedere un'app flusso/daemon delle credenziali clientScopes to request for a client credential flow/daemon app

Anche nel caso del flusso di credenziali client, l'ambito da passare è `/.default`. Ciò indica ad Azure AD: tutte le autorizzazioni a livello di app per cui l'amministratore ha concesso il consenso durante la registrazione dell'applicazione.
