---
title: Configurare un'app Web che esegue l'accesso agli utenti - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'app Web che accede agli utenti (configurazione del codice)
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
ms.openlocfilehash: 51cd7ff97af4588139721930bd4d08ffd0f95e73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297555"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>App Web che accede agli utenti: configurazione del codice

Informazioni su come configurare il codice per l'app Web che consente l'accesso agli utenti.

## <a name="libraries-for-protecting-web-apps"></a>Raccolte per la protezione delle app Web

<!-- This section can be in an include for Web App and Web APIs -->
Le librerie utilizzate per proteggere un'app Web (e un'API Web) sono:

| Piattaforma | Libreria | Descrizione |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Estensioni del modello di identità per .NETIdentity Model Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Utilizzato direttamente da ASP.NET e ASP.NET Core, Microsoft Identity Model Extensions for .NET propone un set di DLL in esecuzione sia su .NET Framework che .NET Core. Da un'app Web ASP.NET o ASP.NET Core è possibile controllare la convalida dei token usando la classe **TokenValidationParameters** (in particolare, in alcuni scenari di partner). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Supporto per applicazioni Web Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Supporto per applicazioni web Python |

Seleziona la scheda che corrisponde alla piattaforma che ti interessa:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I frammenti di codice in questo articolo e i seguenti elementi vengono estratti [dall'esercitazione incrementale dell'app Web ASP.NET Core, capitolo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

È possibile fare riferimento a questa esercitazione per i dettagli completi di implementazione.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I frammenti di codice in questo articolo e i seguenti elementi vengono estratti [dall'esempio di app Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

È possibile fare riferimento a questo esempio per informazioni dettagliate sull'implementazione completa.

# <a name="java"></a>[Java](#tab/java)

I frammenti di codice in questo articolo e quanto segue vengono estratti [dall'applicazione Web Java che chiama l'esempio Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) in MSAL Java.

È possibile fare riferimento a questo esempio per informazioni dettagliate sull'implementazione completa.

# <a name="python"></a>[Python](#tab/python)

I frammenti di codice in questo articolo e quanto segue vengono estratti dall'esempio di grafico Di Python che [chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) in MSAL Python.

È possibile fare riferimento a questo esempio per informazioni dettagliate sull'implementazione completa.

---

## <a name="configuration-files"></a>File di configurazione

Le applicazioni Web che accedono agli utenti utilizzando la piattaforma di identità Microsoft vengono in genere configurate tramite i file di configurazione. Le impostazioni che è necessario compilare sono:

- L'istanza`Instance`cloud ( ) se si desidera che l'app venga eseguita in cloud nazionali, ad esempio
- Il gruppo di destinatari`TenantId`nell'ID tenant ( )
- ID client`ClientId`( ) per l'applicazione, come copiato dal portale di AzureThe client ID ( ) for your application, as copied from the Azure portal

A volte, le applicazioni `Authority`possono essere parametrizzate `Instance` `TenantId`da , che è una concatenazione di e .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core queste impostazioni si trovano nel file [appsettings.json,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) nella sezione "AzureAd".

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
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

In ASP.NET Core, un altro file ([properties , launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) contiene l'URL (`applicationUrl`) e la porta TLS/SSL (`sslPort`) per l'applicazione e vari profili.

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

Nel portale di Azure, gli URI di risposta che è necessario registrare nella pagina **Autenticazione** per l'applicazione devono corrispondere a questi URL. Per i due file di configurazione `https://localhost:44321/signin-oidc`precedenti, essi sarebbero . Il motivo `applicationUrl` `http://localhost:3110`è `sslPort` che è , ma è specificato (44321). `CallbackPath`è `/signin-oidc`, come `appsettings.json`definito in .

Allo stesso modo, l'URI di disconnessione verrebbe impostato su `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, l'applicazione viene configurata tramite il file [Web.config,](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) le righe da 12 a 15.

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

Nel portale di Azure, gli URI di risposta che è necessario registrare nella pagina **Autenticazione** per l'applicazione devono corrispondere a questi URL. Cioè, dovrebbero `https://localhost:44326/`essere .

# <a name="java"></a>[Java](#tab/java)

In Java, la configurazione si trova nel `src/main/resources`file [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) che si trova in .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Nel portale di Azure, gli URI di risposta che è necessario registrare `redirectUri` nella pagina **Autenticazione** per l'applicazione devono corrispondere alle istanze definite dall'applicazione. Cioè, dovrebbero `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me`essere e .

# <a name="python"></a>[Python](#tab/python)

Ecco il file di configurazione Python in [app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Questa guida introduttiva propone di archiviare il segreto client nel file di configurazione per semplicità. Nell'app di produzione è consigliabile usare altri modi per archiviare il segreto, ad esempio un insieme di credenziali delle chiavi o una variabile di ambiente, come descritto nella documentazione di [Flask.](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Codice di inizializzazione

Il codice di inizializzazione è diverso a seconda della piattaforma. Per ASP.NET Core e ASP.NET, gli utenti che accedono vengono delegati al middleware OpenID Connect. Il modello ASP.NET o ASP.NET Core genera applicazioni Web per l'endpoint Azure Active Directory (Azure AD) v1.0. Alcune configurazioni sono necessarie per adattarle all'endpoint della piattaforma di identità Microsoft (v2.0). Nel caso di Java, è gestito da Primavera con la collaborazione dell'applicazione.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET core applicazioni Web (e API Web), l'applicazione è protetta perché si dispone di un `[Authorize]` attributo nei controller o le azioni del controller. Questo attributo controlla che l'utente sia autenticato. Il codice che sta inizializzando l'applicazione si trova nel file di Startup.cs.

Per aggiungere l'autenticazione con la piattaforma di identità Microsoft (in precedenza Azure AD v2.0), è necessario aggiungere il codice seguente. I commenti nel codice devono essere autoesplicativi.

> [!NOTE]
> Se si avvia il progetto con l'impostazione predefinita `dotnet new mvc`ASP.NET `AddAzureAD` progetto Web principale all'interno di Visual Studio o utilizzando , il metodo è disponibile per impostazione predefinita. Questo perché i pacchetti correlati vengono caricati automaticamente.
>
> Se si compila un progetto da zero e si sta tentando di usare il codice seguente, si consiglia di aggiungere il `AddAzureAD` pacchetto NuGet **Microsoft.AspNetCore.Authentication.AzureAD.UI** al progetto per rendere disponibile il metodo.

Il codice riportato di seguito è disponibile in [Startup.cs-L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```csharp
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

Il `AddMicrosoftIdentityPlatformAuthentication` metodo di estensione è definito in [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs .](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23) esso:

- Aggiunge il servizio di autenticazione.
- Configura le opzioni per leggere il file di configurazione.
- Configura le opzioni di OpenID Connect in modo che l'autorità usata sia l'endpoint della piattaforma di identità Microsoft (in precedenza Azure AD v2.0).
- Convalida l'autorità emittente del token.
- Garantisce che le attestazioni corrispondenti `preferred_username` al nome vengano mappate dall'attestazione nel token ID.

Oltre alla configurazione, è possibile specificare il nome `AddMicrosoftIdentityPlatformAuthentication`della sezione di configurazione quando si chiama . Per impostazione predefinita, è `AzureAd`.

La traccia degli eventi middleware OpenId Connect consente di risolvere i problemi dell'applicazione Web se l'autenticazione non funziona. L'impostazione `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` di su `true` mostrerà come le informazioni vengono elaborate dal set di middleware ASP.NET Core `HttpContext.User`man mano che avanzano dalla risposta HTTP all'identità dell'utente in .

```csharp
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

La `AadIssuerValidator` classe consente all'autorità emittente del token di essere convalidata in molti casi. Questa classe funziona con un token v1.0 o v2.0, un'applicazione single-tenant o multitenant o un'applicazione che accede agli utenti con i propri account Microsoft personali nel cloud pubblico di Azure o nei cloud nazionali. È disponibile da [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Il codice relativo all'autenticazione in un'app Web ASP.NET e alle API Web si trova nel file [App_Start/Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)

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

Nell'esempio Java viene utilizzato il framework Spring. L'applicazione è protetta perché si implementa un filtro, che intercetta ogni risposta HTTP. Nella guida introduttiva per le app `AuthFilter` `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`Web Java questo filtro è contenuto in .

Il filtro elabora il flusso del codice di autorizzazione OAuth 2.0 e controlla se l'utente è autenticato (metodo).`isAuthenticated()` Se l'utente non è autenticato, calcola l'URL degli endpoint di autorizzazione di Azure AD e reindirizza il browser a questo URI.

Quando arriva la risposta, contenente il codice di autorizzazione, acquisisce il token utilizzando MSAL Java. Quando riceve infine il token dall'endpoint del token (nell'URI di reindirizzamento), l'utente ha eseguito l'accesso.

Per informazioni dettagliate, vedere il `doFilter()` metodo in [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Il codice `doFilter()` di è scritto in un ordine leggermente diverso, ma il flusso è quello descritto.

Per informazioni dettagliate sul flusso del codice di autorizzazione attivato da questo metodo, vedere Piattaforma di identità Microsoft e Flusso di codice di [autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

L'esempio Python usa Flask.The Python sample uses Flask. L'inizializzazione di Flask e MSAL Python viene eseguita in [app.py-L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

Nel prossimo articolo verrà illustrato come attivare l'accesso e la disconnessione.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Accesso e disconnessione](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Accesso e disconnessione](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Accesso e disconnessione](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Accesso e disconnessione](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
