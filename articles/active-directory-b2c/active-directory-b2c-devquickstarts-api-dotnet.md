---
title: Azure AD B2C | Documentazione Microsoft
description: Come compilare un'API Web .NET con Azure Active Directory B2C protetta con i token di accesso OAuth 2.0 per l'autenticazione.
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 48749bfa2ab54a0e766a4aad4f39073cc4e90818
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017

---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: compilare un'API Web .NET

Azure Active Directory (Azure AD) B2C permette di proteggere un'API Web usando i token di accesso OAuth 2.0. Questi token consentono alle app client di eseguire l'autenticazione all'API. Questo articolo illustra come creare un'API "elenco attività" MVC .NET che consente agli utenti dell'applicazione client di eseguire azioni CRUD sulle attività. L'API Web è protetta con Azure AD B2C e consente soltanto agli utenti autenticati di gestire il proprio elenco attività.

## <a name="create-an-azure-ad-b2c-directory"></a>Creare una directory Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire con questa guida.

> [!NOTE]
> L'applicazione client e l'API Web devono usare la stessa directory di Azure AD B2C.
>

## <a name="create-a-web-api"></a>Creare un'API Web

Successivamente, è necessario creare un'app per le API Web nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

* Includere un'**app Web** o un'**API Web** nell'applicazione.
* Usare l'**URI di reindirizzamento** `https://localhost:44332/` per l'app Web. Si tratta della posizione predefinita del client dell'app Web per questo codice di esempio.
* Copiare l' **ID applicazione** assegnato all'app. Sarà necessario più avanti.
* Immettere un identificatore app in **URI ID app**.
* Aggiungere le autorizzazioni tramite il menu **Ambiti pubblicati**.

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md)specifici. Sarà necessario creare un criterio per comunicare con Azure AD B2C. È consigliabile usare i criteri di iscrizione/accesso combinati, come illustrato nell'[articolo di riferimento sui criteri](active-directory-b2c-reference-policies.md). Durante la creazione dei criteri, assicurarsi di:

* Scegliere **Nome visualizzato** e altri attributi di iscrizione nei criteri.
* Scegliere le attestazioni **Nome visualizzato** e **ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
* Copiare il **Nome** di ogni criterio dopo averlo creato. Il nome dei criteri sarà necessario più avanti.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i criteri, è possibile passare alla compilazione dell'app.

## <a name="download-the-code"></a>Scaricare il codice

Il codice per questa esercitazione è salvato su [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). È possibile clonare l'esempio eseguendo:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare. Il file della soluzione contiene due progetti: `TaskWebApp` e `TaskService`. `TaskWebApp` è un'applicazione Web MVC con cui l'utente interagisce. `TaskService` è l'API Web back-end dell'app in cui viene archiviato l'elenco attività di ogni utente. Questo articolo illustra solo l'applicazione `TaskService`. Per informazioni su come compilare `TaskWebApp` usando Azure AD B2C, vedere l'[esercitazione sulle app Web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Aggiornare la configurazione di Azure AD B2C

L'esempio è configurato per l'uso dei criteri e dell'ID client del tenant di demo. Se si vuole usare il proprio tenant, sarà necessario seguire questa procedura:

1. Aprire `web.config` nel progetto `TaskService` e sostituire i valori seguenti:
    * `ida:Tenant` con il nome del tenant
    * `ida:ClientId` con l'ID dell'applicazione API Web
    * `ida:SignUpSignInPolicyId` con il nome del criterio "di iscrizione o di accesso"

2. Aprire `web.config` nel progetto `TaskWebApp` e sostituire i valori di
    * `ida:Tenant` con il nome del tenant
    * `ida:ClientId` con l'ID dell'applicazione di tipo app Web
    * `ida:ClientSecret` con la chiave privata dell'app Web
    * `ida:SignUpSignInPolicyId` con il nome del criterio "Iscrizione o accesso"
    * `ida:EditProfilePolicyId` con il nome del criterio "Modifica profilo"
    * `ida:ResetPasswordPolicyId` con il nome del criterio "Reimposta password"


## <a name="secure-the-api"></a>Proteggere l'API

Quando è disponibile un client che chiama l'API, è possibile proteggere l'API, ad esempio `TaskService`, usando i token di connessione OAuth 2.0. In questo modo si assicura che ogni richiesta all'API sarà valida solo se la richiesta include un token di connessione. L'API può accettare e convalidare i token di connessione usando la libreria OWIN (Open Web Interface for .NET) di Microsoft.

### <a name="install-owin"></a>Installare OWIN

Installare prima di tutto la pipeline di autenticazione OAuth OWIN usando la console di Gestione pacchetti di Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

Verrà installato il middleware OWIN che accetterà e convaliderà i token di connessione.

### <a name="add-an-owin-startup-class"></a>Aggiungere una classe di avvio OWIN

Aggiungere una classe di avvio OWIN all'API denominata `Startup.cs`.  Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi **Nuovo elemento** e cercare "OWIN". Il middleware OWIN richiamerà il metodo `Configuration(…)` all'avvio dell'app.

In questo esempio la dichiarazione di classe è stata modificata in `public partial class Startup` ed è stata implementata l'altra parte della classe in `App_Start\Startup.Auth.cs`. Nel metodo `Configuration` è stata aggiunta una chiamata a `ConfigureAuth`, definita in `Startup.Auth.cs`. Dopo le modifiche, `Startup.cs` ha un aspetto analogo al seguente:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Configurare l'autenticazione OAuth 2.0

Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(...)`. Ad esempio può avere un aspetto simile al seguente:

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Proteggere il controller dell'attività

Dopo aver configurato l'app per usare l'autenticazione OAuth 2.0, per proteggere l'API Web aggiungere un tag `[Authorize]` al controller dell'attività. Si tratta del controller in cui avvengono tutte le modifiche relative all'elenco attività. Sarà quindi necessario proteggere l'intero controller a livello della classe. È anche possibile aggiungere il tag `[Authorize]` a singole azioni per un controllo più specifico.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Ottenere le informazioni utente dal token

`TasksController` archivia le attività in un database in cui a ogni attività è associato un utente "proprietario" dell'attività. Il proprietario è identificato dall' **ID oggetto**dell'utente. Per questo motivo è necessario aggiungere l'ID oggetto come attestazione dell'applicazione in tutti i criteri.

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>Convalidare le autorizzazioni nel token

Un requisito comune per l'API Web è la convalida degli "ambiti" presenti nel token. Ciò garantisce che l'utente goda delle autorizzazioni necessarie per accedere a un servizio di tipo "To Do List".

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Compilare ed eseguire infine `TaskWebApp` e `TaskService`. Creare alcune attività nell'elenco attività dell'utente e osservarne la persistenza nell'API anche dopo l'arresto e il riavvio del client.

## <a name="edit-your-policies"></a>Modificare i criteri

Dopo aver protetto l'API con Azure AD B2C, è possibile provare i criteri di iscrizione/di accesso dell'app e visualizzarne gli effetti o l'assenza di effetti nell'API. È possibile modificare le attestazioni dell'applicazione nei criteri nonché le informazioni utente disponibili nell'API Web. Le eventuali attestazioni aggiunte saranno disponibili per l'API Web MVC .NET nell'oggetto `ClaimsPrincipal` , come descritto in precedenza in questo articolo.

