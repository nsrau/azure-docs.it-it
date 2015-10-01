<properties
	pageTitle="Anteprima di AD B2C di Azure | Microsoft Azure"
	description="Come creare un'applicazione desktop di Windows con esperienze di accesso, iscrizione e gestione del profilo tramite Azure AD B2C."
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
	ms.date="09/04/2015"
	ms.author="dastrock"/>

# Anteprima B2C AD Azure: costruire un'app desktop di Windows

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Azure AD B2C consente di aggiungere potenti funzionalità di gestione delle identità self-service all'app del desktop con pochi brevi passaggi. Questo articolo descrive come creare un'app Web WPF .NET con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente. Include anche il supporto per l'iscrizione e l'accesso con un nome utente o un indirizzo di posta elettronica o con gli account dei social network, ad esempio Facebook e Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, passare a [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

## 2\. Creare un'applicazione

A questo punto, è necessario creare un'app nella directory B2C, che fornisce ad Azure AD alcune informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md): Assicurarsi di

- Includere un **native client** nell'applicazione
- Copiare l’**Uri di reindirizzamento** `urn:ietf:wg:oauth:2.0:oob` -è l'URL predefinito per questo esempio di codice.
- Copiare l'**ID applicazione** assegnato all'app. perché verrà richiesto a breve.

## 3\. Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [**criteri**](active-directory-b2c-reference-policies.md) specifici. Questo esempio di codice contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. Sarà necessario creare i criteri per ogni tipo, come descritto nell'articolo che illustra il [riferimento ai criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere **Iscrizione tramite ID utente** o **Iscrizione tramite indirizzo di posta elettronica** nel pannello dei provider di identità.
- Scegliere il **Nome visualizzato** e alcuni altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere le attestazioni **Nome visualizzato** e **ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altri criteri.
- Copiare il **Nome** dei criteri dopo averli creati. Dovrebbero mostrare il prefisso `b2c_1_`. Sarà necessario usare i nomi dei criteri a breve. 

Dopo aver creato i tre criteri, è possibile passare alla creazione dell'app.

## 4\. Scaricare il codice

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Per creare l'esempio passo passo, è possibile [scaricare un progetto scheletro come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip) o clonare lo scheletro:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

L'app completata è anche [disponibile come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, per iniziare aprire il file `.sln` in Visual Studio. Osservare che nella soluzione sono presenti due progetti: un progetto `TaskClient` e un progetto `TaskService`. `TaskClient` è l'applicazione desktop WPF con cui interagisce l'utente. `TaskService` è l'API Web back-end dell'app che archivia le applicazioni "To-Do List" degli utenti. Sia il `TaskClient` che `TaskService` saranno rappresentati da una singola **ID applicazione** in questo caso, poiché entrambi includono un’applicazione per la logica.

## 5\. Configurare il servizio dell’attività

Quando `TaskService` riceve le richiesta da `TaskClient`, verifica la presenza di un token di accesso valido per autenticare la richiesta. Per convalidare il token di accesso, è necessario fornire a `TaskService` alcune informazioni sull'app. Nel progetto `TaskService` aprire il file `web.config` nella radice del progetto e sostituire i valori nella sezione `<appSettings>`:

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

If you want to learn how a web API securely authenticates requests using Azure AD B2C, check out our
[Web API Getting Started article](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Execute policies
Now that the `TaskService` is ready to authenticate requests, we can implement the `TaskClient`.  Your app communicates with Azure AD B2C by sending HTTP authentication requests,
specifying the policy it wishes to execute as part of the request.  For .NET desktop applications, you can use the **Active Directory Authentication Library (ADAL)**
to send OAuth 2.0 authentication messages, execute policies, and get tokens for calling web APIs.

#### Install ADAL
Begin by adding ADAL to the TaskClient project using the Visual Studio Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

#### Immettere le informazioni B2C
Aprire il file `Globals.cs` e sostituire i valori di proprietà con i propri. Questa classe viene utilizzata in tutto il `TaskClient` per fare riferimento ai valori utilizzati comunemente.

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


#### Crea un AuthenticationContext
La classe principale della libreria ADAL è `AuthenticationContext` -rappresenta la connessione dell'app con il servizio directory B2C. All'avvio dell'app, creare un'istanza di `AuthenticationContext` nel `MainWindow.xaml.cs`, che può essere utilizzato in tutta la finestra.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

#### Avvia un flusso di registrazione
Quando l'utente fa clic sul pulsante per l'abbonamento, si desidera avviare un flusso di registrazione utilizzando i criteri per l'abbonamento che sono stati creati. Con ADAL, è sufficiente chiamare `authContext.AcquireTokenAsync(...)`. I parametri passati a `AcquireTokenAsync(...)` determinano quali token si ricevono, il criterio utilizzato nella richiesta di autenticazione e così via.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that we want a token to the our own backend API
		// Use the PromptBehavior.Always flag to indicate to ADAL that it should show a sign-up UI no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;
		
		// When the request completes successfully, you can get user information form the AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign up successfully completes, display the user's To-Do List
		GetTodoList();
	}
	
	// Handle any exeptions that occurred during execution of the policy.
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

#### Avvia un flusso di accesso
Un flusso di accesso può essere avviato esattamente come il flusso di registrazione. Quando l'utente fa clic sul pulsante Accedi, fa la stessa chiamata a ADAL, questa volta utilizzando i criteri di accesso:

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

#### Avvia un flusso di modifica profilo
Anche in questo caso, è possibile eseguire il criterio di modifica del profilo nello stesso modo:

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

In tutti questi casi, ADAL restituirà un token nella relativa `AuthenticationResult` o genererà un'eccezione. Ogni volta che si ottiene un token da ADAL, è possibile utilizzare l’oggetto `AuthenticationResult.UserInfo` per aggiornare i dati utente nell'app, ad esempio l'interfaccia utente. ADAL inoltre memorizza nella cache il token, per l'utilizzo in altre parti dell'applicazione.

## 7\. Chiamare le API
È già stato utilizzato ADAL per eseguire i criteri e ottenere token. In molti casi, tuttavia, si desidererà cercare un token memorizzato nella cache esistente senza eseguire alcun criterio. Un esempio è quando l'applicazione tenta di recuperare l'elenco di attività dell'utente da `TaskService`. È possibile utilizzare lo stesso metodo `authContext.AcquireTokenAsync(...)` a tale scopo, ancora una volta utilizzando `clientId` come parametro di ambito, ma questa volta utilizzando `PromptBehavior.Never`:

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
		// could not be acquired from the cache, rather than automatically prompting the user to sign in. 
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);
	
	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign-in.
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

Quando la chiamata a `AcquireTokenAsync(...)` ha esito positivo e un token viene trovato nella cache, è possibile aggiungere il token per l’intestazione `Authorization` della richiesta HTTP in modo che `TaskService` sia in grado di autenticare la richiesta di lettura dell'elenco attività dell'utente:

```C#
	...
	// Once the token has been returned by ADAL, add it to the http authorization header, before making the call to the TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the To Do list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
``` 

È possibile utilizzare questo stesso modello ogni volta che si desidera controllare la cache dei token per i token senza chiedere conferma all'utente per accedere. Ad esempio - all'avvio dell'applicazione, si desidera controllare `FileCache` per tutti i token esistenti, in modo che venga mantenuta la sessione di accesso dell'utente ogni volta che si esegue la app. È possibile visualizzare lo stesso codice nell’evento `OnInitialized`di `MainWindow`, che gestisce il caso della prima esecuzione.

## 8\. Disconnettere l'utente
Infine, è possibile utilizzare la libreria ADAL per terminare la sessione dell'utente con l'app quando l'utente fa clic sul pulsante "Esci". Con ADAL, è semplice come la cancellazione di tutti i token dalla cache dei token:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses, it is not part of ADAL.
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

## 9\. Eseguire l'app di esempio

Infine, compilare ed eseguire sia `TaskClient` che `TaskService`. Eseguire l'iscrizione per l'app usando un indirizzo di posta elettronica o un nome utente. Disconnettersi e accedere nuovamente con lo stesso account utente. Modificare il profilo dell'utente. Disconnettersi ed eseguire l'iscrizione usando un account utente diverso.

## 10\. Aggiungere i provider di identità per i social network

Attualmente, l'app supporta solo l'iscrizione e l'accesso con gli account denominati **account locali**, ovvero account archiviati nella directory B2C con un nome utente e una password. Con Azure AD B2C, è possibile aggiungere il supporto per altri **provider di identità**, o IdP, senza modificare il codice.

Per aggiungere provider di identità per i social network all'applicazione, seguire le istruzioni dettagliate fornite in uno di questi due articoli. Per ogni provider di identità che si vuole supportare, sarà necessario registrare un'applicazione nel sistema del provider e ottenere un ID client.

- [Configurare Facebook come provider di identità](active-directory-b2c-setup-fb-app.md)
- [Configurare Google come provider di identità](active-directory-b2c-setup-goog-app.md)
- [Configurare Amazon come provider di identità](active-directory-b2c-setup-amzn-app.md)
- [Configurare LinkedIn come provider di identità](active-directory-b2c-setup-li-app.md) 

Dopo aver aggiunto i provider di identità alla propria directory B2C, sarà necessario tornare indietro e modificare ognuno dei tre criteri per includere i nuovi provider di identità, come descritto nell'articolo di [riferimento ai criteri](active-directory-b2c-reference-policies.md). Dopo aver salvato i criteri, eseguire di nuovo l'app. I nuovi provider di identità dovrebbero essere stati aggiunti tra le opzioni di accesso e iscrizione in ognuna delle esperienze per l'identità.

È possibile sperimentare liberamente i criteri e osservare gli effetti generati nell'app, ad esempio aggiungere o rimuovere i provider di identità, modificare le attestazioni dell'applicazione o modificare gli attributi dell'iscrizione. Fare delle prove fino a quando non è chiaro il modo in cui criteri, richieste di autenticazione e ADAL sono collegati tra loro.

Come riferimento, l'esempio completato [è disponibile in un file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!----HONumber=Sept15_HO3-->