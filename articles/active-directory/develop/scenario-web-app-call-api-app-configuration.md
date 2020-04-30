---
title: Configurare un'app Web che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come configurare il codice di un'app Web che chiama API Web
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
ms.openlocfilehash: 68f6f8ec67aca44c89b338287bdd37b6066992e0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207021"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Un'app Web che chiama le API Web: configurazione del codice

Come illustrato nell' [app Web che](scenario-web-app-sign-user-overview.md) esegue lo scenario degli utenti, l'app Web usa il [flusso del codice di autorizzazione OAuth 2,0](v2-oauth2-auth-code-flow.md) per accedere all'utente. Questo flusso è costituito da due passaggi:

1. Richiedere un codice di autorizzazione. Questa parte delega un dialogo privato con l'utente alla piattaforma di identità Microsoft. Durante questo dialogo, l'utente accede e acconsente all'uso di API Web. Quando il dialogo privato termina correttamente, l'app Web riceve un codice di autorizzazione sul relativo URI di reindirizzamento.
1. Richiedere un token di accesso per l'API riscattando il codice di autorizzazione.

L' [app Web che](scenario-web-app-sign-user-overview.md) esegue l'accesso agli scenari degli utenti ha coperto solo il primo passaggio. In questo articolo viene illustrato come modificare l'app Web in modo che non solo gli utenti vengano firmati ma che ora chiama anche le API Web.

## <a name="libraries-that-support-web-app-scenarios"></a>Librerie che supportano scenari di app Web

Le librerie seguenti in Microsoft Authentication Library (MSAL) supportano il flusso del codice di autorizzazione per le app Web:

| Libreria MSAL | Descrizione |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Supporto per le piattaforme .NET Framework e .NET Core. Non supportati sono piattaforma UWP (Universal Windows Platform) (UWP), Novell. iOS e Novell. Android, perché queste piattaforme vengono usate per compilare applicazioni client pubbliche. Per ASP.NET Core app Web e le API Web, MSAL.NET è incapsulato in una libreria di livello superiore denominata Microsoft. Identity. Web|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL per Python | Supporto per le applicazioni Web Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL per Java | Supporto per le applicazioni Web Java. |

Selezionare la scheda per la piattaforma a cui si è interessati:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Per consentire all'app Web di chiamare le API protette quando si usa Microsoft. Identity. Web, è necessario solo `AddWebAppCallsProtectedWebApi` chiamare e specificare un formato di serializzazione della cache dei token (ad esempio, cache dei token in memoria):

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

Per ulteriori informazioni sulla cache dei token, vedere [Opzioni di serializzazione della cache dei token](#token-cache) .

> [!NOTE]
> Per comprendere completamente gli esempi di codice, è necessario avere familiarità con [ASP.NET Core nozioni fondamentali](https://docs.microsoft.com/aspnet/core/fundamentals)e in particolare con l' [inserimento di dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) e le [Opzioni](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Poiché l'accesso utente viene delegato al middleware Open ID Connect (OIDC), è necessario interagire con il processo OIDC. La modalità di interazione dipende dal Framework usato.

Per ASP.NET, si sottoscriveranno gli eventi middleware OIDC:

- Si consentirà ASP.NET Core richiedere un codice di autorizzazione tramite il middleware Open ID Connect. ASP.NET o ASP.NET Core consentirà all'utente di accedere e acconsentire.
- L'app Web verrà sottoscritta per ricevere il codice di autorizzazione. Questa sottoscrizione viene eseguita usando un delegato C#.
- Quando viene ricevuto il codice di autorizzazione, si useranno le librerie MSAL per riscattarlo. I token di accesso e i token di aggiornamento risultanti vengono archiviati nella cache dei token. La cache può essere usata in altre parti dell'applicazione, ad esempio i controller, per acquisire altri token in modo invisibile all'utente.

Gli esempi di codice in questo articolo e quelli elencati di seguito sono estratti dall' [esempio di app Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). È possibile fare riferimento a tale esempio per i dettagli di implementazione completi.

# <a name="java"></a>[Java](#tab/java)

Gli esempi di codice in questo articolo e quelli riportati di seguito vengono estratti dall' [applicazione Web Java che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), un esempio di app Web che usa MSAL per Java.
L'esempio consente attualmente a MSAL per Java di produrre l'URL del codice di autorizzazione e gestisce la navigazione all'endpoint di autorizzazione per la piattaforma di identità Microsoft. È anche possibile usare la sicurezza sprint per accedere all'utente. È possibile fare riferimento all'esempio per i dettagli di implementazione completi.

# <a name="python"></a>[Python](#tab/python)

Gli esempi di codice in questo articolo e quelli seguenti vengono estratti dall' [applicazione Web Python che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), un esempio di app Web che usa MSAL. Python.
L'esempio consente attualmente MSAL. Python produce l'URL del codice di autorizzazione e gestisce la navigazione all'endpoint di autorizzazione per la piattaforma di identità Microsoft. È possibile fare riferimento all'esempio per i dettagli di implementazione completi.

---

## <a name="code-that-redeems-the-authorization-code"></a>Codice che riscatta il codice di autorizzazione

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web semplifica il codice impostando le impostazioni corrette di OpenID Connect, sottoscrivendo l'evento di ricezione del codice e riscattando il codice. Non è necessario alcun codice aggiuntivo per riscattare il codice di autorizzazione.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET gestisce elementi in modo analogo a ASP.NET Core, ad eccezione del fatto che la configurazione di OpenID `OnAuthorizationCodeReceived` Connect e la sottoscrizione all'evento si verificano nel file [app_start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . I concetti sono simili anche a quelli in ASP.NET Core, ad eccezione del fatto che in ASP.NET è `RedirectUri` necessario specificare in [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Questa configurazione è leggermente meno affidabile rispetto a quella in ASP.NET Core, perché sarà necessario modificarla quando si distribuisce l'applicazione.

Ecco il codice per Startup.Auth.cs:

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

Vedere [app Web per l'accesso degli utenti: configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) per comprendere come l'esempio Java ottiene il codice di autorizzazione. Dopo che l'app ha ricevuto il codice, [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delega al `AuthHelper.processAuthenticationCodeRedirect` metodo in [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
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

Il `getAuthResultByAuthCode` metodo è definito in [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Viene creato un MSAL `ConfidentialClientApplication`, quindi viene chiamato `acquireToken()` con `AuthorizationCodeParameters` creato dal codice di autorizzazione.

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

Il flusso del codice di autorizzazione viene richiesto come illustrato nell' [app Web che esegue l'accesso agli utenti: configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Il codice viene quindi ricevuto sulla `authorized` funzione, che Flask instrada dall' `/getAToken` URL. Per il contesto completo del codice, vedere [app. py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) :

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

Anziché un segreto client, l'applicazione client riservata può anche dimostrare la propria identità usando un certificato client o un'asserzione client.
L'utilizzo di asserzioni client è uno scenario avanzato, descritto in dettaglio in [asserzioni client](msal-net-client-assertions.md).

## <a name="token-cache"></a>Cache del token

> [!IMPORTANT]
> L'implementazione della cache dei token per app Web o API Web è diversa dall'implementazione per le applicazioni desktop, che è spesso [basata su file](scenario-desktop-acquire-token.md#file-based-token-cache).
> Per motivi di sicurezza e prestazioni, è importante assicurarsi che per le app Web e le API Web esista una cache dei token per ogni account utente. È necessario serializzare la cache dei token per ogni account.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

L'esercitazione ASP.NET Core usa l'inserimento delle dipendenze per consentire di decidere l'implementazione della cache dei token nel file Startup.cs per l'applicazione. Microsoft. Identity. Web è dotato di serializzatori di cache dei token predefiniti descritti in [serializzazione della cache dei token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Una possibilità interessante consiste nel scegliere ASP.NET Core [cache di memoria distribuita](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddSignIn(Configuration, "AzureAd");
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

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

Per informazioni dettagliate sui provider di cache di token, vedere anche le [esercitazioni sulle app Web di ASP.NET Core | ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)Fase della cache dei token dell'esercitazione.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

L'implementazione della cache dei token per app Web o API Web è diversa dall'implementazione per le applicazioni desktop, che è spesso [basata su file](scenario-desktop-acquire-token.md#file-based-token-cache).

L'implementazione di app Web può usare la sessione ASP.NET o la memoria del server. Vedere ad esempio il modo in cui l'implementazione della cache viene collegata dopo la creazione dell'applicazione MSAL.NET in [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

MSAL Java fornisce metodi per serializzare e deserializzare la cache dei token. L'esempio Java gestisce la serializzazione dalla sessione, come illustrato nel `getAuthResultBySilentFlow` metodo in [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

I dettagli della `SessionManagementHelper` classe sono disponibili nell' [esempio MSAL per Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

Nell'esempio Python, una cache per ogni account viene garantita ricreando un'applicazione client riservata per ogni richiesta e quindi serializzando la cache nella cache della sessione Flask:

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

A questo punto, quando l'utente accede, un token viene archiviato nella cache dei token. Vediamo come viene usato in altre parti dell'app Web.

> [!div class="nextstepaction"]
> [Un'app Web che chiama le API Web: rimuovere gli account dalla cache nella disconnessione globale](scenario-web-app-call-api-sign-in.md)
