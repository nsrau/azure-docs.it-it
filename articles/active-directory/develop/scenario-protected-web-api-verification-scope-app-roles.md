---
title: Verificare gli ambiti e l'API Web protetta dei ruoli applicazione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web protetta e configurare il codice dell'applicazione.
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
ms.openlocfilehash: 018e7f9bc389e3d148ff6860dae9fef88991e5c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537169"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API Web protetta: verifica degli ambiti e dei ruoli dell'app

Questo articolo descrive come è possibile aggiungere l'autorizzazione all'API Web. Questa protezione garantisce che l'API venga chiamata solo da:

- Applicazioni per conto di utenti con gli ambiti corretti.
- App daemon con i ruoli applicazione appropriati.

> [!NOTE]
> I frammenti di codice di questo articolo sono estratti dagli esempi seguenti, che sono completamente funzionanti:
>
> - [Esercitazione incrementale sull'API web ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) su GitHub
> - [Esempio di API Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Per proteggere un'API Web ASP.NET o ASP.NET Core, è necessario aggiungere l' `[Authorize]` attributo a uno degli elementi seguenti:

- Il controller stesso se si desidera che tutte le azioni del controller siano protette
- Azione del singolo controller per l'API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ma questa protezione non è sufficiente. Garantisce solo che ASP.NET e ASP.NET Core convalidano il token. L'API deve verificare che il token usato per chiamare l'API sia richiesto con le attestazioni previste. Queste attestazioni in particolare richiedono la verifica:

- *Ambiti* se l'API viene chiamata per conto di un utente.
- I *ruoli dell'app* se l'API può essere chiamata da un'app daemon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verificare gli ambiti nelle API chiamate per conto degli utenti

Se un'app client chiama l'API per conto di un utente, l'API deve richiedere un bearer token con ambiti specifici per l'API. Per altre informazioni, vedere [configurazione del codice | Token di porta](scenario-protected-web-api-app-configuration.md#bearer-token).

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

Il `VerifyUserHasAnyAcceptedScope` metodo esegue una procedura analoga alla seguente:

- Verificare la presenza di un' `http://schemas.microsoft.com/identity/claims/scope` attestazione `scp`denominata o.
- Verificare che il valore dell'attestazione includa l'ambito previsto dall'API.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Il [codice di esempio](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) precedente è per ASP.NET Core. Per ASP.NET, è sufficiente `HttpContext.User` sostituire `ClaimsPrincipal.Current`con e sostituire il tipo `"http://schemas.microsoft.com/identity/claims/scope"` di attestazione con. `"scp"` Vedere anche il frammento di codice più avanti in questo articolo.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verificare i ruoli app nelle API chiamate dalle app daemon

Se l'API Web viene chiamata da un' [app daemon](scenario-daemon-overview.md), l'app deve richiedere l'autorizzazione dell'applicazione per l'API Web. Come illustrato in [esposizione delle autorizzazioni dell'applicazione (ruoli app)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), l'API espone tali autorizzazioni. Un esempio è il `access_as_application` ruolo app.

A questo punto è necessario fare in modo che l'API verifichi che il token `roles` ricevuto contiene l'attestazione e che questa attestazione abbia il valore previsto. Il codice di verifica è simile al codice che verifica le autorizzazioni delegate, ad eccezione del fatto che l'azione del controller verifica i ruoli invece degli ambiti:

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

Il `ValidateAppRole` metodo può essere simile al seguente:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

Questa volta, il frammento di codice è per ASP.NET. Per ASP.NET Core, è sufficiente `ClaimsPrincipal.Current` sostituire `HttpContext.User`con e sostituire il `"roles"` nome dell'attestazione con `"http://schemas.microsoft.com/identity/claims/roles"`. Vedere anche il frammento di codice riportato in precedenza in questo articolo.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Accettazione di token solo app se l'API Web deve essere chiamata solo da app daemon

Gli utenti possono anche usare le attestazioni dei ruoli nei modelli di assegnazione utente, come illustrato in [procedura: aggiungere i ruoli dell'app nell'applicazione e riceverli nel token](howto-add-app-roles-in-azure-ad-apps.md). Se i ruoli sono assegnabili a entrambi, il controllo dei ruoli consente alle app di accedere come utenti e utenti per accedere come app. Per evitare questa confusione, è consigliabile dichiarare diversi ruoli per gli utenti e le app.

Se si vuole che solo le app daemon chiamino l'API Web, aggiungere la condizione in cui il token è un token solo app quando si convalida il ruolo dell'app.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Il controllo della condizione inversa consente solo alle app che effettuano l'accesso a un utente di chiamare l'API.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare in produzione](scenario-protected-web-api-production.md)
