---
title: API Web che chiama le API (chiamata API) - piattaforma delle identità di Microsoft web
description: Informazioni su come creare una web API che le chiamate a valle web API (chiama un'API web).
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
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074726"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>API Web che chiama le API - web chiama un'API

Dopo aver ottenuto un token, è possibile chiamare un'API web protetta. Questa operazione viene eseguita dal controller dell'API web ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Codice del controller

Ecco la continuazione del codice di esempio illustrato nella [chiamate all'API web Protected web API: acquisire un token](scenario-web-api-call-api-acquire-token.md), denominato nelle azioni del controller dell'API, chiama un'API downstream (denominata todolist).

Una volta acquisito il token, usarlo come un token di connessione per chiamare l'API downstream.

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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare alla produzione](scenario-web-api-call-api-production.md)
