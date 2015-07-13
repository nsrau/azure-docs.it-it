<properties
	pageTitle="Introduzione a .NET per Azure AD | Microsoft Azure"
	description="Come compilare un'app Web .NET MVC che si integra con Azure AD per l'accesso."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="dastrock"/>

# Accedere e disconnettersi dalle app Web con Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD facilita e semplifica l'outsourcing della gestione delle identità delle app Web, fornendo un unico account di accesso e disconnessione solo con poche righe di codice. Nelle app Web Asp.NET, a questo scopo si usa l'implementazione di Microsoft del middleware OWIN gestito dalla community e incluso in .NET Framework 4.5. Qui si userà OWIN per: far accedere l'utente all'app con Azure AD come provider di identità; visualizzare alcune informazioni sull'utente; far disconnettere l'utente dall'app.

A questo scopo è necessario:

1. Registrare un'applicazione con Azure AD.
2. Configurare l'app per l'uso della pipeline di autenticazione OWIN.
3. Usare OWIN per inoltrare le richieste di accesso e disconnessione ad Azure AD.
4. Visualizzare dati relativi all'utente.

Per iniziare, [scaricare la struttura dell'app](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip). Sarà necessario anche un tenant di Azure AD in cui registrare l'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## *1. Registrare un'applicazione con Azure AD*
Per consentire all'app di autenticare gli utenti, sarà innanzitutto necessario registrare una nuova applicazione nel tenant.

- Accedere al portale di gestione di Azure.
- Nel pannello di navigazione a sinistra fare clic su **Active Directory**.
- Selezionare il tenant in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda **Applicazioni**, quindi fare clic su aggiungi nel pannello in basso.
- Seguire le istruzioni e creare una nuova **Applicazione Web e/o API Web**.
    - Il **nome** dell'applicazione deve essere una descrizione per gli utenti finali.
    -	L'**URL accesso** è l'URL di base dell'app. Il valore predefinito della struttura è `https://localhost:44320/`.
    - L'**URI ID app** è un identificatore univoco dell'applicazione. Per convenzione si usa `https://<tenant-domain>/<app-name>`, ad esempio `https://contoso.onmicrosoft.com/my-first-aad-app`.
- Dopo avere completato la registrazione, AAD assegnerà all'app un identificatore client univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla scheda Configura.

## *2. Configurare l'app per l'uso della pipeline di autenticazione OWIN*
In questo caso, verrà configurato il middleware OWIN per l'uso del protocollo di autenticazione OpenID Connect. OWIN verrà usato, tra le altre cose, per inviare le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente.

-	Per iniziare, aggiungere i pacchetti NuGet del middleware OWIN al progetto usando la Console di Gestione pacchetti.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	Aggiungere al progetto una OWIN Startup Class denominata `Startup.cs`. Fare clic con il pulsante destro del mouse sul progetto --> **Aggiungi** --> **Nuovo elemento** --> Cercare "OWIN". Il middleware OWIN richiamerà il metodo `Configuration(...)` all'avvio dell'app.
-	Sostituire la dichiarazione della classe con `public partial class Startup`. Parte di questa classe è già stata implementata in un altro file. Nel metodo `Configuration(...)` effettuare una chiamata a ConfgureAuth(...) per configurare l'autenticazione per l'app Web  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(...)`. I parametri forniti in `OpenIDConnectAuthenticationOptions` fungeranno da coordinate per consentire all'app di comunicare con Azure AD. È inoltre necessario impostare l'autenticazione tramite cookie: il middleware OpenID Connect usa i cookie in background.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-	Infine aprire il file `web.config` nella radice del progetto e immettere i valori di configurazione nella sezione `<appSettings>`.
    -	Il valore `ida:ClientId` dell'app è il Guid copiato dal portale di Azure nel Passaggio 1.
    -	`ida:Tenant` è il nome del tenant di Azure AD, ad esempio, "contoso.onmicrosoft.com".
    -	`ida:PostLogoutRedirectUri` indica ad Azure AD dove reindirizzare un utente dopo il completamento di una richiesta di disconnessione.

## *3. Usare OWIN per inoltrare le richieste di accesso e disconnessione ad Azure AD*
L'app ora è configurata correttamente per comunicare con Azure AD mediante il protocollo di autenticazione OpenID Connect. OWIN ha gestito tutte le difficoltà derivanti dalla creazione dei messaggi di autenticazione, dalla convalida dei token da Azure AD e dalla gestione della sessione utente. Non resta che dare agli utenti un modo per accedere e disconnettersi.

- È possibile usare tag di autorizzazione nei controller per obbligare l'utente ad accedere prima di aprire una determinata pagina. Aprire `Controllers\HomeController.cs` e aggiungere il tag `[Authorize]` al controller About.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	È possibile usare OWIN anche per inviare le richieste di autenticazione direttamente dal codice. Aprire `Controllers\AccountController.cs`. Nelle azioni SignIn() e SignOut() inoltrare rispettivamente le richieste di verifica e di disconnessione di OpenID Connect.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-	Ora aprire `Views\Shared_LoginPartial.cshtml`, dove si mostreranno all'utente i collegamenti di accesso e disconnessione e si visualizzerà il nome dell'utente.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## *4. Visualizzare le informazioni utente*
Quando si autenticano gli utenti con OpenID Connect, Azure AD restituisce un id_token all'applicazione contenente "attestazioni", o asserzioni, sull'utente. È possibile usare queste attestazioni per personalizzare l'app:

- Aprire il file `Controllers\HomeController.cs`. È possibile accedere alle attestazioni dell'utente nei controller tramite l'oggetto di entità di sicurezza `ClaimsPrincipal.Current`.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Infine compilare ed eseguire l'app. Se non si è ancora creato un nuovo utente nel tenant con un dominio *.onmicrosoft.com, ora è possibile farlo. Accedere con tale utente e notare che l'identità dell'utente è visualizzata nella barra di spostamento in alto. Disconnettersi e accedere nuovamente come un altro utente nel tenant. Per vedere Single Sign-On in azione, registrare ed eseguire un'altra istanza di questa applicazione (con il suo clientId).

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) [qui](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).

Ora è possibile passare ad argomenti più avanzati. È possibile:

[Proteggere un'API Web con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

Per altre risorse, vedere: [AzureADSamples su GitHub >>](https://github.com/AzureAdSamples), [CloudIdentity.com >>](https://cloudidentity.com), la documentazione su Azure AD in [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)
 

<!---HONumber=62-->