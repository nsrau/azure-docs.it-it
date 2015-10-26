<properties
	pageTitle="Anteprima di Azure AD B2C | Microsoft Azure"
	description="Come creare un'API Web .NET usando Azure AD B2C protetta tramite i token di accesso OAuth 2.0 per l'autenticazione."
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
	
# Anteprima di Azure AD B2C: Creare un'API Web .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD B2C consente di proteggere un'API Web tramite token di accesso OAuth 2.0 e permettere alle app client che usano Azure AD B2C di eseguire l'autenticazione all'API. Questo articolo descrive come creare un'app Web MVC .NET di elenco attività "To-Do List" che include funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente. Ogni elenco attività dell'utente verrà archiviato da un servizio attività, ovvero un'API Web che consente agli utenti autenticati di creare e leggere attività nel proprio elenco attività.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, passare a [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

## 2\. Creare un'applicazione

A questo punto, è necessario creare un'app nella directory B2C, che fornisce ad Azure AD alcune informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di

- Includere un'**app Web/API Web** nell'applicazione.
- Per l'app Web, usare l'**URI di reindirizzamento** `https://localhost:44316/`: si tratta della posizione predefinita del client di app Web per questo codice di esempio.
- Copiare l'**ID applicazione** assegnato all'app, perché verrà richiesto a breve.

 [AZURE.INCLUDE [Active-Directory-B2C-devquickstarts-v2-Apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [**criteri**](active-directory-b2c-reference-policies.md) specifici. Il client in questo esempio di codice contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. Sarà necessario creare i criteri per ciascun tipo, come descritto nell'articolo [riferimento ai criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere **Iscrizione tramite ID utente** o **Iscrizione tramite indirizzo di posta elettronica** nel pannello dei provider di identità.
- Scegliere il **Nome visualizzato** e alcuni altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere le attestazioni **Nome visualizzato** e **ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altri criteri.
- Copiare il **Nome** di ciascun criterio dopo averlo creato. Sarà necessario usare i nomi dei criteri a breve. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla creazione dell'app.

## 4\. Scaricare il codice

Il codice per questa esercitazione è disponibile [in GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Per creare l'esempio passo dopo passo, è possibile [scaricare un progetto scheletro come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip) o clonare lo scheletro:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

L'app completata è anche [disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, per iniziare aprire il file `.sln` in Visual Studio. Osservare che nella soluzione sono presenti due progetti: un progetto `TaskWebApp` e un progetto `TaskService`. `TaskWebApp` è un'applicazione Web MVC con cui interagisce l'utente. `TaskService` è l'API Web back-end dell'app che archivia le applicazioni "To-Do List" degli utenti.

## 5\. Configurare l'app Web TaskWebApp

Quando l'utente interagisce con l'app `TaskWebApp`, il client invia le richieste ad Azure AD e riceve a sua volta i token da usare per chiamare l'API Web `TaskService`. Per consentire l'accesso dell'utente e ottenere i token, è necessario fornire all'app `TaskWebApp` alcune informazioni sull'app. Nel progetto `TaskWebApp` aprire il file `web.config` nella radice del progetto e sostituire i valori nella sezione `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Sono anche presenti due elementi `[PolicyAuthorize]` decorator in cui è necessario specificare il nome dei criteri di accesso. L'attributo `[PolicyAuthorize]` è usato per richiamare criteri specifici quando l'utente tenta di accedere a una pagina dell'app che richiede l'autenticazione.

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

Per informazioni su come un'app Web come `TaskWebApp` usa Azure AD B2C, vedere l'articolo della guida introduttiva che illustra come [creare un'app Web con il supporto per l'accesso](active-directory-b2c-devquickstarts-web-dotnet.md).

## 6\. Proteggere l'API

Ora che è stato creato un client che chiama l'API per conto degli utenti, è possibile proteggere il servizio `TaskService` tramite token di connessione OAuth 2.0. L'API può accettare e convalidare i token usando la libreria OWIN di Microsoft.

#### Installare OWIN
Per iniziare, installare la pipeline di autenticazione OWIN OAuth:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

#### Immettere le informazioni B2C
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

#### Aggiungere una classe di avvio OWIN
Aggiungere al progetto `TaskService` una "classe di avvio OWIN" denominata `Startup.cs`. Fare clic con il pulsante destro del mouse sul progetto --> **Aggiungi** --> **Nuovo elemento** --> Cercare "OWIN".
  

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

#### Configurare l'autenticazione OAuth 2.0
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
			// This is where you specify that your API only accepts tokens from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

#### Proteggere il controller dell'attività
Ora che l'app è stata configurata per usare l'autenticazione OAuth 2.0, per proteggere l'API Web è sufficiente aggiungere un tag `[Authorize]` al controller dell'attività. Si tratta del controller in cui avvengono tutte le modifiche relative all'elenco attività, quindi sarà necessario proteggere l'intero controller a livello della classe. È anche possibile aggiungere il tag `[Authorize]` a singole azioni per un controllo più dettagliato.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

#### Ottenere le informazioni utente dal token
`TaskController` archivia le attività in un database in cui a ogni attività è associato un utente "proprietario" dell'attività. Il proprietario viene identificato tramite l'**ID oggetto** dell'utente (ecco perché è stato necessario aggiungere l'ID oggetto come attestazione dell'applicazione in tutti i criteri):

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## 7\. Eseguire l'app di esempio

Infine, compilare ed eseguire sia `TaskWebApp` che `TaskService`. Eseguire l'iscrizione per l'app usando un indirizzo di posta elettronica o un nome utente. Creare alcune attività nell'elenco attività dell'utente e osservarne la persistenza nell'API anche dopo l'arresto e il riavvio del client.

## 8\. Modificare i criteri

Ora che l'API è stata protetta tramite Azure AD B2C, è possibile effettuare delle prove con i criteri dell'app e visualizzarne gli effetti (o l'assenza di effetti) nell'API. È possibile <!--add **identity providers**
to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->modificare le **attestazioni dell'applicazione** nei criteri e modificare le informazioni utente disponibili nell'API Web. Le eventuali attestazioni supplementari che si aggiungono saranno disponibili per l'API Web MVC .NET nell'oggetto `ClaimsPrincipal`, come descritto in precedenza.

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO3-->