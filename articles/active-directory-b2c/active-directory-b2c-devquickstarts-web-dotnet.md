<properties
	pageTitle="Anteprima di Azure AD B2C | Microsoft Azure"
	description="Come creare un'applicazione Web con esperienze di accesso, iscrizione e gestione del profilo tramite Azure AD B2C."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Anteprima di Azure AD B2C: Creare un'app Web .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD B2C consente di aggiungere potenti funzionalità di gestione delle identità self-service all'app Web con pochi brevi passaggi. Questo articolo descrive come creare un'app Web MVC .NET con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente. Include anche il supporto per l'iscrizione e l'accesso con un nome utente o un indirizzo di posta elettronica o con gli account dei social network, ad esempio Facebook e Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, passare a [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

## 2\. Creare un'applicazione

A questo punto, è necessario creare un'app nella directory B2C, che fornisce ad Azure AD alcune informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di

- Includere un'**app Web/API Web** nell'applicazione
- Specificare `https://localhost:44316/` come **URL di risposta**. Si tratta dell'URL predefinito per questo esempio di codice.
- Copiare l'**ID applicazione** assegnato all'app, perché verrà richiesto a breve.

[AZURE.INCLUDE [Active-Directory-B2C-devquickstarts-v2-Apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [**criteri**](active-directory-b2c-reference-policies.md) specifici. Questo esempio di codice contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. Sarà necessario creare i criteri per ciascun tipo, come descritto nell'articolo [riferimento ai criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere **Iscrizione tramite ID utente** o **Iscrizione tramite indirizzo di posta elettronica** nel pannello dei provider di identità.
- Scegliere il **Nome visualizzato** e alcuni altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere l'attestazione **Nome visualizzato** come attestazione dell'applicazione in tutti i criteri. È consentito scegliere anche altri criteri.
- Copiare il **Nome** dei criteri dopo averli creati. Sarà necessario usare i nomi dei criteri a breve. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla creazione dell'app.

## 4\. Scaricare il codice e configurare l'autenticazione

Il codice per questo esempio è disponibile [su GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Per creare l'esempio passo dopo passo, è possibile [scaricare un progetto scheletro come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o clonare lo scheletro:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

L'esempio completato è anche [disponibile come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, per iniziare aprire il file `.sln` in Visual Studio.

L'app comunica con Azure AD B2C inviando richieste di autenticazione HTTP e specificando i criteri da esempio come parte della richiesta. Per le applicazioni Web .NET, è possibile usare la libreria OWIN (Open Web Interface for .NET) di Microsoft per inviare richieste di autenticazione OpenID Connect, eseguire criteri, gestire la sessione dell'utente e così via.

Per iniziare, aggiungere i pacchetti NuGet del middleware OWIN al progetto usando la Console di Gestione pacchetti.

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
    <add key="ida:ClientId" value="[Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609]" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit]" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Aggiungere al progetto una "classe di avvio OWIN" denominata `Startup.cs`. Fare clic con il pulsante destro del mouse sul progetto --> **Aggiungi** --> **Nuovo elemento** --> Cercare "OWIN". Sostituire la dichiarazione della classe con `public partial class Startup`. Parte di questa classe è già stata implementata in un altro file. Il middleware OWIN richiamerà il metodo `Configuration(...)` all'avvio dell'app. In questo metodo effettuare una chiamata a ConfigureAuth(...), dove verrà configurata l'autenticazione per l'app.

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

Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(...)`. I parametri forniti in `OpenIdConnectAuthenticationOptions` fungeranno da coordinate per consentire all'app di comunicare con Azure AD. Sarà anche necessario configurare l'autenticazione tramite cookie: il middleware OpenID Connect usa i cookie per mantenere la sessione utente, tra le altre cose.

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
            // endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
            // The first parameter is the metadata URL of your B2C directory
            // The second parameter is an array of the policies that your app will use.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {  
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);
            
    }
```

## 5\. Inviare richieste di autenticazione ad Azure AD
L'app è ora configurata correttamente per comunicare con Azure AD B2C mediante il protocollo di autenticazione OpenID Connect. OWIN ha gestito tutte le difficoltà derivanti dalla creazione dei messaggi di autenticazione, dalla convalida dei token da Azure AD e dalla gestione della sessione utente. A questo punto, non resta che avviare ogni flusso utente.

Quando un utente fa clic sui pulsanti "Iscrizione", "Accesso" o "Modifica profilo" nell'app Web, l'azione associata verrà richiamata in `Controllers\AccountController.cs`. In ogni caso, è possibile usare i metodi OWIN predefiniti per attivare i criteri corretti:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by adding it to the AuthenticationProperties using the PolicyKey provided.
    
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

È anche possibile usare un tag `PolicyAuthorize` nei controller per richiedere l'esecuzione di determinati criteri se l'utente non ha ancora eseguito l'accesso. Aprire `Controllers\HomeController.cs` e aggiungere il tag `[PolicyAuthorize]` al controller Claims. Assicurarsi di sostituire i criteri di esempio con i propri criteri di accesso.

```C#
// Controllers\HomeController.cs

// You can use the PolicyAuthorize decorator to execute a certain policy if the user is not already signed into the app.
[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
  ...
```

Ora è possibile usare OWIN anche per disconnettere l'utente dall'app. Di nuovo in `Controllers\AccountController.cs`:

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request using the last policy that the user executed.
    // This is as easy as looking up the current value of the ACR claim, adding it to the AuthenticationProperties, and making an OWIN SignOut call.

    HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string> 
            { 
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

Per impostazione predefinita, OWIN non invierà i criteri specificati in `AuthenticationProperties` ad Azure AD. È comunque possibile modificare le richieste che OWIN genera nella notifica `RedirectToIdentityProvider`. Usare questa notifica in `App_Start\Startup.Auth.cs` per recuperare l'endpoint corretto per i criteri dai metadati dei criteri. Questo garantisce che ad Azure AD venga inviata la richiesta corretta per i criteri che verranno eseguiti dall'app.

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

## 6\. Visualizzare le informazioni utente
Quando si autenticano gli utenti con OpenID Connect, Azure AD restituisce un id\_token all'app contenente **attestazioni**, ovvero asserzioni sull'utente. È possibile usare queste attestazioni per personalizzare l'app.

Aprire il file `Controllers\HomeController.cs`. È possibile accedere alle attestazioni dell'utente nei controller tramite l'oggetto di entità di sicurezza `ClaimsPrincipal.Current`.

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

È possibile accedere a qualsiasi attestazione ricevuta dall'applicazione nello stesso modo. È possibile esaminare un elenco delle attestazioni ricevute dall'app è stato stampato nella pagina Attestazioni.

## 7\. Eseguire l'app di esempio

Infine compilare ed eseguire l'app. Eseguire l'iscrizione per l'app usando un indirizzo di posta elettronica o un nome utente. Disconnettersi e accedere nuovamente con lo stesso account utente. Modificare il profilo dell'utente. Disconnettersi ed eseguire l'iscrizione usando un account utente diverso. Osservare che le informazioni visualizzate nella scheda **Attestazioni** corrispondono alle informazioni configurate nei criteri.

## 8\. Aggiungere i provider di identità per i social network

Attualmente, l'app supporta solo l'iscrizione e l'accesso con gli account denominati **account locali**, ovvero account archiviati nella directory B2C con un nome utente e una password. Con Azure AD B2C, è possibile aggiungere il supporto per altri **provider di identità**, o IDP, senza modificare il codice.

Per aggiungere provider di identità per i social network all'applicazione, seguire le istruzioni dettagliate fornite in uno di questi due articoli. Per ogni provider di identità che si vuole supportare, sarà necessario registrare un'applicazione nel sistema del provider e ottenere un ID client.

- [Configurare Facebook come provider di identità](active-directory-b2c-setup-fb-app.md)
- [Configurare Google come provider di identità](active-directory-b2c-setup-goog-app.md)
- [Configurare Amazon come provider di identità](active-directory-b2c-setup-amzn-app.md)
- [Configurare LinkedIn come provider di identità](active-directory-b2c-setup-li-app.md) 

Dopo aver aggiunto i provider di identità alla propria directory B2C, sarà necessario tornare indietro e modificare ognuno dei tre criteri per includere i nuovi provider di identità, come descritto nell'articolo di [riferimento ai criteri](active-directory-b2c-reference-policies.md). Dopo aver salvato i criteri, eseguire di nuovo l'app. I nuovi provider di identità dovrebbero essere stati aggiunti tra le opzioni di accesso e iscrizione in ognuna delle esperienze per l'identità.

È possibile sperimentare liberamente i criteri e osservare gli effetti generati nell'app, ad esempio aggiungere o rimuovere i provider di identità, modificare le attestazioni dell'applicazione o modificare gli attributi dell'iscrizione. Fare delle prove fino a quando non è chiaro il modo in cui criteri, richieste di autenticazione e OWIN sono collegati tra loro.

Come riferimento, l'esempio completato (senza i valori di configurazione) [è disponibile in un file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO1-->