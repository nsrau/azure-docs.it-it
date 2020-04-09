---
title: Verifica gli ambiti e i ruoli dell'app protetti dall'API Web Azure
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
ms.openlocfilehash: fe029cdec5bca8885a78267c45bace51e7b3c141
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882370"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API Web protetta: verificare gli ambiti e i ruoli dell'appProtected web API: Verify scopes and app roles

In questo articolo viene descritto come aggiungere l'autorizzazione all'API Web. Questa protezione garantisce che l'API venga chiamata solo da:This protection ensures that the API is called only by:

- Applicazioni per conto di utenti con ambiti corretti.
- App Daemon con ruoli di applicazione corretti.

> [!NOTE]
> I frammenti di codice di questo articolo vengono estratti dagli esempi seguenti, che sono completamente funzionali:The code snippets from this article are extracted from the following samples, which are fully functional:
>
> - [esercitazione incrementale dell'API Web di ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) su GitHub
> - [Esempio di API Web di ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Per proteggere un'API Web ASP.NET o `[Authorize]` ASP.NET Core, è necessario aggiungere l'attributo a uno dei seguenti elementi:

- Il controller stesso se si desidera che tutte le azioni del controller siano protette
- L'azione del singolo controller per l'API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ma questa protezione non è sufficiente. Garantisce solo che ASP.NET e ASP.NET Core convalidi il token. L'API deve verificare che il token utilizzato per chiamare l'API sia richiesto con le attestazioni previste. Queste affermazioni, in particolare, necessitano di verifica:

- *Ambiti* se l'API viene chiamata per conto di un utente.
- *L'app riruoloi* se l'API può essere chiamata da un'app daemon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verificare gli ambiti nelle API chiamate per conto degli utentiVerify scopes in APIs called on behalf of users

Se un'app client chiama l'API per conto di un utente, l'API deve richiedere un token di connessione con ambiti specifici per l'API. Per ulteriori informazioni, vedere [Configurazione del codice. Gettone bearer](scenario-protected-web-api-app-configuration.md#bearer-token).

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

Il `VerifyUserHasAnyAcceptedScope` metodo esegue una procedura simile ai seguenti:

- Verificare che sia `http://schemas.microsoft.com/identity/claims/scope` presente `scp`un'attestazione denominata o .
- Verificare che l'attestazione abbia un valore che contiene l'ambito previsto dall'API.

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

Il [codice di esempio](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) precedente è per ASP.NET Core.The preceding sample code is for ASP.NET Core. Per ASP.NET, `HttpContext.User` è `ClaimsPrincipal.Current`sufficiente sostituire con `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"`e sostituire il tipo di attestazione con . Vedere anche il frammento di codice più avanti in questo articolo.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verificare i ruoli delle app nelle API chiamate dalle app daemonVerify app roles in APIs called by daemon apps

Se l'API Web viene chiamata da [un'app daemon,](scenario-daemon-overview.md)tale app deve richiedere un'autorizzazione dell'applicazione per l'API Web. Come illustrato in [Esposizione delle autorizzazioni dell'applicazione (ruoli dell'app), l'API](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)espone tali autorizzazioni. Un esempio `access_as_application` è il ruolo dell'app.

È ora necessario che l'API verifichi che `roles` il token ricevuto contenga l'attestazione e che questa attestazione abbia il valore previsto. Il codice di verifica è simile al codice che verifica le autorizzazioni delegate, ad eccezione del fatto che l'azione del controller verifica i ruoli anziché gli ambiti:The verification code is similar to the code that verifies delegated permissions, except that your controller action tests for roles instead of scopes:

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

Il `ValidateAppRole` metodo può essere simile al seguente:The method can be something like this:

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

Questa volta, il frammento di codice è per ASP.NET. Per ASP.NET Core, `ClaimsPrincipal.Current` `HttpContext.User`è sufficiente `"roles"` sostituire con `"http://schemas.microsoft.com/identity/claims/roles"`e , quindi sostituire il nome dell'attestazione con . Vedere anche il frammento di codice più indietro in questo articolo.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Accettazione di token solo app se l'API Web deve essere chiamata solo dalle app daemon

Gli utenti possono anche usare le attestazioni di ruolo nei modelli di assegnazione utente, come illustrato in [Procedura: aggiungere ruoli dell'app nell'applicazione e riceverli nel token.](howto-add-app-roles-in-azure-ad-apps.md) Se i ruoli sono assegnabili a entrambi, il controllo dei ruoli consentirà alle app di accedere come utenti e utenti per accedere come app. Ti consigliamo di dichiarare ruoli diversi per utenti e app per evitare questa confusione.

Se vuoi che solo le app daemon chiamino l'API Web, aggiungi la condizione che il token sia un token solo per le app quando convalidi il ruolo dell'app.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Il controllo della condizione inversa consente solo alle app che accedono a un utente di chiamare l'API.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare in produzione](scenario-protected-web-api-production.md)
