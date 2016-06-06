<properties
	pageTitle="Anteprima di Azure Active Directory B2C | Microsoft Azure"
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
	ms.date="02/19/2016"
	ms.author="dastrock"/>

# Anteprima di Azure AD B2C: Compilare un'app Web .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C permette di aggiungere funzionalità efficaci di gestione delle identità self-service all'app Web con pochi brevi passaggi. Questo articolo descrive come creare un'app Web MVC (Model-View-Controller) .NET con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente. L'app includerà il supporto per l'iscrizione e l'accesso tramite un nome utente o un indirizzo di posta elettronica e usando account di social media quali Facebook e Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire in questa guida.

## Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un'**app Web o un'API Web** nell'applicazione.
- Immettere `https://localhost:44316/` come **URI di reindirizzamento**. Si tratta dell'URL predefinito per questo esempio di codice.
- Copiare l'**ID applicazione** assegnato all'app. Sarà necessario più avanti.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md) specifici. Questo esempio di codice contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. È necessario creare i criteri per ogni tipo, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).

>[AZURE.NOTE] Azure AD B2C supporta anche criteri di registrazione o accesso combinati non illustrati in questa esercitazione. I criteri di registrazione o accesso sono illustrati in [questa esercitazione equivalente](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere **User ID sign-up** (Registrazione tramite ID utente) o **Email sign-up** (Registrazione tramite indirizzo di posta elettronica) nel pannello dei provider di identità.
- Scegliere **Display name** (Nome visualizzato) e altri attributi per la registrazione nei criteri di registrazione.
- Scegliere l'attestazione di **Display name** (Nome visualizzato) come attestazione dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **nome** di ogni criterio dopo averlo creato. I nomi dei criteri saranno necessari in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

## Scaricare il codice e configurare l'autenticazione

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-devquickstarts-bug-fix.md)]

Il codice per questo esempio è [disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Per creare l'esempio passo dopo passo, è possibile [scaricare la struttura di progetto come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

L'esempio completato è [disponibile anche come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare.

L'app comunica con Azure AD B2C inviando richieste di autenticazione HTTP che specificano i criteri da eseguire come parte della richiesta. Per le applicazioni Web .NET, è possibile usare la libreria OWIN di Microsoft per inviare richieste di autenticazione OpenID Connect, eseguire criteri, gestire le sessioni utente e così via.

Per iniziare, aggiungere i pacchetti NuGet del middleware OWIN al progetto usando la Console di Gestione pacchetti di Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Aprire quindi il file `web.config` nella radice del progetto e immettere i valori di configurazione dell'app nella sezione `<appSettings>`.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="[Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com]" />
    <add key="ida:ClientId" value="[Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609]" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit]" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Aggiungere al progetto una classe di avvio OWIN denominata `Startup.cs`. Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi**, **Nuovo elemento** e quindi cercare "OWIN". Modificare la dichiarazione di classe in `public partial class Startup`. Parte di questa classe è stata implementata in un altro file. Il middleware OWIN richiamerà il metodo `Configuration(...)` all'avvio dell'app. In questo metodo effettuare una chiamata a `ConfigureAuth(...)`, in cui viene configurata l'autenticazione per l'app.

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
    // The ACR claim is used to indicate which policy was executed
    public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
    public const string PolicyKey = "b2cpolicy";
    public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

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

        OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
        {
            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
                RedirectToIdentityProvider = OnRedirectToIdentityProvider,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
            // endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
            // The first parameter is the metadata URL of your B2C directory.
            // The second parameter is an array of the policies that your app will use.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional. It is used to display the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {  
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);

    }
```

## Inviare richieste di autenticazione ad Azure AD
L'app è ora configurata correttamente per comunicare con Azure AD B2C usando il protocollo di autenticazione OpenID Connect. OWIN ha gestito tutti dettagli relativi alla creazione dei messaggi di autenticazione, alla convalida dei token da Azure AD e alla gestione della sessione utente. A questo punto, non resta che avviare ogni flusso utente.

Quando un utente seleziona **Sign up** (Registrazione), **Sign in** (Accesso) o **Edit profile** (Modifica profilo) nell'app Web, l'azione associata viene richiamata in `Controllers\AccountController.cs`. In ogni caso, è possibile usare i metodi OWIN predefiniti per attivare i criteri corretti:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by adding it to the AuthenticationProperties by using the PolicyKey provided.

        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties (
                new Dictionary<string, string>
                {
                    {Startup.PolicyKey, Startup.SignInPolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string>
                {
                    {Startup.PolicyKey, Startup.SignUpPolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string>
                {
                    {Startup.PolicyKey, Startup.ProfilePolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

È anche possibile usare un tag `PolicyAuthorize` personalizzato nei controller per richiedere l'esecuzione di determinati criteri se l'utente non ha eseguito l'accesso. Aprire `Controllers\HomeController.cs` e aggiungere il tag `[PolicyAuthorize]` al controller delle attestazioni. Sostituire i criteri di esempio con i criteri di accesso.

```C#
// Controllers\HomeController.cs

// You can use the PolicyAuthorize decorator to execute a certain policy if the user is not already signed in the app.
[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
  ...
```

È anche possibile usare OWIN per disconnettere l'utente dell'app. In `Controllers\AccountController.cs`:

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenID Connect sign-out request by using the last policy that the user executed.
    // This is as easy as looking up the current value of the ACR claim, adding it to the AuthenticationProperties, and making an OWIN SignOut call.

    HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string>
            {
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

Per impostazione predefinita, OWIN non invia i criteri specificati in `AuthenticationProperties` ad Azure AD. È comunque possibile modificare le richieste che OWIN genera nella notifica `RedirectToIdentityProvider`. Usare questa notifica in `App_Start\Startup.Auth.cs` per recuperare l'endpoint corretto per i criteri dai metadati dei criteri. Questo garantisce che ad Azure AD venga inviata la richiesta corretta per i criteri che devono essere eseguiti dall'app.

```C#
// App_Start\Startup.Auth.cs

private async Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    PolicyConfigurationManager mgr = notification.Options.ConfigurationManager as PolicyConfigurationManager;
    if (notification.ProtocolMessage.RequestType == OpenIdConnectRequestType.LogoutRequest)
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseRevoke.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.EndSessionEndpoint;
    }
    else
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseChallenge.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.AuthorizationEndpoint;
    }
}
```

## Visualizzare le informazioni utente
Quando si autenticano gli utenti usando OpenID Connect, Azure AD restituisce un token ID all'app che contiene **attestazioni**. Si tratta di asserzioni sull'utente. È possibile usare le attestazioni per personalizzare l'app.

Aprire il file `Controllers\HomeController.cs`. È possibile accedere alle attestazioni dell'utente nei controller tramite l'oggetto entità di sicurezza `ClaimsPrincipal.Current`.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
	Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
	ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

È possibile accedere a qualsiasi attestazione ricevuta dall'applicazione nello stesso modo. Un elenco di tutte le attestazioni ricevute dall'app è disponibile nella pagina **Attestazioni**.

## Eseguire l'app di esempio

A questo punto e possibile compilare ed eseguire l'app. Effettuare l'iscrizione all'app usando un indirizzo di posta elettronica o un nome utente. Disconnettersi e accedere nuovamente con lo stesso account utente. Modificare il profilo dell'utente. Disconnettersi ed effettuare l'iscrizione usando un account utente diverso. Si noti che le informazioni visualizzate nella scheda **Attestazioni** corrispondono alle informazioni configurate nei criteri.

## Aggiungere i provider di identità per i social network

Attualmente l'app supporta solo la registrazione e l'accesso dell'utente tramite **account locali**. Si tratta di account archiviati nella directory B2C che usano un nome utente e una password. Tramite Azure AD B2C è possibile aggiungere il supporto per altri **provider di identità** (IDP) senza modificare il codice.

Per aggiungere provider di identità per i social media all'applicazione, seguire le istruzioni dettagliate fornite in questi articoli. Per ogni provider di identità che si vuole supportare, è necessario registrare un'applicazione nel relativo sistema e ottenere un ID client.

- [Configurare Facebook come provider di identità](active-directory-b2c-setup-fb-app.md)
- [Configurare Google come provider di identità](active-directory-b2c-setup-goog-app.md)
- [Configurare Amazon come provider di identità](active-directory-b2c-setup-amzn-app.md)
- [Configurare LinkedIn come provider di identità](active-directory-b2c-setup-li-app.md)

Dopo aver aggiunto i provider di identità alla directory B2C, è necessario modificare ognuno dei tre criteri per includere i nuovi provider di identità, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md). Dopo aver salvato i criteri, eseguire nuovamente l'app. I nuovi provider di identità dovrebbero essere stati aggiunti tra le opzioni di accesso e iscrizione in ognuna delle esperienze per l'identità.

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

<!---HONumber=AcomDC_0525_2016-->