---
title: Configurare un'app Web per l'accesso degli utenti a Microsoft Identity Platform | Azure
description: Informazioni su come creare un'app Web per l'accesso degli utenti (configurazione del codice)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b077a71a541d29c9b93778babc096ea40c3b43cb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964872"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>App Web che esegue l'accesso degli utenti: configurazione del codice

Informazioni su come configurare il codice per l'app Web che esegue l'accesso agli utenti.

## <a name="libraries-for-protecting-web-apps"></a>Librerie per la protezione delle app Web

<!-- This section can be in an include for Web App and Web APIs -->
Le librerie usate per proteggere un'app Web (e un'API Web) sono:

| Piattaforma | Library | Description |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Estensioni del modello di identità per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usato direttamente da ASP.NET e ASP.NET Core, Microsoft Identity Model Extensions per .NET propone un set di dll in esecuzione sia in .NET Framework che in .NET Core. Da un'app Web ASP.NET o ASP.NET Core è possibile controllare la convalida dei token usando la classe **TokenValidationParameters** (in particolare, in alcuni scenari partner). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Microsoft Authentication Library (MSAL) per Java. Attualmente disponibile in anteprima pubblica. |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL per Python. Attualmente disponibile in anteprima pubblica. |

Selezionare la scheda che corrisponde alla piattaforma a cui si è interessati:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall' [esercitazione incrementale ASP.NET Core app Web, capitolo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

È possibile fare riferimento a questa esercitazione per i dettagli di implementazione completi.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall' [esempio di app web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

È possibile fare riferimento a questo esempio per i dettagli di implementazione completi.

# <a name="javatabjava"></a>[Java](#tab/java)

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall'esempio di [applicazione Web Java che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) in MSAL Java.

È possibile fare riferimento a questo esempio per i dettagli di implementazione completi.

# <a name="pythontabpython"></a>[Python](#tab/python)

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall'esempio di [applicazione Web Python che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) in MSAL Python.

È possibile fare riferimento a questo esempio per i dettagli di implementazione completi.

---

## <a name="configuration-files"></a>File di configurazione

Le applicazioni Web che eseguono l'accesso agli utenti tramite la piattaforma di identità Microsoft vengono in genere configurate tramite i file di configurazione. Le impostazioni che è necessario compilare sono:

- L'istanza cloud (`Instance`) se si vuole che l'app venga eseguita nei cloud nazionali, ad esempio
- Destinatari nell'ID tenant (`TenantId`)
- ID client (`ClientId`) per l'applicazione, copiato dalla portale di Azure

In alcuni casi, le applicazioni possono essere con parametri tramite `Authority`, che è una concatenazione di `Instance` e `TenantId`.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core queste impostazioni si trovano nel file [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) , nella sezione "AzureAd".

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
    "TenantId": "[Enter the tenantId here]]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

In ASP.NET Core un altro file ([properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) contiene l'URL (`applicationUrl`) e la porta SSL (`sslPort`) per l'applicazione e vari profili.

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

Nel portale di Azure gli URI di risposta necessari per la registrazione nella pagina di **autenticazione** per l'applicazione devono corrispondere a questi URL. Per i due file di configurazione precedenti, verranno `https://localhost:44321/signin-oidc`. Il motivo è che `applicationUrl` è `http://localhost:3110`, ma viene specificato `sslPort` (44321). `CallbackPath` è `/signin-oidc`, come definito in `appsettings.json`.

Allo stesso modo, l'URI di disconnessione viene impostato su `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, l'applicazione viene configurata tramite il file [Web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) , righe da 12 a 15.

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

Nel portale di Azure gli URI di risposta necessari per la registrazione nella pagina di **autenticazione** per l'applicazione devono corrispondere a questi URL. Ovvero devono essere `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

In Java la configurazione si trova nel file [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) situato in `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Nel portale di Azure, gli URI di risposta necessari per la registrazione nella pagina di **autenticazione** per l'applicazione devono corrispondere alle istanze di `redirectUri` definite dall'applicazione. Ovvero devono essere `http://localhost:8080/msal4jsample/secure/aad` e `http://localhost:8080/msal4jsample/graph/me`.

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core app Web (e le API Web), l'applicazione è protetta perché è presente un attributo `[Authorize]` per i controller o le azioni del controller. Questo attributo verifica che l'utente sia autenticato. Il codice che Inizializza l'applicazione si trova nel file Startup.cs. 

Per aggiungere l'autenticazione con la piattaforma di identità Microsoft (in precedenza Azure AD v 2.0), è necessario aggiungere il codice seguente. I commenti nel codice devono essere autoesplicativi.

> [!NOTE]
> Se si avvia il progetto con il progetto Web ASP.NET Core predefinito in Visual Studio o tramite `dotnet new mvc`, il metodo `AddAzureAD` è disponibile per impostazione predefinita. Ciò è dovuto al fatto che i pacchetti correlati vengono caricati automaticamente.
>
> Se si compila un progetto da zero e si tenta di usare il codice seguente, è consigliabile aggiungere il pacchetto NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** al progetto per rendere disponibile il `AddAzureAD` metodo.

Il codice seguente è disponibile da [Startup. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```CSharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

Il metodo di estensione `AddMicrosoftIdentityPlatformAuthentication` è definito in [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). È

- Aggiunge il servizio di autenticazione.
- Configura le opzioni per la lettura del file di configurazione.
- Configura le opzioni di OpenID Connect in modo che l'autorità utilizzata sia l'endpoint della piattaforma di identità Microsoft (in precedenza Azure AD v 2.0).
- Convalida l'emittente del token.
- Garantisce che le attestazioni corrispondenti al nome siano mappate dall'attestazione `preferred_username` nel token ID.

Oltre alla configurazione, è possibile specificare il nome della sezione di configurazione quando si chiama `AddMicrosoftIdentityPlatformAuthentication`. Per impostazione predefinita, è `AzureAd`.

La traccia degli eventi del middleware OpenId Connect può essere utile per risolvere i problemi dell'applicazione Web se l'autenticazione non funziona. Impostando `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` su `true` verrà illustrato il modo in cui le informazioni vengono elaborate dal set di ASP.NET Core middleware durante l'avanzamento dalla risposta HTTP all'identità dell'utente in `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

La classe `AadIssuerValidator` consente la convalida in molti casi dell'emittente del token. Questa classe funziona con un token v 1.0 o v 2.0, un'applicazione multi-tenant o multi-tenant o un'applicazione che esegue l'accesso degli utenti con gli account Microsoft personali nel cloud pubblico di Azure o nei cloud nazionali. È disponibile da [Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Il codice correlato all'autenticazione in un'app Web ASP.NET e in API Web si trova nel file [app_start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```CSharp
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

# <a name="javatabjava"></a>[Java](#tab/java)

L'esempio Java USA Spring Framework. L'applicazione è protetta perché si implementa un filtro che intercetta ogni risposta HTTP. Nella Guida introduttiva per le app Web Java questo filtro è `AuthFilter` in `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. 

Il filtro elabora il flusso del codice di autorizzazione OAuth 2,0 e controlla se l'utente è autenticato (`isAuthenticated()` metodo). Se l'utente non è autenticato, calcola l'URL dei Azure AD endpoint di autorizzazione e reindirizza il browser a questo URI.

Quando arriva la risposta, che contiene il codice di autorizzazione, acquisisce il token usando MSAL Java. Quando riceve infine il token dall'endpoint del token (sull'URI di reindirizzamento), l'utente ha eseguito l'accesso.

Per informazioni dettagliate, vedere il metodo `doFilter()` in [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Il codice del `doFilter()` viene scritto in un ordine leggermente diverso, ma il flusso è quello descritto.

Per informazioni dettagliate sul flusso del codice di autorizzazione attivato da questo metodo, vedere [Microsoft Identity Platform e il flusso del codice di autorizzazione OAuth 2,0](v2-oauth2-auth-code-flow.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Accedere e disconnettersi](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Accedere e disconnettersi](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Accedere e disconnettersi](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Accedere e disconnettersi](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
