<properties
   pageTitle="Autorizzazioni nelle applicazioni multi-tenant | Microsoft Azure"
   description="Come eseguire le autorizzazioni in un'applicazione multi-tenant"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Autorizzazioni nelle applicazioni multi-tenant

Questo articolo fa [parte di una serie]. Per questa serie è anche disponibile un'[applicazione di esempio] completa.

In questo articolo vengono esaminati due approcci generali alle autorizzazioni.

-	**Autorizzazione basata sui ruoli**. Autorizzazione di un'azione in base ai ruoli assegnati a un utente, ad esempio alcune azioni richiedono un ruolo di amministratore.
-	**Autorizzazione basata sulle risorse**. Autorizzazione di un'azione in base a una determinata risorsa, ad esempio ogni risorsa ha un proprietario. Il proprietario può eliminare la risorsa, mentre gli altri utenti non dispongono di questa opzione.

In genere, un'app può impiegare una combinazione di entrambi gli approcci. Ad esempio, per eliminare una risorsa l'utente deve essere proprietario _o_ amministratore della risorsa.

## Autorizzazione basata sui ruoli

L'applicazione [Tailspin Surveys][Tailspin] definisce i ruoli seguenti:

- Amministratore: può eseguire tutte le operazioni CRUD su qualsiasi sondaggio appartenente al tenant.
- Autore: può creare nuovi sondaggi.
- Lettore: può leggere qualsiasi sondaggio appartenente al tenant.

I ruoli si applicano agli _utenti_ dell'applicazione. Nell'applicazione Surveys, un utente può essere amministratore, autore o lettore.

Per una discussione su come definire e gestire i ruoli, vedere l'articolo relativo ai [ruoli dell'applicazione].

Il codice di autorizzazione risulterà invariato indipendentemente dalla modalità di gestione dei ruoli. ASP.NET Core 1.0 introduce un'astrazione denominata _Criteri di autorizzazione_. Questa funzionalità consente di definire i criteri di autorizzazione nel codice e quindi applicare i criteri alle azioni del controller. I criteri vengono separati dal controller.

### Creare criteri

Per definire i criteri, creare innanzitutto una classe che implementa `IAuthorizationRequirement`. La procedura più semplice consiste nella derivazione da `AuthorizationHandler`. Nel metodo `Handle` esaminare le attestazioni pertinenti.

Ecco un esempio dell'applicazione Tailspin Surveys:

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Vedere [SurveyCreatorRequirement.cs].

Questa classe definisce i requisiti che consentono a un utente di creare un nuovo sondaggio. L'utente deve avere il ruolo SurveyAdmin o SurveyCreator.

Nella classe di avvio, definire i criteri denominati che includono uno o più requisiti. Se sono presenti più requisiti, l'utente deve soddisfare _ogni_ requisito da autorizzare. Il codice seguente definisce due criteri:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Vedere [Startup.cs].

Questo codice imposta inoltre lo schema di autenticazione, che indica ad ASP.NET quale middleware di autenticazione deve essere eseguito se si verifica un errore di autorizzazione. In questo caso, si specifica il middleware di autenticazione del cookie, in quanto può reindirizzare l'utente a una pagina non consentita. Il percorso della pagina non consentita è impostato nell'opzione AccessDeniedPath per il middleware del cookie. Vedere l'argomento relativo alla [configurazione del middleware di autenticazione].

### Autorizzare le azioni del controller

Infine, per autorizzare un'azione in un controller MVC, impostare i criteri nell'attributo `Authorize`:

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

Nelle versioni precedenti di ASP.NET, è necessario impostare la proprietà **Roles** sull'attributo:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Questa opzione è ancora supportata in ASP.NET Core 1.0, ma presenta alcuni svantaggi rispetto ai criteri di autorizzazione:

-	Si presuppone che sia presente un tipo di attestazione specifico. I criteri possono verificare qualsiasi tipo di attestazione, i ruoli solo un tipo.
-	Il nome del ruolo è hardcoded nell'attributo. Con i criteri, la logica di autorizzazione si trova in un'unica posizione, rendendo più semplice le operazioni di aggiornamento o caricamento dalle impostazioni di configurazione.
-	I criteri consentono di abilitare decisioni di autorizzazione più complesse, ad esempio età >= 21, che non possono essere espresse dalla semplice appartenenza al ruolo.

## Autorizzazione basata sulle risorse

L'_autorizzazione basata sulle risorse_ si verifica ogni volta che l'autorizzazione dipende da una risorsa specifica che verrà influenzata da un'operazione. Nell'applicazione Tailspin Surveys ogni sondaggio ha un proprietario e collaboratori zero-a-molti.

-	Il proprietario può leggere, aggiornare, eliminare, pubblicare e annullare la pubblicazione del sondaggio.
-	Il proprietario può assegnare collaboratori al sondaggio.
-	I collaboratori possono leggere e aggiornare il sondaggio.

Si noti che "proprietario" e "collaboratore" non sono ruoli applicazione e vengono archiviati per ogni sondaggio nel database dell'applicazione. Per verificare se un utente può eliminare un sondaggio, ad esempio, l'app verifica se l'utente è il proprietario di tale sondaggio.

In ASP.NET Core 1.0, implementare l'autorizzazione basata sulle risorse mediante la derivazione da **AuthorizationHandler** ed eseguendo l'override del metodo **Handle**.

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Si noti che questa classe è fortemente tipizzata per gli oggetti Survey. Registrare la classe per l'inserimento delle dipendenze all'avvio:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Per eseguire i controlli di autorizzazione, usare l'interfaccia **IAuthorizationService** che è possibile inserire nei controller. Il codice seguente controlla se un utente può leggere un sondaggio:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Poiché viene passato un oggetto `Survey`, questa chiamata richiamerà `SurveyAuthorizationHandler`.

Nel codice di autorizzazione, un buon approccio consiste nell'aggregazione di tutte le autorizzazioni dell'utente basate sui ruoli e sulle risorse e nella verifica del set di aggregazione in base all'operazione desiderata. Ecco un esempio relativo all'app Surveys. L'applicazione definisce diversi tipi di autorizzazioni:

- Amministratore
- Collaboratore
- Autore
- Proprietario
- Lettore

Inoltre, l'applicazione definisce un set di operazioni possibili sui sondaggi:

- Create
- Read
- Update
- Delete
- Publish
- Unpublish

Il codice seguente crea un elenco di autorizzazioni per un utente e un sondaggio specifici. Si noti che questo codice esamina sia i ruoli dell'app dell'utente sia i campi proprietario o collaboratore del sondaggio.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Vedere [SurveyAuthorizationHandler.cs].

In un'applicazione multi-tenant, è necessario assicurarsi che nelle autorizzazioni non si verifichi una diffusione dei dati degli altri tenant. Nell'app Surveys, l'autorizzazione di collaboratore è consentita tra tenant. È possibile assegnare un utente da un altro tenant come collaboratore. Gli altri tipi di autorizzazione sono limitati alle risorse che appartengono al tenant dell'utente, pertanto il codice controlla l'ID tenant prima di concedere tali tipi di autorizzazione. Il campo `TenantId` viene assegnato alla creazione del sondaggio.

Il passaggio successivo consiste nella verifica dell'operazione (read, update, delete e così via) in base alle autorizzazioni. L'app Surveys implementa questa operazione usando una tabella di ricerca di funzioni:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

## Risorse aggiuntive

- [Autorizzazione basata sulle risorse](https://docs.asp.net/en/latest/security/authorization/resourcebased.html)
- [Autorizzazione personalizzata basata sui criteri](https://docs.asp.net/en/latest/security/authorization/policies.html)

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte di una serie]: guidance-multitenant-identity.md
[ruoli dell'applicazione]: guidance-multitenant-identity-app-roles.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[configurazione del middleware di autenticazione]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->