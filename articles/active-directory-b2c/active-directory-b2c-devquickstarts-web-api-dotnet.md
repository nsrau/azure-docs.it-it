<properties
	pageTitle="Azure Active Directory B2C | Microsoft Azure"
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
	ms.date="07/22/2016"
	ms.author="dastrock"/>

# Azure AD B2C: Chiamare un'API Web da un'app Web .NET

Con Azure Active Directory (Azure AD) B2C è possibile aggiungere funzionalità avanzate di gestione delle identità self-service alle app Web e alle API Web in pochi brevi passaggi. Questo articolo descrive come creare un'app Web Model-View-Controller (MVC) .NET "To do list" che chiama un'API Web usando token di connessione.

Questo articolo non descrive come implementare le esperienze di accesso, iscrizione e gestione del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha già effettuato l'autenticazione. Se non è già stato fatto, iniziare con l'[esercitazione introduttiva per la compilazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) per acquisire nozioni di base su Azure AD B2C.

## Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e altro ancora. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire con questa guida.

## Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. In questo caso, sia l'app Web che l'API Web saranno rappresentate da un singolo **ID applicazione**, perché includono un'app per la logica. Per creare un'app, seguire [questa procedura](active-directory-b2c-app-registration.md): Assicurarsi di:

- Includere un'**app Web o un'API Web** nell'applicazione.
- Immettere `https://localhost:44316/` come **URL di risposta**. Si tratta dell'URL predefinito per questo esempio di codice.
- Copiare l'**ID applicazione** assegnato all'app. Sarà necessario più avanti.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Creare i criteri

In Azure AD B2C, ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md) specifici. Questa app Web contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. È necessario creare un criterio per ogni tipo, come descritto nell'[articolo di riferimento sui criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere **Nome visualizzato** e altri attributi nei criteri di iscrizione.
- Scegliere le attestazioni dell'applicazione **Nome visualizzato** e **ID oggetto** in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **Nome** di ogni criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`. I nomi dei criteri saranno necessari in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

Si noti che questo articolo non illustra come usare i criteri appena creati. Per informazioni sul funzionamento dei criteri in Azure AD B2C, iniziare dall'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Scaricare il codice

Il codice per questa esercitazione è [disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Per compilare l'esempio passo dopo passo, è possibile [scaricare il progetto bozza come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

L'app completata è anche [disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare.

## Configurare l'app Web TaskWebApp

Perché `TaskWebApp` possa comunicare con Azure AD B2C, è necessario fornire alcuni parametri comuni. Nel progetto `TaskWebApp` aprire il file `web.config` nella radice del progetto e sostituire i valori nella sezione `<appSettings>`. I valori `AadInstance`, `RedirectUri` e `TaskServiceUrl` possono essere lasciati così come sono.

```
  <appSettings>
    
    ...
    
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)] <appSettings> <add key="webpages:Version" value="3.0.0.0" /> <add key="webpages:Enabled" value="false" /> <add key="ClientValidationEnabled" value="true" /> <add key="UnobtrusiveJavaScriptEnabled" value="true" /> <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" /> <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" /> <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" /> <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" /> <add key="ida:RedirectUri" value="https://localhost:44316/" /> <add key="ida:SignUpPolicyId" value="b2c\_1_sign\_up" /> <add key="ida:SignInPolicyId" value="b2c_1_sign\_in" /> <add key="ida:UserProfilePolicyId" value="b2c_1\_edit\_profile" /> <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" /> </appSettings>

## Ottenere i token di accesso e chiamare le API dell'attività

Questa sezione illustra come usare il token ricevuto durante l'accesso con Azure AD B2C per accedere a un'API Web protetta anche con Azure AD B2C.

Questo articolo non illustra i dettagli per la protezione dell'API. Per informazioni sull'autenticazione sicura delle richieste da parte di un'API Web con Azure AD B2C, vedere l'[articolo di introduzione all'API Web](active-directory-b2c-devquickstarts-api-dotnet.md).

### Salvare il token di accesso

Prima di tutto autenticare l'utente usando uno dei criteri e ottenere un token di accesso da Azure AD B2C. In caso di dubbi sull'esecuzione dei criteri, tornare indietro e provare l'[esercitazione introduttiva per la compilazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) per acquisire nozioni di base su Azure AD B2C.

Aprire il file `App_Start\Startup.Auth.cs`. È necessario apportare un'importante modifica a `OpenIdConnectAuthenticationOptions`, ovvero impostare `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

return new OpenIdConnectAuthenticationOptions
{
    // For each policy, give OWIN the policy-specific metadata address, and
    // set the authentication type to the id of the policy
    MetadataAddress = String.Format(aadInstance, tenant, policy),
    AuthenticationType = policy,

    // These are standard OpenID Connect parameters, with values pulled from web.config
    ClientId = clientId,
    RedirectUri = redirectUri,
    PostLogoutRedirectUri = redirectUri,
    Notifications = new OpenIdConnectAuthenticationNotifications
    {
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",
        
        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### Ottenere un token nei controller

`TasksController` è responsabile della comunicazione con l'API Web e dell'invio di richieste HTTP all'API per la lettura, la creazione e l'eliminazione di attività. Poiché che l'API è protetta da Azure AD B2C, per prima cosa è necessario recuperare il token salvato nel passaggio precedente.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;
        
	...
}
```

`BootstrapContext` contiene il token di accesso acquisito con l'esecuzione di uno dei criteri di B2C.

### Leggere le attività dall'API Web

Dopo aver ottenuto un token, collegarlo alla richiesta HTTP `GET` nell'intestazione `Authorization` per chiamare `TaskService` in modo sicuro:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
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
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
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

Seguire lo stesso modello quando si inviano richieste `POST` e `DELETE` all'API Web, usando `BootstrapContext` per recuperare il token di accesso. L'azione di creazione è già stata implementata. Provare a completare l'azione di eliminazione in `TasksController.cs`.

## Eseguire l'app di esempio

Infine, compilare ed eseguire l'app. Eseguire l'iscrizione e l'accesso e creare attività per l'utente connesso. Disconnettersi ed eseguire l'accesso usando un account utente diverso. Creare le attività per l'utente. Si noti che le attività sono archiviate per ogni utente nell'API, perché l'API estrae l'identità dell'utente dai token che riceve.

Come riferimento, l'esempio completo [è disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). È anche possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->