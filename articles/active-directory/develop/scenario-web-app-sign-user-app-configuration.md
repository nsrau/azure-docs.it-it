---
title: Configurare un'app Web per l'accesso degli utenti a Microsoft Identity Platform | Azure
description: Informazioni su come creare un'app Web per l'accesso degli utenti (configurazione del codice)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: ea0312cd8129fce342f94cfab5701d1773aca309
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728336"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>App Web che esegue l'accesso degli utenti: configurazione del codice

Informazioni su come configurare il codice per l'app Web che esegue l'accesso agli utenti.

## <a name="libraries-for-protecting-web-apps"></a>Librerie per la protezione delle app Web

<!-- This section can be in an include for web app and web APIs -->
Le librerie usate per proteggere un'app Web (e un'API Web) sono:

| Piattaforma | Libreria | Descrizione |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [Estensioni del modello di identità per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usato direttamente da ASP.NET e ASP.NET Core, Microsoft Identity Model Extensions per .NET propone un set di dll in esecuzione sia in .NET Framework che in .NET Core. Da un'app Web ASP.NET o ASP.NET Core è possibile controllare la convalida dei token usando la classe **TokenValidationParameters** (in particolare, in alcuni scenari partner). In pratica, la complessità è incapsulata nella libreria [Microsoft. Identity. Web](https://aka.ms/ms-identity-web) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Supporto per le applicazioni Web Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Supporto per le applicazioni Web Python |

Selezionare la scheda che corrisponde alla piattaforma a cui si è interessati:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall' [esercitazione incrementale ASP.NET Core app Web, capitolo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

È possibile fare riferimento a questa esercitazione per i dettagli di implementazione completi.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall' [esempio di app web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

È possibile fare riferimento a questo esempio per i dettagli di implementazione completi.

# <a name="java"></a>[Java](#tab/java)

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall'esempio di [applicazione Web Java che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) in MSAL Java.

È possibile fare riferimento a questo esempio per i dettagli di implementazione completi.

# <a name="python"></a>[Python](#tab/python)

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall'esempio di [applicazione Web Python che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) in MSAL Python.

È possibile fare riferimento a questo esempio per i dettagli di implementazione completi.

---

## <a name="configuration-files"></a>File di configurazione

Le applicazioni Web che eseguono l'accesso agli utenti tramite la piattaforma di identità Microsoft vengono configurate tramite i file di configurazione. Le impostazioni che è necessario compilare sono:

- Istanza cloud ( `Instance` ) se si vuole che l'app venga eseguita nei cloud nazionali, ad esempio
- Destinatari nell'ID tenant ( `TenantId` )
- ID client ( `ClientId` ) per l'applicazione, copiato dal portale di Azure

In alcuni casi, le applicazioni possono essere con parametri by `Authority` , che è una concatenazione di `Instance` e `TenantId` .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core queste impostazioni si trovano nella sezione "AzureAd" del [appsettings.jssul](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) file.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

In ASP.NET Core un altro file ([properties\launchSettings.jssu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) contiene l'URL ( `applicationUrl` ) e la porta TLS/SSL ( `sslPort` ) per l'applicazione e vari profili.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Nel portale di Azure gli URI di risposta necessari per la registrazione nella pagina di **autenticazione** per l'applicazione devono corrispondere a questi URL. Per i due file di configurazione precedenti, sarebbe `https://localhost:44321/signin-oidc` . Il motivo è `applicationUrl` `http://localhost:3110` , ma `sslPort` è specificato (44321). `CallbackPath` è `/signin-oidc` , come definito in `appsettings.json` .

Allo stesso modo, l'URI di disconnessione viene impostato su `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, l'applicazione viene configurata tramite il file di [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) , righe da 12 a 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Nel portale di Azure gli URI di risposta necessari per la registrazione nella pagina di **autenticazione** per l'applicazione devono corrispondere a questi URL. Ovvero devono essere `https://localhost:44326/` .

# <a name="java"></a>[Java](#tab/java)

In Java la configurazione si trova nel file [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) che si trova in `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Nel portale di Azure, gli URI di risposta che è necessario registrare nella pagina di **autenticazione** per l'applicazione devono corrispondere alle `redirectUri` istanze definite dall'applicazione. Ovvero devono essere `http://localhost:8080/msal4jsample/secure/aad` e `http://localhost:8080/msal4jsample/graph/me` .

# <a name="python"></a>[Python](#tab/python)

Ecco il file di configurazione di Python in [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Questa Guida introduttiva propone di archiviare il segreto client nel file di configurazione per semplicità. Nell'app di produzione è consigliabile usare altri modi per archiviare il segreto, ad esempio un insieme di credenziali delle chiavi o una variabile di ambiente, come descritto nella [documentazione di Flask](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Codice di inizializzazione

Il codice di inizializzazione è diverso a seconda della piattaforma. Per ASP.NET Core e ASP.NET, l'accesso degli utenti viene delegato al middleware OpenID Connect. Il modello ASP.NET o ASP.NET Core genera applicazioni Web per l'endpoint Azure Active Directory (Azure AD) v 1.0. Alcune configurazioni sono necessarie per adattarle all'endpoint della piattaforma Microsoft Identity (v 2.0). Nel caso di Java, viene gestito da Spring con la collaborazione dell'applicazione.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core app Web (e le API Web), l'applicazione è protetta perché è presente un `[Authorize]` attributo sui controller o sulle azioni del controller. Questo attributo verifica che l'utente sia autenticato. Il codice che Inizializza l'applicazione si trova nel file *Startup.cs* .

Per aggiungere l'autenticazione con la piattaforma di identità Microsoft (in precedenza Azure AD v 2.0), è necessario aggiungere il codice seguente. I commenti nel codice devono essere autoesplicativi.

> [!NOTE]
> Se si vuole iniziare direttamente con i nuovi modelli di ASP.NET Core per la piattaforma di identità Microsoft, che sfruttano Microsoft. Identity. Web, è possibile scaricare un pacchetto NuGet di anteprima contenente i modelli di progetto per .NET Core 3,1 e .NET 5,0. Quindi, una volta installato, è possibile creare direttamente un'istanza di ASP.NET Core applicazioni Web (MVC o blazer). Per informazioni dettagliate, vedere [modelli di progetto di app Web Microsoft. Identity. Web](https://aka.ms/ms-id-web/webapp-project-templates) . Questo è l'approccio più semplice in quanto eseguirà tutti i passaggi indicati di seguito.
>
> Se si preferisce avviare il progetto con l'impostazione predefinita corrente ASP.NET Core progetto Web in Visual Studio o tramite `dotnet new mvc --auth SingleAuth` o `dotnet new webapp --auth SingleAuth` , verrà visualizzato un codice simile al seguente:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> Questo codice usa il pacchetto NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** legacy che viene usato per creare un'applicazione Azure ad versione 1.0. Questo articolo illustra come creare un'applicazione Microsoft Identity Platform (Azure AD v 2.0) che sostituisce tale codice.
>

1. Aggiungere i pacchetti NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) e [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) al progetto. Rimuovere il pacchetto NuGet Microsoft. AspNetCore. Authentication. AzureAD. UI se presente.

2. Aggiornare il codice in in `ConfigureServices` modo che usi i `AddMicrosoftIdentityWebAppAuthentication` `AddMicrosoftIdentityUI` metodi e.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. Nel `Configure` metodo in *Startup.cs*abilitare l'autenticazione con una chiamata a `app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

Nel codice precedente:
- Il `AddMicrosoftIdentityWebAppAuthentication` metodo di estensione è definito in **Microsoft. Identity. Web**. È
  - Aggiunge il servizio di autenticazione.
  - Configura le opzioni per la lettura del file di configurazione (qui dalla sezione "AzureAD")
  - Configura le opzioni di OpenID Connect in modo che l'autorità sia l'endpoint della piattaforma di identità Microsoft.
  - Convalida l'emittente del token.
  - Garantisce che le attestazioni corrispondenti al nome siano mappate dall' `preferred_username` attestazione nel token ID.

- Oltre all'oggetto di configurazione, è possibile specificare il nome della sezione di configurazione quando si chiama `AddMicrosoftIdentityWebAppAuthentication` . Per impostazione predefinita, è `AzureAd` .

- `AddMicrosoftIdentityWebAppAuthentication` dispone di altri parametri per gli scenari avanzati. Ad esempio, la traccia degli eventi del middleware OpenID Connect può aiutare a risolvere i problemi dell'applicazione Web se l'autenticazione non funziona. Impostando il parametro facoltativo `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` su verrà visualizzato il modo in cui le `true` informazioni vengono elaborate dal set di ASP.NET Core middleware durante l'avanzamento dalla risposta http all'identità dell'utente in `HttpContext.User` .

- Il `AddMicrosoftIdentityUI` metodo di estensione è definito in **Microsoft. Identity. Web. UI**. Fornisce un controller predefinito per gestire l'accesso e la disconnessione.

Per altre informazioni sul modo in cui Microsoft. Identity. Web consente di creare app Web in, vedere <https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> Attualmente, Microsoft. Identity. Web non supporta lo scenario di **singoli account utente** (archiviando gli account utente in-app) quando si usa Azure ad come e provider di accesso esterno. Per informazioni dettagliate, vedere: [AzureAD/Microsoft-Identity-Web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Il codice correlato all'autenticazione in un'app Web ASP.NET e in API Web si trova nel file [app_start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

L'esempio Java USA Spring Framework. L'applicazione è protetta perché si implementa un filtro che intercetta ogni risposta HTTP. Nella Guida introduttiva per le app Web Java questo filtro si trova `AuthFilter` in `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` .

Il filtro elabora il flusso del codice di autorizzazione OAuth 2,0 e controlla se l'utente è autenticato ( `isAuthenticated()` metodo). Se l'utente non è autenticato, calcola l'URL dei Azure AD endpoint di autorizzazione e reindirizza il browser a questo URI.

Quando arriva la risposta, che contiene il codice di autorizzazione, acquisisce il token usando MSAL Java. Quando riceve infine il token dall'endpoint del token (sull'URI di reindirizzamento), l'utente ha eseguito l'accesso.

Per informazioni dettagliate, vedere il `doFilter()` metodo in [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Il codice di `doFilter()` viene scritto in un ordine leggermente diverso, ma il flusso è quello descritto.

Per informazioni dettagliate sul flusso del codice di autorizzazione attivato da questo metodo, vedere [Microsoft Identity Platform e il flusso del codice di autorizzazione OAuth 2,0](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

Nell'esempio Python viene usato Flask. L'inizializzazione di Flask e MSAL Python viene eseguita in [app. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Passaggi successivi

Nell'articolo successivo verrà illustrato come attivare l'accesso e la disconnessione.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Accesso e disconnessione](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Accesso e disconnessione](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Accesso e disconnessione](./scenario-web-app-sign-user-sign-in.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Accesso e disconnessione](./scenario-web-app-sign-user-sign-in.md?tabs=python)

---
