<properties
	pageTitle="Applicazione Web di .NET di modello app v 2.0 | Microsoft Azure"
	description="Come creare un'app Web .NET MVC che chiama servizi Web usando account Microsoft personali, aziendali e dell'istituto di istruzione per l'accesso."
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
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# Anteprima di Modello app 2.0: Chiamata di un'API Web da un'app Web .NET

> [AZURE.NOTE]
	Queste informazioni fanno riferimento all'anteprima pubblica dell'endpoint 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

Con Modello app 2.0 è possibile aggiungere rapidamente l'autenticazione alle app e alle API Web con supporto per account Microsoft personali, aziendali e dell'istituto di istruzione. In questo esempio verrà creata un'app Web MVC che:

- Consente agli utenti di accedere tramite OpenID Connect e con l'aiuto del middleware OWIN di Microsoft.
- Ottiene il token di accesso OAuth 2.0 per un'API Web usando ADAL.
- Crea, legge ed elimina gli elementi di un elenco di attività dell'utente ospitato sull'API Web e protetto da OAuth 2.0.

Questa esercitazione fa riferimento principalmente sul recupero e l'uso di token di accesso in un'app Web, descritta dettagliatamente [qui](active-directory-v2-flows.md#web-apps). Come prerequisiti, è consigliabile apprendere come [aggiungere l'accesso base a un'app Web](active-directory-v2-devquickstarts-dotnet-web.md) o come [proteggere correttamente un'API Web](active-directory-v2-devquickstarts-dotnet-api.md).

I passaggi di base per chiamare l'API Web To Do List dal client sono i seguenti:

1. Registrare un'app
2. Far accedere l'utente all'app Web usando OpenID Connect
3. Usare ADAL per ottenere un token di accesso al momento dell'accesso dell'utente
4. Chiamare l'API Web To Do List con un token di accesso

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).

Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

In alternativa, è possibile [scaricare l'app completata come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) oppure clonare l'app completata:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## 1. Registrare un'app
Creare una nuova app in [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o seguire questa [procedura dettagliata](active-directory-v2-app-registration.md). Verificare di:

- Copiare l'**ID applicazione** assegnato all'app, perché verrà richiesto a breve.
- Creare una **Chiave privata app** di tipo **Password** e copiare il relativo valore per usarlo in seguito.
- Aggiungere la piattaforma **Web** per l'app.
- Immettere l'**URI di reindirizzamento** corretto. L'URI di reindirizzamento indica ad Azure AD dove indirizzare le risposte di autenticazione. Il valore predefinito per questa esercitazione è `https://localhost:44326/`.


## 2. Far accedere l'utente con OpenID Connect
In questo caso, il middleware OWIN verrà configurato per l'uso del [protocollo di autenticazione OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). OWIN verrà usato, tra le altre cose, per inviare le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente.

-	Per iniziare, aprire il file `web.config` nella radice del progetto `TodoList-WebApp` e immettere i valori di configurazione dell'app nella sezione `<appSettings>`.
    -	`ida:ClientId` rappresenta l'**ID applicazione** assegnato all'app nel portale di registrazione.
	- `ida:ClientSecret` rappresenta la **chiave privata app** creata nel portale di registrazione.
    -	`ida:RedirectUri` rappresenta l'**URI di reindirizzamento** immesso nel portale.
- Aprire il file `web.config` nella radice del progetto `TodoList-Service` e sostituire `ida:Audience` con lo stesso **ID applicazione** di cui sopra.


-	A questo punto aggiungere i pacchetti NuGet del middleware OWIN al progetto `TodoList-WebApp` tramite la console di Gestione pacchetti.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

-	Aprire il file `App_Start\Startup.Auth.cs` e aggiungere istruzioni `using` per le librerie sopra indicate.
- Nello stesso file, implementare il metodo `ConfigureAuth(...)`. I parametri forniti in `OpenIDConnectAuthenticationOptions` fungeranno da coordinate per consentire all'app di comunicare con Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

					// The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
					// The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
					// In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

					ClientId = clientId,
					Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
					Scope = "openid email profile offline_access",
					RedirectUri = redirectUri,
					PostLogoutRedirectUri = redirectUri,
					TokenValidationParameters = new TokenValidationParameters
					{
						ValidateIssuer = false,
					},

					// The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

					Notifications = new OpenIdConnectAuthenticationNotifications
					{
						AuthenticationFailed = OnAuthenticationFailed,
						AuthorizationCodeReceived = OnAuthorizationCodeReceived,
					}

    	});
}
...
```

## 3\. Usare ADAL per ottenere un token di accesso al momento dell'accesso dell'utente
Nella notifica `AuthorizationCodeReceived` si desidera usare [OAuth 2.0 in parallelo con OpenID Connect](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) per riscattare l'authorization\_code per un token di accesso al servizio To Do List. ADAL può semplificare il processo:

- Per prima cosa installare la versione di anteprima di ADAL:

```PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoList-WebApp -IncludePrerelease``` 
- Aggiungere quindi un'altra istruzione `using` al file `App_Start\Startup.Auth.cs` per ADAL. 
- Aggiungere ora un nuovo metodo, il gestore dell'evento `OnAuthorizationCodeReceived`. Questo gestore userà ADAL per acquisire un token di accesso per l'API To Do List e archivierà il token nella cache dei token di ADAL per usi successivi:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
		string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
		string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
		ClientCredential cred = new ClientCredential(clientId, clientSecret);

		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		var authContext = new Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(authority, new NaiveSessionCache(userObjectId));
		var authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), cred, new string[] { clientId });
}
...
```

- Nelle app Web, ADAL dispone di una cache di token estendibile che può essere usata per archiviare i token. Questo esempio implementa `NaiveSessionCache` che usa lo spazio di archiviazione della sessione HTTP per la memorizzazione dei token nella cache.

<!-- TODO: Token Cache article -->


## 4. Chiamare l'API Web To Do List
È ora possibile usare il token di accesso acquisito al passaggio 3. Aprire il file `Controllers\TodoListController.cs` dell'app Web, che esegue tutte le richieste CRUD all'API To Do List.

- È possibile usare nuovamente ADAL per recuperare i token di accesso dalla cache ADAL. Per prima cosa aggiungere un'istruzione `using` per ADAL a questo file.

    `using Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory;`

- Nell'azione `Index` usare il metodo `AcquireTokenSilentAsync` di ADAL per ottenere un token di accesso da usare per leggere i dati dal servizio To Do List:

```C#
...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser);
...
```

- L'esempio aggiunge quindi il token risultante alla richiesta HTTP GET come intestazione `Authorization`, usata dal servizio To Do List per autenticare la richiesta.
- Se il servizio To Do List restituisce una risposta `401 Unauthorized`, significa che i token di accesso in ADAL per qualche motivo non sono più validi. In questo caso è necessario eliminare eventuali token di accesso dalla cache ADAL e far visualizzare all'utente un messaggio che comunica che potrebbe essere necessario eseguire un nuovo accesso. Questa operazione riavvierà il flusso di acquisizione dei token.

```C#
...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
		var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
		foreach (TokenCacheItem tci in todoTokens)
				authContext.TokenCache.DeleteItem(tci);

		return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
...
```

- Allo stesso modo, se ADAL non è in grado di restituire un token di accesso per qualsiasi motivo, è necessario chiedere all'utente di accedere nuovamente. Questo è semplice quanto individuare eventuali `AdalException`:

```C#
...
catch (AdalException ee)
{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
...
```

- La stessa identica chiamata `AcquireTokenSilentAsync` viene implementata nelle azioni `Create` e `Delete`. Nelle app Web è possibile usare questo metodo ADAL per ottenere token di accesso tutte le volte che sono necessari nell'app. ADAL si occupa dell'acquisizione, della memorizzazione nella cache e dell'aggiornamento dei token.

Infine compilare ed eseguire l'app. Accedere con un account Microsoft o con un account Azure AD e osservare che l'identità dell'utente sia visibile nella barra di spostamento superiore. Aggiungere ed eliminare alcuni elementi dall'elenco di attività dell'utente per vedere le chiamate API protette OAuth 2.0 in azione. Sono ora disponibili un'app e un'API Web protette che usano protocolli standard del settore in grado di autenticare gli utenti con account personali, aziendali e dell'istituto di istruzione.

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) [qui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).

## Passaggi successivi

Per altre risorse consultare: 
- [l'anteprima di Modello app 2.0 >>](active-directory-appmodel-v2-overview.md) 
- [il tag "adal" StackOverflow >>](http://stackoverflow.com/questions/tagged/adal)

<!---HONumber=AcomDC_0128_2016-->