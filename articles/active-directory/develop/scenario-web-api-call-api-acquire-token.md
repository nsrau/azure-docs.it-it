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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 56bcc4e4936371b58d78f6de5ce4c2d25fbf614d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442804"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>API Web che chiama le API Web: acquisire un token per l'app

Dopo aver compilato un oggetto applicazione client, usarlo per acquisire un token che è possibile usare per chiamare un'API Web.

## <a name="code-in-the-controller"></a>Codice nel controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity. Web* aggiunge metodi di estensione che forniscono servizi pratici per chiamare Microsoft Graph o un'API Web downstream. Questi metodi sono descritti in dettaglio in [un'API Web che chiama API Web: chiamare un'API](scenario-web-api-call-api-call-api.md). Con questi metodi helper non è necessario acquisire manualmente un token.

Se tuttavia si desidera acquisire manualmente un token, il codice seguente illustra un esempio di utilizzo di *Microsoft. Identity. Web* a tale scopo in un controller API. Chiama un'API downstream denominata *todo*.
Per ottenere un token per chiamare l'API downstream, inserire il `ITokenAcquisition` servizio tramite l'inserimento di dipendenze nel costruttore del controller (o nel costruttore della pagina se si usa Blazer) e usarlo nelle azioni del controller, ottenendo un token per l'utente ( `GetAccessTokenForUserAsync` ) o per l'applicazione stessa ( `GetAccessTokenForAppAsync` ) nel caso di uno scenario daemon.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Per informazioni dettagliate sul `callTodoListService` metodo, vedere  [un'API Web che chiama API Web: chiamare un'API](scenario-web-api-call-api-call-api.md).

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

Un'API Web Python richiede l'uso del middleware per convalidare la bearer token ricevuta dal client. L'API Web può quindi ottenere il token di accesso per un'API downstream usando la libreria MSAL Python chiamando il [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metodo. Un esempio che illustra questo flusso con MSAL Python non è ancora disponibile.

---

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, [chiamare un'API](scenario-web-api-call-api-call-api.md).
