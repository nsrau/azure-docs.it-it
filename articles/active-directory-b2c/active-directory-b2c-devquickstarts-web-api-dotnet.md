<properties
	pageTitle="Anteprima di Azure Active Directory B2C | Microsoft Azure"
	description="Come creare un'applicazione Web che chiama un'API Web usando Azure Active Directory B2C."
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Anteprima di Azure AD B2C: Chiamare un'API Web da un'app Web .NET


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure Active Directory (Azure AD) B2C è possibile aggiungere funzionalità avanzate di gestione delle identità self-service alle app Web e alle API Web in pochi brevi passaggi. Questo articolo descrive come creare un'app Web Model-View-Controller (MVC) .NET "To do list" che chiama un'API Web .NET usando token di connessione OAuth 2.0. L'app Web e l'API Web usano entrambe Azure AD B2C per gestire le identità utente e autenticare gli utenti.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Questo articolo non descrive come implementare le esperienze di accesso, iscrizione e gestione del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha già effettuato l'autenticazione. Se non è già stato fatto, iniziare con l'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) per acquisire nozioni di base su Azure AD B2C.

## Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e altro ancora. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire in questa guida.

## Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. In questo caso l'app Web e l'API Web saranno rappresentate da un unico **ID applicazione**, perché includono un'app per la logica. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un'**app Web/API Web** nell'applicazione.
- Immettere `https://localhost:44316/` come **URL di risposta**. Si tratta dell'URL predefinito per questo esempio di codice.
- Creare un **segreto applicazione** per l'applicazione e prenderne nota. Sarà necessario più avanti. Si noti che prima di usare questo valore è necessario [inserire un carattere di escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
- Copiare l'**ID applicazione** assegnato all'app. Sarà necessario più avanti.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md) specifici. Questa app Web contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. È necessario creare i criteri per ogni tipo, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere **Nome visualizzato** e altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere **Nome visualizzato** e **ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **nome** di ogni criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`. I nomi dei criteri saranno necessari in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

Si noti che questo articolo non illustra come usare i criteri appena creati. Per informazioni sul funzionamento dei criteri in Azure AD B2C, iniziare dall'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Scaricare il codice

Il codice per questa esercitazione [è disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Per creare l'esempio passo dopo passo, è possibile [scaricare il progetto struttura come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

L'app completata è anche [disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare. Si noti che nella soluzione sono presenti due progetti: un progetto `TaskWebApp` e un progetto `TaskService`. `TaskWebApp` è un'app Web Windows Presentation Foundation (WPF) front-end con cui l'utente interagisce. `TaskService` è l'API Web back-end dell'app in cui sono archiviati gli elenchi attività di ogni utente.

## Configurare il servizio dell’attività

Quando `TaskService` riceve una richiesta da `TaskWebApp`, verifica la presenza di un token di accesso valido per autenticare la richiesta. Per convalidare il token di accesso, è necessario fornire a `TaskService` alcune informazioni sull'app. Nel progetto `TaskService` aprire il file `web.config` nella radice del progetto e sostituire i valori nella sezione `<appSettings>`:

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

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


In questo articolo non verranno illustrati i dettagli della protezione di `TaskService`. Per informazioni sull'autenticazione sicura delle richieste da parte di un'API Web tramite Azure AD B2C, consultare l'[articolo di introduzione all'API Web](active-directory-b2c-devquickstarts-api-dotnet.md).

## Configurare l'app Web TaskWebApp

Perché `TaskWebApp` possa comunicare con Azure AD B2C, è necessario fornire alcuni parametri comuni. Nel progetto `TaskWebApp` aprire il file `web.config` nella radice del progetto e sostituire i valori nella sezione `<appSettings>`. Questi valori verranno usati nell'app Web.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Sono anche presenti due elementi Decorator `[PolicyAuthorize]` per cui è necessario specificare il nome dei criteri di accesso. L'attributo `[PolicyAuthorize]` è usato per richiamare criteri specifici quando un utente prova ad accedere a una pagina dell'app che richiede l'autenticazione.

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

## Ottenere i token di accesso e chiamare le API dell'attività

Questa sezione illustra come completare uno scambio di token OAuth 2.0 in un'app Web usando le librerie e i framework Microsoft. Se non si ha familiarità con i codici di autorizzazione e i token di accesso, vedere il [riferimento al protocollo OpenID Connect](active-directory-b2c-reference-protocols.md) per altre informazioni.

### Ottenere un codice di autorizzazione

Il primo passaggio della chiamata dell'API Web `TaskService` prevede l'autenticazione dell'utente e la ricezione di un codice di autorizzazione da Azure AD. È possibile ricevere un codice di autorizzazione da Azure AD al termine della corretta esecuzione di tutti i criteri, inclusi i criteri di accesso, iscrizione e modifica del profilo.

Per iniziare, installare il middleware OWIN OpenID Connect tramite la console di Gestione pacchetti di Visual Studio. Usare OWIN per inviare le richieste di autenticazione ad Azure AD e gestire le risposte:

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

Aprire il file `App_Start\Startup.Auth.cs`. Qui verrà configurata la pipeline di autenticazione OWIN, specificando i dettagli della directory B2C e dell'applicazione creata:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
	public const string PolicyKey = "b2cpolicy";
	public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

	// App config settings
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

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
				AuthenticationFailed = OnAuthenticationFailed,
				RedirectToIdentityProvider = OnRedirectToIdentityProvider,
				AuthorizationCodeReceived = OnAuthorizationCodeReceived,
			},
			Scope = "openid offline_access",

			// The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
			// endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
			ConfigurationManager = new PolicyConfigurationManager(
				String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
				new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

			// This piece is optional - it is used for displaying the user's name in the navigation bar.
			TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters
			{
				NameClaimType = "name",
			},
		};

		app.UseOpenIdConnectAuthentication(options);
	}
	...
}
```

### Ottenere un token di accesso usando il codice di autorizzazione

L'app Web è ora configurata per autenticare gli utenti tramite la directory B2C e per ricevere i codici di autorizzazione da Azure AD. Il passaggio successivo prevede lo scambio dei codici di autorizzazione per i token di accesso da Azure AD.

Ogni volta che le app Web .NET devono ottenere token di accesso da Azure AD, è possibile usare Active Directory Authentication Library (ADAL). Non è necessario usare ADAL per questo processo, ma tenere presente che questa libreria consente di gestire diverse attività, ad esempio l'invio di messaggi di autenticazione OAuth 2.0, la memorizzazione nella cache e l'aggiornamento di token.

Per prima cosa, installare ADAL nel progetto `TaskWebApp` tramite la console di Gestione pacchetti:

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

A questo punto è necessario passare il codice di autenticazione ad ADAL in modo che possa ottenere automaticamente i token. Il middleware OWIN OpenID Connect offre un servizio di notifica per usare il codice di autorizzazione. La notifica verrà inviata ogni volta che l'app riceve un codice di autorizzazione da Azure AD. In `App_Start\Startup.Auth.cs` implementare il gestore delle notifiche `OnAuthorizationCodeReceived` tramite ADAL:

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and it is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default. In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
```

### Ottenere un token di accesso nei controller

Dopo avere acquisito un token di accesso per il back-end `TaskService` e averlo memorizzato nella cache dei token di ADAL, è necessario usarlo. `TasksController` è responsabile della comunicazione con l'API `TaskService` e invia le richieste HTTP all'API per la lettura, la creazione e l'eliminazione di attività. Prima di inviare una richiesta HTTP, ottenere un token di accesso da ADAL:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	AuthenticationResult result = null;
	try
	{
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

		// We don't care which policy is used to access the TaskService, so let's use the most recent policy
		string mostRecentPolicy = ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value;

		// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache and throw an exception if it cannot do so.
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser, mostRecentPolicy);

		...
	}
	catch (AdalException ee)
	{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
	}
	...
}
```

ADAL gestisce la memorizzazione dei token nella cache, l'aggiornamento dei token alla scadenza e la generazione di eccezioni che indicano quando l'utente deve eseguire di nuovo l'accesso. È sufficiente chiamare `AuthenticationContext.AcquireTokenSilentAsync(...)` ogni volta che è necessario un token per l'app.

### Leggere le attività dall'API Web

Dopo aver ottenuto un token, collegarlo alla richiesta HTTP `GET` nell'intestazione `Authorization` per chiamare `TaskService` in modo sicuro:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	...

	try
	{
		HttpClient client = new HttpClient();
		HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

		// Add the token acquired from ADAL to the request headers
		request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
		HttpResponseMessage response = await client.SendAsync(request);

		if (response.IsSuccessStatusCode)
		{
			String responseString = await response.Content.ReadAsStringAsync();
			JArray tasks = JArray.Parse(responseString);
			ViewBag.Tasks = tasks;
			return View();
		}
		else
		{
			// If the call failed with access denied, then drop the current access token from the cache,
			// and show the user an error that indicates that they might need to sign in again.
			if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
			{
				var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
				foreach (TokenCacheItem tci in todoTokens)
					authContext.TokenCache.DeleteItem(tci);

				return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
			}
		}

		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
	}
	catch (Exception ex)
	{
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
	}
}

```

### Creare ed eliminare attività nell'API Web

Seguire lo stesso modello quando si inviano richieste `POST` e `DELETE` a `TaskService`. Chiamare `AuthenticationContext.AcquireTokenSilentAsync(...)` e collegare il token risultante alla richiesta nell'intestazione `Authorization`. L'azione di creazione è già stata implementata. Provare a completare l'azione di eliminazione in `TasksController.cs`.

## Disconnettere l'utente

Quando l'utente si disconnette dall'app Web, è opportuno svuotare la cache dei token di ADAL per rimuovere eventuali residui della sessione autenticata dell'utente:

```C#
// Controllers/AccountController.cs

public void SignOut()
{
	if (Request.IsAuthenticated)
	{
		// When the user signs out, clear their token cache in the process
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		authContext.TokenCache.Clear();

		HttpContext.GetOwinContext().Authentication.SignOut(
		new AuthenticationProperties(
			new Dictionary<string, string>
			{
				{Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
			}), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
	}
}
```

## Eseguire l'app di esempio

Infine compilare ed eseguire `TaskClient` e `TaskService`. Eseguire l'iscrizione e l'accesso all'app. Creare le attività per l'utente connesso. Disconnettersi ed eseguire l'accesso usando un account utente diverso. Creare le attività per l'utente. Si noti che le attività sono archiviate per ogni utente nell'API, perché l'API estrae l'identità dell'utente dai token di accesso che riceve.

Come riferimento, l'esempio completo [è disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). È anche possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->