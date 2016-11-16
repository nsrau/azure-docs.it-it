---
title: Azure AD B2C | Documentazione Microsoft
description: Come compilare un&quot;API Web .NET usando Azure Active Directory B2C e protetta con i token di accesso OAuth 2.0 per l&quot;autenticazione.
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/22/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 370978187cffa2e5a9544bf99e6a15e13f97ac53


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: compilare un'API Web .NET
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C permette di proteggere un'API Web usando i token di accesso OAuth 2.0. I token di accesso consentono alle app client che usano Azure AD B2C di eseguire l'autenticazione all'API. Questo articolo illustra come creare un'API elenco attività MVC (Model-View-Controller) .NET che consente agli utenti di eseguire azioni CRUD sulle attività. L'API Web è protetta con Azure AD B2C e consente soltanto agli utenti autenticati di gestire il proprio elenco attività.

## <a name="create-an-azure-ad-b2c-directory"></a>Creare una directory Azure AD B2C
Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire con questa guida.

## <a name="create-an-application"></a>Creare un'applicazione
Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

* Includere un'**app Web** o un'**API Web** nell'applicazione.
* Usare l'**URI di reindirizzamento** `https://localhost:44316/` per l'app Web. Si tratta della posizione predefinita del client dell'app Web per questo codice di esempio.
* Copiare l' **ID applicazione** assegnato all'app. Sarà necessario più avanti.
  
  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri
In Azure AD B2C, ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md). Il client in questo esempio di codice contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. Sarà necessario creare i criteri per ogni tipo, come descritto nell' [articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri assicurarsi di:

* Scegliere **Iscrizione ID utente** o **Iscrizione posta elettronica** nel pannello dei provider di identità.
* Scegliere **Nome visualizzato** e altri attributi nei criteri di iscrizione.
* Scegliere le attestazioni **Nome visualizzato** e **ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
* Copiare il **nome** di ogni criterio dopo averlo creato. I nomi dei criteri saranno necessari in un secondo momento.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

## <a name="download-the-code"></a>Scaricare il codice
Il codice per questa esercitazione è [disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Per compilare l'esempio passo dopo passo, è possibile [scaricare un progetto scheletro come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

L'app completata è anche [disponibile come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare. Il file della soluzione contiene due progetti: `TaskWebApp` e `TaskService`. `TaskWebApp` è un'applicazione Web MVC con cui l'utente interagisce. `TaskService` è l'API Web back-end dell'app in cui viene archiviato l'elenco attività di ogni utente.

## <a name="configure-the-task-web-app"></a>Configurare l'app Web TaskWebApp
Quando l'utente interagisce con `TaskWebApp`, il client invia richieste ad Azure AD e ottiene i token da usare per chiamare l'API Web `TaskService`. Per consentire l'accesso dell'utente e ottenere i token, è necessario fornire a `TaskWebApp` alcune informazioni sull'app. Nel progetto `TaskWebApp` aprire il file `web.config` nella radice del progetto e sostituire i valori nella sezione `<appSettings>`.  I valori `AadInstance`, `RedirectUri` e `TaskServiceUrl` possono essere lasciati così come sono.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Questo articolo non descrive la compilazione del client di `TaskWebApp` .  Per informazioni su come compilare un'app Web usando Azure AD B2C, vedere l' [esercitazione sulle app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Proteggere l'API
Ora che è disponibile un client che chiama l'API per conto degli utenti, è possibile proteggere `TaskService` usando token di connessione OAuth 2.0. L'API può accettare e convalidare i token usando la libreria OWIN (Open Web Interface for .NET) di Microsoft.

### <a name="install-owin"></a>Installare OWIN
Per iniziare, installare la pipeline di autenticazione OWIN OAuth:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Immettere le informazioni B2C
Aprire il file `web.config` nella radice del progetto `TaskService` e sostituire i valori nella sezione `<appSettings>`. Questi valori verranno usati nell'API e nella libreria OWIN.  Il valore `AadInstance` può essere lasciato invariato.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Aggiungere una classe di avvio OWIN
Aggiungere al progetto `TaskService` una classe di avvio OWIN denominata `Startup.cs`.  Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi **Nuovo elemento** e cercare "OWIN".

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

### <a name="configure-oauth-20-authentication"></a>Configurare l'autenticazione OAuth 2.0
Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(...)`.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Proteggere il controller dell'attività
Dopo aver configurato l'app per usare l'autenticazione OAuth 2.0, per proteggere l'API Web aggiungere un tag `[Authorize]` al controller dell'attività. Si tratta del controller in cui avvengono tutte le modifiche relative all'elenco attività. Sarà quindi necessario proteggere l'intero controller a livello della classe. È anche possibile aggiungere il tag `[Authorize]` a singole azioni per un controllo più specifico.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Ottenere le informazioni utente dal token
`TasksController` archivia le attività in un database in cui a ogni attività è associato un utente "proprietario" dell'attività. Il proprietario è identificato dall' **ID oggetto**dell'utente. Per questo motivo è necessario aggiungere l'ID oggetto come attestazione dell'applicazione in tutti i criteri.

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio
Compilare ed eseguire infine `TaskWebApp` e `TaskService`. Effettuare l'iscrizione all'app usando un indirizzo di posta elettronica o un nome utente. Creare alcune attività nell'elenco attività dell'utente e osservarne la persistenza nell'API anche dopo l'arresto e il riavvio del client.

## <a name="edit-your-policies"></a>Modificare i criteri
Dopo aver protetto l'API con Azure AD B2C, è possibile provare i criteri dell'app e visualizzarne gli effetti o l'assenza di effetti nell'API. È possibile modificare le attestazioni dell'applicazione nei criteri nonché le informazioni utente disponibili nell'API Web. Le eventuali attestazioni aggiunte saranno disponibili per l'API Web MVC .NET nell'oggetto `ClaimsPrincipal` , come descritto in precedenza in questo articolo.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Nov16_HO2-->


