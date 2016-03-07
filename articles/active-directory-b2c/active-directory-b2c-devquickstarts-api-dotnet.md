<properties
	pageTitle="Anteprima di Azure AD B2C | Microsoft Azure"
	description="Come compilare un'API Web .NET usando Azure Active Directory B2C e protetta con i token di accesso OAuth 2.0 per l'autenticazione."
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
	ms.date="12/22/2015"
	ms.author="dastrock"/>

# Anteprima di Azure Active Directory B2C: Compilare un'API Web .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C permette di proteggere un'API Web usando i token di accesso OAuth 2.0. I token di accesso consentono alle app client che usano Azure AD B2C di eseguire l'autenticazione all'API. Questo articolo descrive come creare un'app elenco attività MVC (Model-View-Controller) .NET con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente. L'elenco attività di ogni utente verrà archiviato da un servizio dell'attività, ovvero un'API Web che consente agli utenti autenticati di creare e leggere attività nel proprio elenco attività.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creare una directory Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire in questa guida.

## Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un'**app Web** o un'**API Web** nell'applicazione.
- Per l'app Web, usare l'**URI di reindirizzamento** `https://localhost:44316/`. Si tratta della posizione predefinita del client dell'app Web per questo codice di esempio.
- Copiare l'**ID applicazione** assegnato all'app. Sarà necessario più avanti.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md) specifici. Il client in questo esempio di codice contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. È necessario creare i criteri per ogni tipo, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri assicurarsi di:

- Scegliere l'**iscrizione tramite ID utente** o l'**iscrizione tramite indirizzo di posta elettronica** nel pannello dei provider di identità.
- Scegliere il **Nome visualizzato** e altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere le attestazioni **Nome visualizzato** e **ID oggetto** com attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **Nome** di ogni criterio dopo averlo creato. I nomi dei criteri saranno necessari in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

## Scaricare il codice

Il codice per questa esercitazione è [disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Per creare l'esempio passo dopo passo, è possibile [scaricare un progetto struttura come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

L'app completata è anche [disponibile come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare. Il file della soluzione contiene due progetti: `TaskWebApp` e `TaskService`. `TaskWebApp` è un'applicazione Web MVC con cui l'utente interagisce. `TaskService` è l'API Web back-end dell'app in cui sono archiviati gli elenchi attività di ogni utente.

## Configurare l'app Web TaskWebApp

Quando l'utente interagisce con `TaskWebApp`, il client invia richieste ad Azure AD e ottiene i token da usare per chiamare l'API Web `TaskService`. Per consentire l'accesso dell'utente e ottenere i token, è necessario fornire a `TaskWebApp` alcune informazioni sull'app. Nel progetto `TaskWebApp` aprire il file `web.config` nella radice del progetto e sostituire i valori nella sezione `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Sono anche presenti due elementi `[PolicyAuthorize]` decorator per cui è necessario specificare il nome dei criteri di accesso. L'attributo `[PolicyAuthorize]` è usato per richiamare criteri specifici quando un utente prova ad accedere a una pagina dell'app che richiede l'autenticazione.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

Per informazioni su come un'app Web come `TaskWebApp` usa AD B2C Azure, vedere [Compilare un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Proteggere l'API

Ora che è disponibile un client che chiama l'API per conto degli utenti, è possibile proteggere `TaskService` usando i token di connessione OAuth 2.0. L'API può accettare e convalidare i token usando la libreria OWIN (Open Web Interface for .NET) di Microsoft.

### Installare OWIN
Per iniziare, installare la pipeline di autenticazione OWIN OAuth:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

### Immettere le informazioni B2C
Aprire il file `web.config` nella radice del progetto `TaskService` e sostituire i valori nella sezione `<appSettings>`. Questi valori verranno usati nell'API e nella libreria OWIN.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```

### Aggiungere una classe di avvio OWIN
Aggiungere al progetto `TaskService` una classe di avvio OWIN denominata `Startup.cs`. Fare clic con il pulsante destro del mouse sul progetto, selezionare **Aggiungi**, **Nuovo elemento** e quindi cercare OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
	// The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### Configurare l'autenticazione OAuth 2.0
Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(...)`.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	// These values are pulled from web.config
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
	private const string discoverySuffix = ".well-known/openid-configuration";

	public void ConfigureAuth(IAppBuilder app)
	{   
		TokenValidationParameters tvps = new TokenValidationParameters
		{
			// This is where you specify that your API accepts tokens only from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata and signing keys from the OpenID Connect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

### Proteggere il controller dell'attività
Dopo aver configurato l'app per usare l'autenticazione OAuth 2.0, per proteggere l'API Web è possibile aggiungere un tag `[Authorize]` al controller dell'attività. Si tratta del controller in cui avvengono tutte le modifiche relative all'elenco attività. Sarà quindi necessario proteggere l'intero controller a livello della classe. È anche possibile aggiungere il tag `[Authorize]` a singole azioni per un controllo più dettagliato.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

### Ottenere le informazioni utente dal token
`TasksController` archivia le attività in un database in cui a ogni attività è associato un utente "proprietario" dell'attività. Il proprietario è identificato dall'**ID oggetto** dell'utente. Per questo motivo è necessario aggiungere l'ID oggetto come attestazione dell'applicazione in tutti i criteri.

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## Eseguire l'app di esempio

Infine, compilare ed eseguire sia `TaskWebApp` che `TaskService`. Effettuare l'iscrizione all'app usando un indirizzo di posta elettronica o un nome utente. Creare alcune attività nell'elenco attività dell'utente e osservarne la persistenza nell'API anche dopo l'arresto e il riavvio del client.

## Modificare i criteri

Dopo aver protetto l'API con Azure AD B2C, è possibile provare i criteri dell'app e visualizzarne gli effetti o l'assenza di effetti nell'API. È possibile <!--add **identity providers** to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->modificare le attestazioni dell'applicazione nei criteri e modificare le informazioni utente disponibili nell'API Web. Le eventuali attestazioni aggiunte saranno disponibili per l'API Web MVC .NET nell'oggetto `ClaimsPrincipal`, come descritto in precedenza.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0224_2016-->