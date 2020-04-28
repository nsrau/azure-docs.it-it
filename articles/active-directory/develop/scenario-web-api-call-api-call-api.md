---
title: API Web che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'API Web che chiama le API Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885090"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>API Web che chiama API Web: chiamare un'API

Una volta che si dispone di un token, è possibile chiamare un'API Web protetta. Questa operazione viene eseguita dal controller dell'API Web.

## <a name="controller-code"></a>Codice controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

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

# <a name="java"></a>[Java](#tab/java)

Il codice seguente continua il codice di esempio illustrato in [un'API Web che chiama API Web: acquisire un token per l'app](scenario-web-api-call-api-acquire-token.md). Il codice viene chiamato nelle azioni dei controller API. Chiama l'API downstream MS Graph.

Dopo aver acquisito il token, usarlo come bearer token per chiamare l'API downstream.

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
Un esempio che illustra questo flusso con MSAL Python non è ancora disponibile.

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Un'API Web che chiama le API Web: passa all'ambiente di produzione](scenario-web-api-call-api-production.md)
