<properties
	pageTitle="Anteprima di Azure Active Directory B2C | Microsoft Azure"
	description="Come creare un'applicazione desktop di Windows con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente usando Azure Active Directory B2C."
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

# Anteprima di Azure AD B2C: Creare un'app desktop di Windows

<br>
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Azure Active Directory (Azure AD) B2C consente di aggiungere funzionalità avanzate di gestione delle identità self-service all'app desktop in pochi brevi passaggi. Questo articolo descrive come creare un'app Windows Presentation Foundation (WPF) .NET "To do list" con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente. L'app includerà il supporto per l'iscrizione e l'accesso tramite un nome utente o un indirizzo di posta elettronica. L'app includerà anche il supporto per l'iscrizione e l'accesso tramite account di social networking quali Facebook e Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire in questa guida.

## Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un **client nativo** nell'applicazione.
- Copiare l'**URI di reindirizzamento** `urn:ietf:wg:oauth:2.0:oob`. Si tratta dell'URL predefinito per questo esempio di codice.
- Copiare l'**ID applicazione** assegnato all'app. Sarà necessario più avanti.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md) specifici. Questo esempio di codice contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. È necessario creare i criteri per ogni tipo, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere l'opzione per l'**iscrizione tramite ID utente** o per l'**iscrizione tramite indirizzo di posta elettronica** nel pannello dei provider di identità.
- Scegliere **Nome visualizzato** e altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere le attestazioni **Nome visualizzato** e **ID oggetto** come attestazioni dell'applicazione per tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **nome** di ogni criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`. I nomi dei criteri saranno necessari in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

## Scaricare il codice

Il codice per questa esercitazione [è disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Per creare l'esempio passo dopo passo, è possibile [scaricare un progetto struttura come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

L'app completata è anche [disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare. La soluzione contiene due progetti: un progetto `TaskClient` e un progetto `TaskService`. `TaskClient` è l'applicazione desktop WPF con cui l'utente interagisce. `TaskService` è l'API Web back-end dell'app in cui sono archiviati gli elenchi attività di ogni utente. In questo caso i progetti `TaskClient` e `TaskService` sono rappresentati da un unico ID applicazione, perché includono un'app per la logica.

## Configurare il servizio dell’attività

Quando `TaskService` riceve una richiesta da `TaskClient`, verifica la presenza di un token di accesso valido per autenticare la richiesta. Per convalidare il token di accesso, è necessario fornire a `TaskService` alcune informazioni sull'app. Nel progetto `TaskService` aprire il file `web.config` nella radice del progetto e sostituire i valori nella sezione `<appSettings>`:

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

Per informazioni sull'autenticazione sicura delle richieste da parte di un'API Web tramite Azure AD B2C, consultare l'[articolo di introduzione all'API Web](active-directory-b2c-devquickstarts-api-dotnet.md).

## Eseguire i criteri
Ora che `TaskService` è pronto ad autenticare le richieste, è possibile implementare `TaskClient`. L'app comunica con Azure AD B2C inviando richieste di autenticazione HTTP. Queste richieste specificano i criteri da eseguire come parte della richiesta. Per le applicazioni desktop .NET, usare Active Directory Authentication Library (ADAL) per inviare messaggi di autenticazione OAuth 2.0, eseguire i criteri e ottenere token per la chiamata delle API Web.

### Installare ADAL
Aggiungere ADAL al progetto `TaskClient` usando la console di Gestione pacchetti di Visual Studio.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

### Immettere le informazioni B2C
Aprire il file `Globals.cs` e sostituire i valori della proprietà con i propri. Questa classe viene usata in tutto il progetto `TaskClient` per fare riferimento ai valori usati comunemente.

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### Creare una classe AuthenticationContext
La classe principale della libreria ADAL è `AuthenticationContext`, che rappresenta la connessione dell'app alla directory B2C. All'avvio dell'app, creare un'istanza di `AuthenticationContext` in `MainWindow.xaml.cs`. Questa istanza può essere usata in tutta la finestra.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default. Because we want tokens to persist when the user closes the app,
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

### Avvia un flusso di registrazione
Quando l'utente sceglie di iscriversi, avviare un flusso di iscrizione che usa i criteri di iscrizione creati. Usando ADAL, è sufficiente chiamare `authContext.AcquireTokenAsync(...)`. I parametri passati a `AcquireTokenAsync(...)` determinano quale token si riceve, il criterio usato nella richiesta di autenticazione e altro ancora.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that you want a token to our own back-end API.
		// Use the PromptBehavior. Always flag to indicate to ADAL that it should show a sign-up UI, no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;

		// When the request completes successfully, you can get user information from AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign-up successfully completes, display the user's to-do list
		GetTodoList();
	}

	// Handle any exemptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
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

### Avvia un flusso di accesso
È possibile avviare un flusso di accesso allo stesso modo in cui si avvia un flusso di iscrizione. Quando l'utente accede, eseguire la stessa chiamata ad ADAL, questa volta usando i criteri di accesso:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...
```

### Avviare un flusso di modifica del profilo
Anche in questo caso è possibile eseguire i criteri di modifica del profilo in modo analogo:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

In tutti questi casi ADAL restituisce un token in `AuthenticationResult` oppure genera un'eccezione. Ogni volta che si ottiene un token da ADAL, è possibile utilizzare l’oggetto `AuthenticationResult.UserInfo` per aggiornare i dati utente nell'app, ad esempio l'interfaccia utente. ADAL inoltre memorizza nella cache il token per l'uso in altre parti dell'applicazione.

## Chiamare le API
Finora si è usato ADAL per eseguire i criteri e ottenere i token. In molti casi, tuttavia, è necessario cercare un token memorizzato nella cache esistente senza eseguire alcun criterio. Un esempio è rappresentato dallo scenario in cui l'app tenta di recuperare l'elenco attività dell'utente da `TaskService`. A tale scopo è possibile usare lo stesso metodo `authContext.AcquireTokenAsync(...)`, anche in questo caso con `clientId` come parametro di ambito, ma questa volta usando anche `PromptBehavior.Never`:

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token
		// could not be acquired from the cache, rather than automatically prompt the user to sign in.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);

	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign in.
		if (ex.ErrorCode == "user_interaction_required")
		{
			MessageBox.Show("Please sign up or sign in first");
			SignInButton.Visibility = Visibility.Visible;
			SignUpButton.Visibility = Visibility.Visible;
			EditProfileButton.Visibility = Visibility.Collapsed;
			SignOutButton.Visibility = Visibility.Collapsed;
			UsernameLabel.Content = string.Empty;

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
	...
```

Quando la chiamata a `AcquireTokenAsync(...)` ha esito positivo e viene trovato un token nella cache, è possibile aggiungere il token all'intestazione `Authorization` della richiesta HTTP. In questo modo `TaskService` è in grado di autenticare la richiesta di lettura dell'elenco attività dell'utente:

```C#
	...
	// After the token has been returned by ADAL, add it to the HTTP authorization header before the call is made to TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the to-do-list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
```

È possibile usare questo modello ogni volta che si vuole controllare la cache dei token per i token senza chiedere conferma all'utente per accedere. All'avvio dell'app, ad esempio, è possibile cercare i token esistenti in `FileCache`. In questo modo la sessione di accesso dell'utente viene mantenuta a ogni esecuzione dell'app. È possibile visualizzare lo stesso codice nell'evento `OnInitialized` di `MainWindow`. `OnInitialized` gestisce il caso della prima esecuzione.

## Disconnettere l'utente
È possibile usare la libreria ADAL per terminare la sessione dell'utente nell'app quando l'utente seleziona **Disconnetti**. Usando ADAL, questa operazione viene eseguita cancellando tutti i token dalla cache dei token:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses. It is not part of ADAL.
	ClearCookies();

	// Update the UI to show the user as signed out.
	TaskList.ItemsSource = string.Empty;
	SignInButton.Visibility = Visibility.Visible;
	SignUpButton.Visibility = Visibility.Visible;
	EditProfileButton.Visibility = Visibility.Collapsed;
	SignOutButton.Visibility = Visibility.Collapsed;
	return;
}
```

## Eseguire l'app di esempio

Infine compilare ed eseguire `TaskClient` e `TaskService`. Effettuare l'iscrizione all'app usando un indirizzo di posta elettronica o un nome utente. Disconnettersi e accedere nuovamente con lo stesso account utente. Modificare il profilo dell'utente. Disconnettersi ed eseguire l'iscrizione usando un account utente diverso.

## Aggiungere i provider di identità per i social network

Attualmente l'app supporta solo l'iscrizione e l'accesso dell'utente tramite **account locali**. Si tratta di account archiviati nella directory B2C che usano un nome utente e una password. Usando Azure AD B2C, è possibile aggiungere il supporto per altri provider di identità (IDP) senza modificare il codice.

Per aggiungere provider di identità per i social media all'applicazione, seguire le istruzioni dettagliate fornite in questi articoli. Per ogni provider di identità che si vuole supportare, è necessario registrare un'applicazione nel relativo sistema e ottenere un ID client.

- [Configurare Facebook come provider di identità](active-directory-b2c-setup-fb-app.md)
- [Configurare Google come provider di identità](active-directory-b2c-setup-goog-app.md)
- [Configurare Amazon come provider di identità](active-directory-b2c-setup-amzn-app.md)
- [Configurare LinkedIn come provider di identità](active-directory-b2c-setup-li-app.md)

Dopo aver aggiunto i provider di identità alla propria directory B2C, è necessario modificare ognuno dei tre criteri per includere i nuovi provider di identità, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md). Dopo aver salvato i criteri, eseguire nuovamente l'app. I nuovi provider di identità dovrebbero essere stati aggiunti tra le opzioni di accesso e iscrizione in ognuna delle esperienze per l'identità.

Provare a usare i criteri e osservare gli effetti sull'app di esempio. Aggiungere o rimuovere provider di identità, manipolare le attestazioni dell'applicazione o modificare gli attributi per l'iscrizione. Fare delle prove fino a quando non è chiaro il modo in cui i criteri, le richieste di autenticazione e ADAL sono collegati tra loro.

Come riferimento, l'esempio completo [è disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). È anche possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->