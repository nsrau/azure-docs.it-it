<properties
	pageTitle="App nativa di .NET v2.0 di Azure AD | Microsoft Azure"
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
	ms.date="05/31/2016"
	ms.author="dastrock"/>

# Aggiungere l'accesso a un'applicazione Desktop di Windows

Con l'endpoint v2.0 è possibile aggiungere rapidamente l'autenticazione alle app desktop con supporto per account Microsoft personali, aziendali o dell'istituto di istruzione. Consente inoltre all'app di comunicare in modo sicuro con un'API Web di back-end e con [Microsoft Graph](https://graph.microsoft.io) e alcune API unificate di [Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE]
	Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).

Per le [app .NET native in esecuzione su un dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD offre Active Directory Authentication Library o ADAL. L'unico scopo di ADAL è consentire all'app di ottenere facilmente i token per le chiamate ai servizi Web. Per dimostrare la semplicità di questa operazione, in questo esempio viene creata un'app .NET WPF To Do List che:

-	Consente agli utenti di accedere e ottiene i token di accesso usando il [protocollo di autenticazione OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
-	Chiama in modo sicuro un servizio Web To Do List di back-end, anch'esso protetto da OAuth 2.0.
-	Disconnette gli utenti.

## Scaricare il codice di esempio

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet). Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

Al termine dell'esercitazione, verrà fornita anche l'app completata.

## Registrare un'app
Creare una nuova app in [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o seguire questa [procedura dettagliata](active-directory-v2-app-registration.md). Verificare di:

- Copiare l'**ID applicazione** assegnato all'app, perché verrà richiesto a breve.
- Aggiungere la piattaforma **Mobile** per l'app.
- Annotare il **Redirect URI** dal portale. È necessario usare il valore predefinito `urn:ietf:wg:oauth:2.0:oob`.

## Installare e configurare ADAL
A questo punto si dispone di un'app registrata in Microsoft ed è possibile installare ADAL e trascrivere il codice correlato all'identità. Affinché ADAL riesca a comunicare con l'endpoint v2.0, è necessario fornirlo insieme ad alcune informazioni relative alla registrazione dell'app.

-	Innanzitutto, aggiungere ADAL al progetto TodoListClient usando la console di Gestione pacchetti.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease
```

-	Nel progetto TodoListClient aprire `app.config`. Sostituire i valori degli elementi nella sezione `<appSettings>` in modo che corrispondano ai valori inseriti nel portale di registrazione dell'app. Il codice farà riferimento a questi valori ogni volta che userà ADAL.
    -	`ida:ClientId` rappresenta l'**ID applicazione** dell'app copiato dal portale.
    -	`ida:RedirectUri` rappresenta l'**URI di reindirizzamento** dal portale.
- Nel progetto TodoList-Service aprire `web.config` nella radice del progetto.  
    - Sostituire il valore `ida:Audience` con lo stesso **ID applicazione** del portale.

## Usare ADAL per ottenere token
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
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
		// If the user clicked the 'clear cache' button,
		// clear the ADAL token cache and show the user as signed out.
		// It's also necessary to clear the cookies from the browser
		// control so the next user has a chance to sign in.

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

## Esegui

Congratulazioni. A questo punto si dispone di un'app WPF .NET funzionante, la quale consente di autenticare gli utenti e di effettuare chiamate sicure delle API Web utilizzando Oauth 2.0. Eseguire entrambi i progetti e accedere con un account Microsoft personale oppure con un account aziendale o dell'istituto di istruzione. Aggiungere le attività all'elenco To-Do dell'utente. Disconnettersi e ripetere l'accesso come utente differente per visualizzare l'elenco To-Do. Chiudere l'app e rieseguirla. La sessione dell'utente non subisce modifiche, poiché l'app memorizza i token in un file locale.

ADAL facilita l'incorporazione delle funzionalità di identità comuni all'interno dell'app usando sia account personali che aziendali. Esegue automaticamente le attività più complesse: gestione della cache, supporto del protocollo OAuth, presentazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti e altro. Tutto ciò che occorre conoscere è una sola chiamata all'API, `authContext.AcquireTokenAsync(...)`.

Come riferimento, l'esempio completato (senza i valori di configurazione) [è disponibile in un file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Passaggi successivi

Ora è possibile passare ad argomenti più avanzati. È possibile:

- [Protezione dell'API Web TodoListService con l'endpoint v2.0 >>](active-directory-v2-devquickstarts-dotnet-api.md)

Per altre risorse, vedere:
- [Guida per sviluppatori v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow, tag "adal" >>](http://stackoverflow.com/questions/tagged/adal)

## Ottenere aggiornamenti della sicurezza per i prodotti

È consigliabile ricevere notifiche in caso di problemi di sicurezza. A tale scopo, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e sottoscrivere gli avvisi di sicurezza.

<!---HONumber=AcomDC_0608_2016-->