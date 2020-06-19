---
title: Configurare un'app Web che chiama le API Web - Microsoft Identity Platform | Azure
description: Informazioni su come configurare il codice di un'app Web che chiama le API Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 82439f8380b0dca676b781e36fff738b5d5bee93
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758181"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>App Web che chiama le API Web: Configurazione del codice

Come illustrato nello scenario [App Web che esegue l'accesso degli utenti](scenario-web-app-sign-user-overview.md), l'app Web usa il [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md) per l'accesso dell'utente. Il flusso include due passaggi:

1. Richiesta di un codice di autorizzazione. Questa parte delega un dialogo privato con l'utente a Microsoft Identity Platform. Durante il dialogo, l'utente esegue l'accesso e acconsente all'uso di API Web. Quando il dialogo privato termina correttamente, l'app Web riceve un codice di autorizzazione sull'URI di reindirizzamento.
1. Richiesta di un token di accesso per l'API tramite il riscatto del codice di autorizzazione.

Gli scenari [App Web che esegue l'accesso degli utenti](scenario-web-app-sign-user-overview.md) prevedono solo il primo passaggio. Di seguito viene descritto come modificare l'app Web in modo che non solo esegua l'accesso degli utenti ma chiami anche le API Web.

## <a name="libraries-that-support-web-app-scenarios"></a>Librerie che supportano gli scenari delle app Web

Le librerie seguenti in Microsoft Authentication Library (MSAL) supportano il flusso del codice di autorizzazione per le app Web:

| Libreria MSAL | Descrizione |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Supporto per le piattaforme .NET Framework e .NET Core. Non sono supportate le piattaforme UWP (Universal Windows Platform), Xamarin.iOS e Xamarin.Android perché usate per creare applicazioni client pubbliche. Per le app Web e le API Web ASP.NET Core, MSAL.NET è incapsulato in una libreria di livello più alto denominata Microsoft.Identity.Web|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL per Python | Supporto per le applicazioni Web Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL per Java | Supporto per le applicazioni Web Java. |

Selezionare la scheda della piattaforma desiderata:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Per consentire all'app Web di chiamare le API protette quando si usa Microsoft.Identity.Web, è necessario chiamare solo `AddWebAppCallsProtectedWebApi` e specificare un formato di serializzazione della cache dei token (ad esempio, cache dei token in memoria):

```C#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // more code here

    services.AddSignIn(Configuration, "AzureAd")
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddInMemoryTokenCaches();

    // more code here
}
```

Per altre informazioni sulla cache dei token, vedere [Opzioni di serializzazione della cache dei token](#token-cache)

> [!NOTE]
> Per comprendere gli esempi di codice, è necessario conoscere le [Nozioni fondamentali su ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals), in particolare l'[inserimento delle dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) e le [opzioni](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Poiché l'accesso utente viene delegato al middleware Open ID Connect (OIDC), è necessario interagire con il processo OIDC. La modalità di interazione varia a seconda del framework in uso.

Per ASP.NET, si effettua la sottoscrizione per gli eventi OIDC del middleware:

- Si consentirà ad ASP.NET Core di richiedere un codice di autorizzazione tramite il middleware Open ID Connect. ASP.NET o ASP.NET Core consentirà all'utente di accedere e acconsentire.
- Si effettuerà la sottoscrizione dell'app Web per ricevere il codice di autorizzazione. La sottoscrizione viene eseguita usando un delegato C#.
- Quando viene ricevuto il codice di autorizzazione, si useranno le librerie MSAL per riscattarlo. I token di accesso e i token di aggiornamento risultanti vengono archiviati nella cache dei token. La cache può essere usata in altre parti dell'applicazione, ad esempio i controller, per acquisire altri token in modo invisibile all'utente.

Gli esempi di codice di questo articolo e di quello successivo sono tratti dall'[esempio di app Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). È possibile fare riferimento all'esempio per i dettagli di implementazione completi.

# <a name="java"></a>[Java](#tab/java)

Gli esempi di codice di questo articolo e di quello successivo sono tratti dall'[applicazione Web Java che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), un esempio di app Web che usa MSAL per Java.
L'esempio consente attualmente a MSAL per Java di produrre l'URL del codice di autorizzazione e gestisce la navigazione all'endpoint di autorizzazione per Microsoft Identity Platform. È anche possibile usare la sicurezza Sprint per l'accesso dell'utente. È possibile fare riferimento all'esempio per i dettagli di implementazione completi.

# <a name="python"></a>[Python](#tab/python)

Gli esempi di codice di questo articolo e di quello successivo sono tratti dall'[applicazione Web Python che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), un esempio di app Web che usa MSAL.Python.
L'esempio consente attualmente a MSAL.Python di produrre l'URL del codice di autorizzazione e gestisce la navigazione all'endpoint di autorizzazione per Microsoft Identity Platform. È possibile fare riferimento all'esempio per i dettagli di implementazione completi.

---

## <a name="code-that-redeems-the-authorization-code"></a>Codice che riscatta il codice di autorizzazione

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web semplifica il codice specificando le impostazioni corrette di OpenID Connect, sottoscrivendo l'evento di ricezione del codice e riscattando il codice. Non è necessario alcun codice aggiuntivo per riscattare il codice di autorizzazione.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET gestisce gli elementi in modo analogo a ASP.NET Core, ad eccezione del fatto che la configurazione di OpenID Connect e la sottoscrizione all'evento `OnAuthorizationCodeReceived` si verificano nel file [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs). Anche i concetti sono simili a quelli in ASP.NET Core, ad eccezione del fatto che in ASP.NET è necessario specificare `RedirectUri` in [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Questa configurazione è leggermente meno affidabile rispetto a quella in ASP.NET Core poiché sarà necessario modificarla quando si distribuisce l'applicazione.

Di seguito è riportato il codice per Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Vedere [App Web che esegue l'accesso degli utenti: configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) per comprendere in che modo l'esempio Java ottiene il codice di autorizzazione. Dopo che l'app ha ricevuto il codice, [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delega al metodo `AuthHelper.processAuthenticationCodeRedirect` in [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Chiama `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

Il metodo `getAuthResultByAuthCode` è definito in [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Crea `ConfidentialClientApplication` MSAL e quindi chiama `acquireToken()` con `AuthorizationCodeParameters` creato dal codice di autorizzazione.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

Il flusso del codice di autorizzazione viene richiesto come illustrato in [App Web che esegue l'accesso degli utenti: configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Il codice viene quindi ricevuto nella funzione `authorized` che Flask instrada dall'URL `/getAToken`. Vedere [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) per il contesto completo di questo codice:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Anziché usare un segreto client, l'applicazione client riservata può anche dimostrare la propria identità usando un certificato client o un'asserzione client.
L'uso di asserzioni client è uno scenario avanzato descritto in [Asserzioni client](msal-net-client-assertions.md).

## <a name="token-cache"></a>Cache del token

> [!IMPORTANT]
> L'implementazione della cache dei token per le app Web o le API Web è diversa dall'implementazione per le applicazioni desktop che spesso è [basata su file](scenario-desktop-acquire-token.md#file-based-token-cache).
> Per motivi di sicurezza e prestazioni, è importante assicurarsi che per le app Web e le API Web esista una cache dei token per ogni account utente. È necessario serializzare la cache dei token per ogni account.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

L'esercitazione di ASP.NET Core usa l'inserimento delle dipendenze per consentire di decidere l'implementazione della cache dei token nel file Startup.cs per l'applicazione. Microsoft.Identity.Web include serializzatori di cache dei token predefiniti descritti in [Serializzazione della cache dei token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application). Una possibilità interessante è scegliere le [cache di memoria distribuita](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache) di ASP.NET Core:

```csharp
// Use a distributed token cache by adding:
    services.AddSignIn(Configuration, "AzureAd")
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Per informazioni dettagliate sui provider di cache di token, vedere anche la fase dell'esercitazione [Esercitazioni dell'app Web ASP.NET Core | Cache di token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

L'implementazione della cache dei token per le app Web o le API Web è diversa dall'implementazione per le applicazioni desktop che spesso è [basata su file](scenario-desktop-acquire-token.md#file-based-token-cache).

L'implementazione dell'app Web può usare la sessione ASP.NET o la memoria del server. Ad esempio, vedere il modo in cui l'implementazione della cache viene collegata dopo la creazione dell'applicazione MSAL.NET in [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java offre metodi per serializzare e deserializzare la cache dei token. L'esempio Java gestisce la serializzazione dalla sessione, come mostrato nel metodo `getAuthResultBySilentFlow` in [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Il dettaglio della classe `SessionManagementHelper` viene fornito nell'[esempio MSAL per Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

Nell'esempio Python viene garantita una cache per ogni account ricreando un'applicazione client riservata per ogni richiesta e quindi serializzandola nella cache della sessione Flask:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Passaggi successivi

A questo punto, quando l'utente esegue l'accesso, viene archiviato un token nella cache dei token. Viene ora descritto come viene usato in altre parti dell'app Web.

> [!div class="nextstepaction"]
> [Un'app Web che chiama le API Web: rimuovere gli account dalla cache per la disconnessione globale](scenario-web-app-call-api-sign-in.md)
