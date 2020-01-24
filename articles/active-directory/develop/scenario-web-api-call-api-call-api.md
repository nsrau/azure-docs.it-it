---
title: API Web che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'API Web che chiama le API Web.
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
ms.openlocfilehash: c7c54c7c4718cea479a812574e961ef2338a6be6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701757"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>API Web che chiama API Web: chiamare un'API

Una volta che si dispone di un token, è possibile chiamare un'API Web protetta. Questa operazione viene eseguita dal controller dell'API Web ASP.NET o ASP.NET Core.

## <a name="controller-code"></a>Codice controller

Il codice seguente continua il codice di esempio illustrato in [un'API Web che chiama API Web: acquisire un token per l'app](scenario-web-api-call-api-acquire-token.md). Il codice viene chiamato nelle azioni dei controller API. Chiama un'API downstream denominata *todo*.

Dopo aver acquisito il token, usarlo come bearer token per chiamare l'API downstream.

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Un'API Web che chiama le API Web: passa all'ambiente di produzione](scenario-web-api-call-api-production.md)
