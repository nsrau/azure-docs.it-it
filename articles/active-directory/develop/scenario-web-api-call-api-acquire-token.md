---
title: Ottenere un token per un'API Web che chiama le API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web che chiama le API Web che richiedono l'acquisizione di un token per l'app.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cf660cbf981079ca20111e34fcd34504d8dcbfb
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044127"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>API Web che chiama le API Web: acquisire un token per l'app

Dopo aver compilato un oggetto applicazione client, usarlo per acquisire un token che è possibile usare per chiamare un'API Web.

## <a name="code-in-the-controller"></a>Codice nel controller

Di seguito è riportato un esempio di codice chiamato nelle azioni dei controller API. Chiama un'API downstream denominata *todo*.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` è simile allo scenario in [un'API Web che chiama API Web: configurazione dell'app](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` crea un'istanza `IConfidentialClientApplication` con una cache che contiene informazioni per un solo account. L'account viene fornito dal metodo `GetAccountIdentifier`.

Il metodo `GetAccountIdentifier` usa le attestazioni associate all'identità dell'utente per cui l'API Web ha ricevuto il token Web JSON (JWT):

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API Web che chiama API Web: chiamare un'API](scenario-web-api-call-api-call-api.md)
