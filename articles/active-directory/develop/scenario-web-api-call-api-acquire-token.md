---
title: Ottenere un token per un'API Web che chiama le API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web che chiama le API Web che richiedono l'acquisizione di un token per l'app.
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
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885141"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>API Web che chiama le API Web: acquisire un token per l'app

Dopo aver compilato un oggetto applicazione client, usarlo per acquisire un token che è possibile usare per chiamare un'API Web.

## <a name="code-in-the-controller"></a>Codice nel controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

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

`BuildConfidentialClient()`è simile allo scenario in [un'API Web che chiama API Web: configurazione dell'app](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()`Crea un' `IConfidentialClientApplication` istanza di con una cache che contiene informazioni per un solo account. L'account viene fornito dal `GetAccountIdentifier` metodo.

Il `GetAccountIdentifier` metodo usa le attestazioni associate all'identità dell'utente per cui l'API Web ha ricevuto il token Web JSON (JWT):

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
Di seguito è riportato un esempio di codice chiamato nelle azioni dei controller API. Chiama il Microsoft Graph API downstream.

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

Un'API Web Python dovrà usare un middleware per convalidare la bearer token ricevuta dal client. L'API Web può quindi ottenere il token di accesso per l'API downstream usando la libreria MSAL Python [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) chiamando il metodo. Un esempio che illustra questo flusso con MSAL Python non è ancora disponibile.

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API Web che chiama API Web: chiamare un'API](scenario-web-api-call-api-call-api.md)
