---
title: API Web che chiama le API Web - Piattaforma di identità Microsoft Azure
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
ms.openlocfilehash: d66a08d4e84a3771d6c3fa46b96c975869435452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76833380"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Un'API Web che chiama le API Web: chiamare un'API

Dopo aver creato un token, è possibile chiamare un'API Web protetta. Questa operazione viene eseguito dal controller dell'API Web.

## <a name="controller-code"></a>Codice controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Il codice seguente continua il codice di esempio visualizzato in [un'API Web che chiama le API Web: acquisisci un token per l'app.](scenario-web-api-call-api-acquire-token.md) Il codice viene chiamato nelle azioni dei controller API. Chiama un'API downstream denominata *todolist*.

Dopo aver acquisito il token, usarlo come token di connessione per chiamare l'API downstream.

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

# <a name="java"></a>[Java](#tab/java)

Il codice seguente continua il codice di esempio visualizzato in [un'API Web che chiama le API Web: acquisisci un token per l'app.](scenario-web-api-call-api-acquire-token.md) Il codice viene chiamato nelle azioni dei controller API. Chiama l'API downstream MS Graph.

Dopo aver acquisito il token, usarlo come token di connessione per chiamare l'API downstream.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Un esempio che dimostra questo flusso con MSAL Python non è ancora disponibile.

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Un'API Web che chiama le API Web: passare all'ambiente di produzione](scenario-web-api-call-api-production.md)
