---
title: Ottenere un token per un'API Web che chiama le API Web. Azure
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
ms.openlocfilehash: 2721837459af24f39bb15ee17d394345cbb37eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834111"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Un'API Web che chiama le API Web: acquisire un token per l'appA web API that calls web APIs: Acquire a token for the app

Dopo aver creato un oggetto applicazione client, usarlo per acquisire un token che è possibile usare per chiamare un'API Web.After you've built a client application object, use it to acquire a token that you can use to call a web API.

## <a name="code-in-the-controller"></a>Codice nel controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ecco un esempio di codice chiamato nelle azioni dei controller API. Chiama un'API downstream denominata *todolist*.

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

`BuildConfidentialClient()`è simile allo scenario in [Un'API Web che chiama le API Web: Configurazione dell'app](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()`crea `IConfidentialClientApplication` un'istanza con una cache che contiene informazioni per un solo account. L'account viene `GetAccountIdentifier` fornito dal metodo.

Il `GetAccountIdentifier` metodo utilizza le attestazioni associate all'identità dell'utente per il quale l'API Web ha ricevuto il token JSON Web (JWT):

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

# <a name="java"></a>[Java](#tab/java)
Ecco un esempio di codice chiamato nelle azioni dei controller API. Chiama l'API downstream - Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Un'API Web Python dovrà usare un middleware per convalidare il token di connessione ricevuto dal client. L'API Web può quindi ottenere il token di accesso per [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) l'API downstream usando la libreria MSAL Python chiamando il metodo . Un esempio che dimostra questo flusso con MSAL Python non è ancora disponibile.

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Un'API Web che chiama le API Web: chiamare un'API](scenario-web-api-call-api-call-api.md)
