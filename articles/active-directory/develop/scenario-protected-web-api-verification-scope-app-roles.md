---
title: 'Verificare ambiti e ruoli app: API Web protetta | Azure'
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
ms.openlocfilehash: a4ee2679da5065ab9e9b02d4ddb313fab75e78f7
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845136"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API Web protetta: verificare ambiti e ruoli app

Questo articolo illustra come è possibile aggiungere l'autorizzazione a un'API Web. Questa protezione garantisce che l'API venga chiamata solo da:

- Applicazioni per conto di utenti con gli ambiti corretti.
- App daemon con i ruoli applicazione corretti.

> [!NOTE]
> I frammenti di codice di questo articolo sono estratti dagli esempi seguenti, completamente funzionali:
>
> - [Esercitazione incrementale su API Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) su GitHub
> - [Esempio di API Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Per proteggere un'API Web ASP.NET o ASP.NET Core, è necessario aggiungere l'attributo `[Authorize]` a uno degli elementi seguenti:

- Controller stesso, se si vuole che tutte le azioni del controller siano protette
- Singola azione del controller per l'API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Questa protezione, tuttavia, non è sufficiente. Garantisce solo che ASP.NET e ASP.NET Core convalidino il token. L'API deve verificare che il token usato per chiamare l'API venga richiesto con le attestazioni previste. La verifica è necessaria, in particolare, per queste attestazioni:

- *Ambiti*, se l'API viene chiamata per conto di un utente.
- *Ruoli app*, se l'API può essere chiamata da un'app daemon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verificare gli ambiti nelle API chiamate per conto di utenti

Se un'app client chiama l'API per conto di un utente, l'API deve richiedere un token di connessione con ambiti specifici per l'API. Per altre informazioni, vedere [Configurazione del codice - Token di connessione](scenario-protected-web-api-app-configuration.md#bearer-token).

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

Il metodo `VerifyUserHasAnyAcceptedScope` consente di eseguire questi passaggi:

- Verificare la presenza di un'attestazione denominata `http://schemas.microsoft.com/identity/claims/scope` o `scp`.
- Verificare che l'attestazione includa un valore contenente l'ambito previsto dall'API.

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

Il [codice di esempio](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) precedente è per ASP.NET Core. Per ASP.NET, è sufficiente sostituire `HttpContext.User` con `ClaimsPrincipal.Current` e il tipo di attestazione `"http://schemas.microsoft.com/identity/claims/scope"` con `"scp"`. Vedere anche il frammento di codice più avanti in questo articolo.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verificare i ruoli app nelle API chiamate da app daemon

Se l'API Web viene chiamata da un'[app daemon](scenario-daemon-overview.md), tale app dovrà richiedere un'autorizzazione dell'applicazione all'API Web. Come illustrato in [Esposizione delle autorizzazioni dell'applicazione (ruoli app)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), l'API espone tali autorizzazioni. Un esempio è il ruolo app `access_as_application`.

A questo punto è necessario fare in modo che l'API verifichi che il token ricevuto contenga l'attestazione `roles` e che questa attestazione abbia il valore previsto. Il codice di verifica è simile al codice che verifica le autorizzazioni delegate, tranne per il fatto che l'azione del controller verifica i ruoli invece degli ambiti:

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

Il metodo `ValidateAppRole` può essere simile al seguente:

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

In questo caso, il frammento di codice è per ASP.NET. Per ASP.NET Core, è sufficiente sostituire `ClaimsPrincipal.Current` con `HttpContext.User` e il nome di attestazione `"roles"` con `"http://schemas.microsoft.com/ws/2008/06/identity/claims/role"`. Vedere anche il frammento di codice più indietro in questo articolo.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Accettazione di token solo per app se l'API Web deve essere chiamata solo da app daemon

Gli utenti possono anche usare le attestazioni dei ruoli nei modelli di assegnazione di utenti, come illustrato in [Procedura: Aggiungere ruoli dell'app in un'applicazione e riceverli nel token](howto-add-app-roles-in-azure-ad-apps.md). Se i ruoli sono assegnabili a entrambi, il controllo dei ruoli consentirà alle app di accedere come utenti e agli utenti di accedere come app. Per evitare questa confusione, è consigliabile dichiarare ruoli diversi per gli utenti e le app.

Se si vuole che l'API Web venga chiamata solo da app daemon, aggiungere la condizione che il token sia un token solo per app quando si convalida il ruolo app.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Controllando la condizione inversa, l'API potrà essere chiamata solo dalle app che effettuano l'accesso di un utente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare in produzione](scenario-protected-web-api-production.md)
