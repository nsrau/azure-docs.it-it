<properties
	pageTitle="Modello app 2.0 | Microsoft Azure"
	description="Come creare un'API Web .NET MVC che accetta token da account Microsoft personali, aziendali e dell'istituto di istruzione."
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
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Anteprima di Modello app 2.0: Proteggere un'API Web MVC

Con Modello app 2.0 è possibile proteggere un'API Web usando token di accesso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), consentendo agli utenti di accedere all'API Web in modo sicuro con un account Microsoft personale, aziendale e dell'istituto di istruzione.

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

Nelle API Web ASP.NET, a questo scopo si usa il middleware OWIN di Microsoft incluso in .NET Framework 4.5. Qui si userà OWIN per creare un'API Web MVC "To Do List" che: - Consente ai client di creare e leggere attività da un elenco di attività dell'utente - Designa le API da proteggere - Verifica che le chiamate all'API Web contengano un token di accesso valido.

A questo scopo è necessario:

1. Registrare un'app con Azure AD.
2. Configurare l'app per l'uso della pipeline di autenticazione OWIN.
3. Configurare un'app client per chiamare l'API Web To Do List.

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet). Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

L'app completa viene inoltre fornita alla fine di questa esercitazione.

## 1. Registrare un'app

Creare una nuova app [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o seguire questi [passaggi dettagliati] (active-directory-v2-app-registration.md).  Assicurarsi di:

- Copiare l'**ID applicazione** assegnato all'app; sarà necessario a breve.

Questa soluzione di Visual Studio contiene anche una "TodoListClient", che è una semplice applicazione WPF.  L'applicazione TodoListClient viene utilizzata per dimostrare il modo in cui effettua l'accesso un utente e il modo in cui un client può emettere richieste per le API Web.  In questo caso, sia l'applicazione TodoListClient che TodoListService sono rappresentate dalla stessa applicazione.  Per configurare l'applicazione TodoListClient, inoltre, è necessario:

- Aggiungere la piattaforma **mobile** per l'app.
- Copiare l'**URI di reindirizzamento** dal portale. È necessario utilizzare il valore predefinito `urn:ietf:wg:oauth:2,0 :oob`.


## 2. Impostare l'app per l'utilizzo della pipeline di autenticazione OWIN

Ora che è stata registrata un'app, è necessario impostarla per comunicare con l'endpoint v2.0 endpoint per convalidare le richieste in arrivo e i token.

-	Innanzitutto aprire la soluzione e aggiungere i pacchetti NuGet del middleware OWIN al progetto TodoListService usando la console di Gestione pacchetti

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService 
```

-	Aggiungere al progetto TodoListService una OWIN Startup Class denominata `Startup.cs`. Fare clic con il pulsante destro del mouse sul progetto --> **Aggiungi** --> **Nuovo elemento** --> Cercare "OWIN". Il middleware OWIN richiamerà il metodo `Configuration(…)` all'avvio dell'app.
-	Sostituire la dichiarazione della classe con `public partial class Startup`. Parte di questa classe è già stata implementata in un altro file. Nel metodo `Configuration(…)` eseguire una chiamata a ConfigureAuth(...) per configurare l'autenticazione per l'app Web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(…)`, che configura l'API Web per accettare token dall'endpoint 2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
		var tvps = new TokenValidationParameters
		{
				// In this app, the TodoListClient and TodoListService
				// are represented using the same Application Id - we use
				// the Application Id to represent the audience, or the
				// intended recipient of tokens.

				ValidAudience = clientId,

				// In a real applicaiton, you might use issuer validation to
				// verify that the user's organization (if applicable) has
				// signed up for the app.  Here, we'll just turn it off.

				ValidateIssuer = false,
		};

		// Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
		// The options provided here tell the middleware about the type of tokens
		// that will be recieved, which are JWTs for the v2.0 endpoint.

		// NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
		// metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
		// OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
		// metadata document.

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{
				AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
		});
}
```

-	È ora possibile usare gli attributi `[Authorize]` per proteggere i controller e le azioni con l'autenticazione della connessione OAuth 2.0. Decorare la classe `Controllers\TodoListController.cs` con un tag di autorizzazione. Ciò forzerà l'utente a eseguire l'accesso prima di accedere alla pagina.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quando un chiamante autorizzato riesce a chiamare una delle API `TodoListController`, l'azione potrebbe richiedere l'accesso alle informazioni relative al chiamante. OWIN fornisce l'accesso alle attestazioni all'interno del token di connessione tramite l'oggetto `ClaimsPrincpal`.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-	Infine aprire il file `web.config` nella radice del progetto TodoListService e immettere i valori di configurazione nella sezione `<appSettings>`.
  -	`ida:Audience` rappresenta l'**ID applicazione** dell'app immesso nel portale.

## 3\. Configurare un'app client ed eseguire il servizio
Prima di poter vedere in azione il servizio To Do List, è necessario configurare il client To Do List in modo che possa ricevere i token dall'endpoint 2.0 ed eseguire chiamate al servizio.

- Nel progetto client To Do List aprire `App.config` e immettere i valori di configurazione nella sezione `<appSettings>`.
  -	ID applicazione `ida:ClientId` copiato dal portale.
	- `ida:RedirectUri` rappresenta l'**URI di reindirizzamento** dal portale.

Infine pulire, compilare ed eseguire ogni progetto. È ora disponibile un'API Web .NET MVC che accetta token da account Microsoft personali, aziendali e dell'istituto di istruzione. Accedere al client To Do List e chiamare l'API Web in modo che aggiunga attività all'elenco di attività da svolgere dell'utente.

Come riferimento [è disponibile qui l'esempio completato (senza i valori di configurazione) in un file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## Passaggi successivi
È ora possibile passare ad altri argomenti. È possibile consultare:

[Chiamata di un'API Web da un'app Web con Modello app 2.0 >>](active-directory-devquickstarts-webapp-webapi-dotnet.md)

Per altre risorse consultare: - [l'anteprima di Modello app 2.0 >>](active-directory-appmodel-v2-overview.md) - [il tag "azure-active directory" StackOverflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!----HONumber=August15_HO7-->