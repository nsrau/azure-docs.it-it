<properties
	pageTitle="Modello app 2.0 | Microsoft Azure"
	description="Come creare un'app .NET nativa che consente agli utenti di accedere con un account Microsoft personale, aziendale e dell'istituto di istruzione."
	services="active-directory"
	documentationCenter=""
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

# Anteprima di Modello app 2.0: Aggiungere l'accesso a un'app desktop di Windows

Con Modello app 2.0 è possibile aggiungere rapidamente l'autenticazione alle app desktop con supporto per account Microsoft personali, aziendali e dell'istituto di istruzione. Consente inoltre all'app di comunicare in modo sicuro con un'API Web di back-end, oltre che con alcune [API unificate di Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

Per le [app .NET native in esecuzione su un dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD offre Active Directory Authentication Library o ADAL. L'unico scopo di ADAL è consentire all'app di ottenere facilmente i token per le chiamate ai servizi Web. Per dimostrare la semplicità di questa operazione, in questo esempio viene creata un'app .NET WPF To Do List che:

-	Consente agli utenti di accedere e ottiene i token di accesso usando il [protocollo di autenticazione OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
-	Chiama in modo sicuro un servizio Web To Do List di back-end, anch'esso protetto da OAuth 2.0.
-	Disconnette gli utenti.

Per creare l'app funzionante completa, sarà necessario:

2. Registrare l'app.
3. Installare e configurare ADAL.
5. Usare ADAL per ottenere i token da Azure AD.

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet). Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

The completed app is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Mobile** platform for your app.
- Copy down the **Redirect URI** from the portal. You must use the default value of `urn:ietf:wg:oauth:2.0:oob`.

## 2. Install & Configure ADAL
Now that you have an app registered with Microsoft, you can install ADAL and write your identity-related code.  In order for ADAL to be able to communicate the v2.0 endpoint, you need to provide it with some information about your app registration.

-	Begin by adding ADAL to the TodoListClient project using the Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease 
```

-	Nel progetto TodoListClient aprire `app.config`. Sostituire i valori degli elementi nella sezione `<appSettings>` in modo che corrispondano ai valori inseriti nel portale di registrazione dell'app. Il codice farà riferimento a questi valori ogni volta che userà ADAL.
    -	`ida:ClientId` rappresenta l'**ID applicazione** dell'app copiato dal portale.
    -	`ida:RedirectUri` rappresenta l'**URI di reindirizzamento** dal portale.
- Nel progetto TodoList-Service aprire `web.config` nella radice del progetto.  
    - Sostituire il valore `ida:Audience` con lo stesso **ID applicazione** del portale.

## 3\. Usare ADAL per ottenere token
Il principio alla base di ADAL è che l'app, ogni volta che ha bisogno di un token di accesso, deve semplicemente chiamare `authContext.AcquireToken(...)` e ADAL fa il resto.

-	Nel progetto `TodoListClient` aprire `MainWindow.xaml.cs` e individuare il metodo `OnInitialized(...)`. Il primo passaggio consiste nell'inizializzare l'oggetto `AuthenticationContext` dell'app, ovvero la classe primaria di ADAL, dove si passano ad ADAL le coordinate di cui ha bisogno per comunicare con Azure AD e gli si indica come memorizzare i token nella cache.

```C#
protected override async void OnInitialized(EventArgs e)
{
		base.OnInitialized(e);

		authContext = new AuthenticationContext(authority, new FileCache());
		AuthenticationResult result = null;
		...
}
```

- All'avvio dell'app si intende quindi verificare e controllare se l'utente è già connesso all'app. Tuttavia, non si desidera ancora richiamare un'interfaccia utente di accesso. A questo scopo l'utente deve fare clic su "Accedi". Nel metodo `OnInitialized(...)`:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from ADAL, passing in the parameter
// PromptBehavior.Never.  This forces ADAL to throw an exception if it cannot
// get a token for the user without showing a UI.

try
{
		result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));

		// If we got here, a valid token is in the cache.  Proceed to
		// fetch the user's tasks from the TodoListService via the
		// GetTodoList() method.

		SignInButton.Content = "Clear Cache";
		GetTodoList();
}
catch (AdalException ex)
{
		if (ex.ErrorCode == "user_interaction_required")
		{
				// If user interaction is required, the app should take no action,
				// and simply show the user the sign in button.
		}
		else
		{
				// Here, we catch all other AdalExceptions

				string message = ex.Message;
				if (ex.InnerException != null)
				{
						message += "Inner Exception : " + ex.InnerException.Message;
				}
				MessageBox.Show(message);
		}
}
```

- Se l'utente non è connesso e fa clic sul pulsante "Accedi", si intende richiamare un'interfaccia utente di accesso e richiedere all'utente di immettere le credenziali. Implementare il gestore del pulsante di accesso:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
		// TODO: Sign the user out if they clicked the "Clear Cache" button

		// If the user clicked the 'Sign-In' button, force
		// ADAL to prompt the user for credentials by specifying
		// PromptBehavior.Always.  ADAL will get a token for the
		// TodoListService and cache it for you.

		AuthenticationResult result = null;
		try
		{
				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Always, null));
				SignInButton.Content = "Clear Cache";
				GetTodoList();
		}
		catch (AdalException ex)
		{
				// If ADAL cannot get a token, it will throw an exception.
				// If the user canceled the login, it will result in the
				// error code 'authentication_canceled'.

				if (ex.ErrorCode == "authentication_canceled")
				{
						MessageBox.Show("Sign in was canceled by the user");
				}
				else
				{
						// An unexpected error occurred.
						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}

						MessageBox.Show(message);
				}

				return;
		}

}
```

- Se l'utente accede correttamente, ADAL riceve e memorizza nella cache un token per l'utente ed è possibile procedere in tutta sicurezza alla chiamata al metodo `GetTodoList()`. Per ottenere le attività dell'utente è sufficiente implementare il metodo `GetTodoList()`.

```C#
private async void GetTodoList()
{
		AuthenticationResult result = null;
		try
		{
				// Here, we try to get an access token to call the TodoListService
				// without invoking any UI prompt.  PromptBehavior.Never forces
				// ADAL to throw an exception if it cannot get a token silently.

				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));
		}
		catch (AdalException ex)
		{
				// ADAL couldn't get a token silently, so show the user a message
				// and let them click the Sign-In button.

				if (ex.ErrorCode == "user_interaction_required")
				{
						MessageBox.Show("Please sign in first");
						SignInButton.Content = "Sign In";
				}
				else
				{
						// In any other case, an unexpected error occurred.

						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}
						MessageBox.Show(message);
				}

				return;
		}

		// Once the token has been returned by ADAL,
    // add it to the http authorization header,
    // before making the call to access the To Do list service.

    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

		...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null) { // If the user clicked the 'clear cache' button, // clear the ADAL token cache and show the user as signed out. // It's also necessary to clear the cookies from the browser // control so the next user has a chance to sign in.

		if (SignInButton.Content.ToString() == "Clear Cache")
		{
				TodoList.ItemsSource = string.Empty;
				authContext.TokenCache.Clear();
				ClearCookies();
				SignInButton.Content = "Sign In";
				return;
		}

		...
```

Congratulations! You now have a working .NET WPF app that has the ability to authenticate users & securely call Web APIs using OAuth 2.0.  Run your both projects, and sign in with either a personal Microsoft account or a work or school account.  Add tasks to that user's To-Do list.  Sign out, and sign back in as another user to view their To-Do list.  Close the app, and re-run it.  Notice how the user's session remains intact - that is becuase the app caches tokens in a local file.

ADAL makes it easy to incorporate common identity features into your app, using both personal and work accounts.  It takes care of all the dirty work for you - cache management, OAuth protocol support, presenting the user with a login UI, refreshing expired tokens, and more.  All you really need to know is a single API call, `authContext.AcquireTokenAsync(...)`.

For reference, the completed sample (without your configuration values) [is provided as a .zip here](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), or you can clone it from GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Passaggi successivi

Ora è possibile passare ad argomenti più avanzati. È possibile consultare:

- [Protezione dell'API Web TodoListService con Modello app 2.0 >>](active-directory-v2-devquickstarts-dotnet-api.md)

Per altre risorse consultare: - [l'anteprima di Modello app 2.0 >>](active-directory-appmodel-v2-overview.md) - [il tag "adal" StackOverflow >>](http://stackoverflow.com/questions/tagged/adal)

<!----HONumber=August15_HO7-->