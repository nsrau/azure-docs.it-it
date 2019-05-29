---
title: Ambiti per un'applicazione v1.0 (Microsoft Authentication Library) | Azure
description: Informazioni sugli ambiti per un'applicazione v1.0 con Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44aad6b2fab7e0ab2ff11d8469782b001b1f4d18
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545878"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Ambiti per un'API Web che accetta i token v1.0

Le autorizzazioni OAuth2 sono ambiti di autorizzazione che un'applicazione (risorsa) dell'API Web (v1.0) di Azure AD per sviluppatori espone alle applicazioni client. Questi ambiti di autorizzazione possono essere concessi alle applicazioni client durante il consenso. Vedere la sezione relativa a `oauth2Permissions` nelle [informazioni di riferimento sul manifesto dell'applicazione in Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Ambiti per richiedere l'accesso a specifiche autorizzazioni OAuth2 di un'applicazione v1.0
Se si vogliono acquisire i token per gli ambiti specifici di un'applicazione v1.0 (ad esempio, Graph di Azure AD, ovvero https://graph.windows.net)), è necessario creare ambiti concatenando un identificatore di risorsa desiderato con un'autorizzazione OAuth2 desiderata per tale risorsa.

Ad esempio, per accedere per conto dell'utente a un'API Web v1.0 dove l'URI dell'ID app è `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Per eseguire operazioni di lettura e scrittura con Azure Active Directory in MSAL.NET tramite l'API Graph di Azure AD (https://graph.windows.net/)), è possibile creare un elenco di ambiti come mostrato di seguito:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Per eseguire operazioni di scrittura nell'ambito corrispondente all'API di Azure Resource Manager (https://management.core.windows.net/)), è necessario richiedere l'ambito seguente (si notino le due barre):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Le due barre sono necessarie perché l'API di Azure Resource Manager prevede una barra nell'attestazione dei destinatari (aud) e quindi è presente una barra per separare il nome dell'API dall'ambito.

La logica usata da Azure AD è la seguente:

- Per l'endpoint ADAL (v1.0) con un token di accesso v1.0 (l'unico possibile), aud=resource
- Per MSAL (endpoint v2.0 di Microsoft Identity Platform) che richiede un token di accesso per una risorsa che accetta i token v2.0, aud=resource.AppId
- Per MSAL (endpoint v2.0) che richiede un token di accesso per una risorsa che accetta un token di accesso v1.0 (come nel caso precedente), Azure AD analizza i destinatari desiderati dall'ambito richiesto, prendendo tutto ciò che precede l'ultima barra e usandolo come identificatore della risorsa. Di conseguenza, se https://database.windows.net prevede un gruppo di destinatari "https://database.windows.net/", sarà necessario richiedere un ambito "https://database.windows.net//.default". Vedere anche in GitHub il problema [n. 747: se la barra finale dell'URL della risorsa viene omessa, si verifica un errore di autenticazione SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

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

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Ambiti da richiedere per flusso di credenziali client/app daemon
Anche nel caso del flusso di credenziali client, l'ambito da passare è `/.default`. Ciò indica ad Azure AD: tutte le autorizzazioni a livello di app per cui l'amministratore ha concesso il consenso durante la registrazione dell'applicazione.