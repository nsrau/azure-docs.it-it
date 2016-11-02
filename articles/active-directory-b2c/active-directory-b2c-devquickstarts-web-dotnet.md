<properties
	pageTitle="Azure Active Directory B2C | Microsoft Azure"
	description="Come compilare un'appllicazione Web con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente usando Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="dastrock"/>

# Azure AD B2C: compilare un'app Web .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C permette di aggiungere funzionalità efficaci di gestione delle identità self-service all'app Web con pochi brevi passaggi. Questo articolo descrive come creare un'app Web MVC (Model-View-Controller) .NET con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente. L'app includerà il supporto per l'iscrizione e l'accesso tramite un nome utente o un indirizzo di posta elettronica e usando account di social media quali Facebook e Google.

## Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non ne è già disponibile una, prima di continuare con questa guida [creare una directory B2C](active-directory-b2c-get-started.md).

## Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un'**app Web o un'API Web** nell'applicazione.
- Immettere `https://localhost:44316/` come **URI di reindirizzamento**. Si tratta dell'URL predefinito per questo esempio di codice.
- Copiare l'**ID applicazione** assegnato all'app, Sarà necessario più avanti.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md) specifici. Questo esempio di codice contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. È necessario creare un criterio per ogni tipo, come descritto nell'[articolo di riferimento sui criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).

>[AZURE.NOTE] Azure AD B2C supporta anche criteri di registrazione o accesso combinati non illustrati in questa esercitazione. I criteri di iscrizione o accesso sono illustrati in [questa esercitazione equivalente](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere **Iscrizione ID utente** o **Iscrizione posta elettronica** nel pannello dei provider di identità.
- Scegliere **Nome visualizzato** e altri attributi nei criteri di iscrizione.
- Scegliere l'attestazione **Nome visualizzato** come attestazione dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **Nome** di ogni criterio dopo averlo creato. I nomi dei criteri saranno necessari in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

## Scaricare il codice e configurare l'autenticazione

Il codice per questo esempio è [disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Per compilare l'esempio passo dopo passo, è possibile [scaricare il progetto bozza come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

L'esempio completato è [disponibile anche come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare.

L'app comunica con Azure AD B2C inviando messaggi di autenticazione che specificano i criteri che devono essere eseguiti come parte della richiesta HTTP. Per le applicazioni Web .NET, è possibile usare la libreria OWIN di Microsoft per inviare richieste di autenticazione OpenID Connect, eseguire criteri, gestire le sessioni utente e così via.

Per iniziare, aggiungere i pacchetti NuGet del middleware OWIN al progetto usando la Console di Gestione pacchetti di Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Aprire quindi il file `web.config` nella radice del progetto e immettere i valori di configurazione dell'app nella sezione `<appSettings>`, sostituendo i valori esistenti.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Aggiungere quindi al progetto una classe di avvio OWIN denominata `Startup.cs`. Fare clic con il pulsante destro del mouse sul progetto, selezionare **Aggiungi**, **Nuovo elemento** e quindi cercare "OWIN". **Assicurarsi di modificare la dichiarazione di classe in `public partial class Startup`**. Parte di questa classe è stata implementata in un altro file. Il middleware OWIN richiamerà il metodo `Configuration(...)` all'avvio dell'app. In questo metodo effettuare una chiamata a `ConfigureAuth(...)`, in cui viene configurata l'autenticazione per l'app.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(...)`. I parametri forniti in `OpenIdConnectAuthenticationOptions` fungono da coordinate per consentire all'app di comunicare con Azure AD. È necessario configurare anche l'autenticazione tramite cookie. Il middleware OpenID Connect usa i cookie per gestire, tra l'altro, le sessioni utente.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
```

## Inviare richieste di autenticazione ad Azure AD
L'app è ora configurata correttamente per comunicare con Azure AD B2C usando il protocollo di autenticazione OpenID Connect. OWIN ha gestito tutti dettagli relativi alla creazione dei messaggi di autenticazione, alla convalida dei token da Azure AD e alla gestione della sessione utente. A questo punto, non resta che avviare ogni flusso utente.

Quando un utente seleziona **Iscrizione**, **Accesso** o **Modifica profilo** nell'app Web, l'azione associata viene richiamata in `Controllers\AccountController.cs`. In ogni caso, è possibile usare i metodi OWIN predefiniti per attivare i criteri corretti:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

È anche possibile usare un tag `[Authorize]` nei controller per richiedere l'esecuzione di determinati criteri se l'utente non ha eseguito l'accesso. Aprire `Controllers\HomeController.cs` e aggiungere il tag `[Authorize]` al controller delle attestazioni. Quando viene richiamato il tag Authorize, in OWIN verrà selezionato l'ultimo criterio configurato per l'esecuzione.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

È anche possibile usare OWIN per disconnettere l'utente dell'app. In `Controllers\AccountController.cs`:

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## Visualizzare le informazioni utente
Quando si autenticano gli utenti usando OpenID Connect, Azure AD restituisce un token ID all'app che contiene le **attestazioni**. Si tratta di asserzioni sull'utente. È possibile usare le attestazioni per personalizzare l'app.

Aprire il file `Controllers\HomeController.cs`. È possibile accedere alle attestazioni utente nei controller tramite l'oggetto entità di sicurezza `ClaimsPrincipal.Current`.

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
	Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
	ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

È possibile accedere a qualsiasi attestazione ricevuta dall'applicazione nello stesso modo. Nella pagina **Attestazioni** è disponibile un elenco di tutte le attestazioni ricevute dall'app.

## Eseguire l'app di esempio

A questo punto e possibile compilare ed eseguire l'app. Effettuare l'iscrizione all'app usando un indirizzo di posta elettronica o un nome utente. Disconnettersi e accedere nuovamente con lo stesso account utente. Modificare il profilo dell'utente. Disconnettersi ed effettuare l'iscrizione usando un account utente diverso. Si noti che le informazioni visualizzate nella scheda **Attestazioni** corrispondono alle informazioni configurate nei criteri.

## Aggiungere i provider di identità per i social network

L'app supporta attualmente solo l'iscrizione e l'accesso dell'utente con **account locali**. Si tratta di account archiviati nella directory B2C che usano un nome utente e una password. Tramite Azure AD B2C è possibile aggiungere il supporto per altri **provider di identità** (IdP) senza modificare il codice.

Per aggiungere provider di identità per i social media all'applicazione, seguire le istruzioni dettagliate fornite in questi articoli. Per ogni provider di identità che si vuole supportare, è necessario registrare un'applicazione nel relativo sistema e ottenere un ID client.

- [Configurare Facebook come provider di identità](active-directory-b2c-setup-fb-app.md)
- [Configurare Google come provider di identità](active-directory-b2c-setup-goog-app.md)
- [Configurare Amazon come provider di identità](active-directory-b2c-setup-amzn-app.md)
- [Configurare LinkedIn come provider di identità](active-directory-b2c-setup-li-app.md)

Dopo aver aggiunto i provider di identità alla directory B2C, è necessario modificare ognuno dei tre criteri per includere i nuovi provider di identità, come descritto nell'[articolo di riferimento sui criteri](active-directory-b2c-reference-policies.md). Dopo aver salvato i criteri, eseguire nuovamente l'app. I nuovi provider di identità dovrebbero essere stati aggiunti tra le opzioni di accesso e iscrizione in ognuna delle esperienze per l'identità.

Provare a usare i criteri e osservare gli effetti sull'app di esempio. Aggiungere o rimuovere provider di identità, manipolare le attestazioni dell'applicazione o modificare gli attributi per l'iscrizione. Fare delle prove fino a quando non è chiaro il modo in cui criteri, richieste di autenticazione e OWIN sono collegati tra loro.

Come riferimento viene fornito l'esempio completato, senza i valori di configurazione, [come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). È anche possibile clonarlo da GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->