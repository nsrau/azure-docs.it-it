---
title: API web - configurazione del codice app protetta | Azure Active Directory
description: Informazioni su come compilare un'API Web protetta e configurare il codice dell'applicazione.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: b700825be9a7fe23fe4b50a2d69d4de71f7dc038
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116454"
---
# <a name="protected-web-api---adding-authorization-to-your-api"></a>API - aggiunta di autorizzazioni all'API web protetta

Questo articolo descrive come aggiungere autorizzazioni all'API Web. Questa protezione garantisce che viene chiamato solo:

- applicazioni per conto degli utenti con gli ambiti corretti 
- o dalle App daemon con i ruoli dell'applicazione corretti.

Per un ASP.NET o ASP.NET Core Web API da proteggere, è necessario aggiungere il `[Authorize]` attributo:

- il Controller stesso se si desidera che tutte le azioni del controller da proteggere
- o l'azione del controller singoli per l'API.

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ma questo tipo di protezione non è sufficiente. È garantito solo dal che ASP.NET o ASP.NET Core verrà convalidato il token. L'API deve verificare che il token utilizzato per chiamare l'API Web è stata richiesta con le attestazioni è previsto che, in particolare:

- il **ambiti** se viene chiamata l'API per conto di un utente
- il **ruoli app** se l'API può essere chiamato da un'app daemon.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Verifica per determinare se gli ambiti per le API chiamata per conto degli utenti

Se l'API viene chiamata da un'app client per conto di un utente, quindi è necessario richiedere un token di connessione con gli ambiti specifici per l'API (vedere [configurazione del codice | Token di connessione](scenario-protected-web-api-app-configuration.md#bearer-token))

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The Web API will only accept tokens 1) for users, 2) having the `access_as_user` scope for
    /// this API
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result
        ...
    }
...
}
```

Il `VerifyUserHasAnyAcceptedScope` metodo fare qualcosa di simile al seguente:

- Verificare che sia presente un attestazioni denominate `http://schemas.microsoft.com/identity/claims/scope` o `scp`
- Verificare che l'attestazione ha un valore che contiene l'ambito previsto dall'API.

```CSharp
    /// <summary>
    /// When applied to an <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// Web API has any of the accepted scopes.
    /// If the authenticated user does not have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized with the message telling which scopes are expected in the token
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

Questo codice di esempio viene per ASP.NET Core. Per sostituire semplicemente ASP.NET `HttpContext.User` dal `ClaimsPrincipal.Current`e il tipo di attestazione `"http://schemas.microsoft.com/identity/claims/scope"` da `"scp"` (vedere anche il frammento di codice riportato di seguito)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Verifica per determinare se i ruoli dell'app per le API chiamata dalle App daemon

Se viene chiamata l'API Web da un [applicazione Daemon](scenario-daemon-overview.md), quindi l'applicazione deve richiedere un'autorizzazione dell'applicazione all'API Web. Abbiamo visto in [scenario-protected-web-api-app-registration.md#how-to-expose-application-permissions--app-roles-] che l'API espone tali autorizzazioni (ad esempio come il `access_as_application` ruolo dell'app).
È ora necessario avere le API di verificare che contenga il token ricevuto il `roles` attestazioni e che l'attestazione con il valore prevede di ricevere. Il codice esegue questa verifica è simile al codice che verifica le autorizzazioni delegate, tranne il fatto che, anziché eseguire il test per `scopes`, l'azione del controller eseguirà il test per `roles`:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

Il `ValidateAppRole()` metodo può essere simile al seguente:

```CSharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case we look for a `role` value of `access_as_application`
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

Questo codice di esempio viene per ASP.NET. Per ASP.NET Core, è sufficiente sostituire `ClaimsPrincipal.Current` dal `HttpContext.User` e il `"roles"` attestazione nome base `"http://schemas.microsoft.com/identity/claims/roles"` (vedere anche il frammento di codice precedente)

### <a name="accepting-app-only-tokens-if-the-web-api-should-only-be-called-by-daemon-apps"></a>Accettare i token dell'app solo se l'API Web deve essere chiamata solo dalle App daemon

Il `roles` attestazione viene usata anche per gli utenti nei modelli di assegnazione di utenti (vedere [come: Aggiungere i ruoli dell'app nell'applicazione e che vengano ricevuti nel token](howto-add-app-roles-in-azure-ad-apps.md)). Quindi è sufficiente controllare ruoli consentirà alle app di accedere come utenti e viceversa, se i ruoli sono assegnabili a entrambi. È consigliabile avere diversi ruoli dichiarati per gli utenti e App per evitare questa confusione.

Se si desidera solo consentire alle applicazioni del daemon di chiamare l'API Web, è necessario aggiungere una condizione, quando si convalida il ruolo dell'app, che il token è un token solo app:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

La verifica della condizione inversa consentirà solo le app che un utente, l'accesso per chiamare l'API.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare alla produzione](scenario-protected-web-api-production.md)
