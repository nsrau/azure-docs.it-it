---
title: "Web API che chiama altri web API (acquisire un token per l'app): piattaforma delle identità Microsoft"
description: Informazioni su come creare una web API che chiama altri web API (acquisire un token per l'app).
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231105"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>API Web che chiama le API - web acquisire un token per l'app

Dopo avere compilato un'applicazione client di oggetto, utilizzarlo per acquisire un token che è possibile usare per chiamare un'API web.

## <a name="code-in-the-controller"></a>Nel controller di codice

Di seguito è riportato un esempio di codice che verrà chiamato nelle azioni del controller dell'API, la chiamata a un'API downstream (denominata todolist).

```CSharp
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

`BuildConfidentialClient()` è simile a ciò che si è visto nell'articolo [API - configurazione delle app web di API Web che chiama](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` Crea un'istanza `IConfidentialClientApplication` con una cache che contiene solo informazioni per un unico account. L'account viene fornito per il `GetAccountIdentifier` (metodo).

Il `GetAccountIdentifier` metodo Usa le attestazioni associate all'identità dell'utente per cui l'API web ha ricevuto il token JWT:

```CSharp
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
> [Chiamare un'API web](scenario-web-api-call-api-call-api.md)
