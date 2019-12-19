---
title: Configurare l'app Web che chiama le API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come configurare il codice di un'app Web che chiama API Web
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
ms.openlocfilehash: 83523fd12700789fb5c34230d529e06c0b284147
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964986"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>App Web che chiama API Web-configurazione del codice

Come illustrato nello [scenario degli utenti](scenario-web-app-sign-user-overview.md)di accesso all'app Web, l'app Web usa il flusso del [codice di autorizzazione](v2-oauth2-auth-code-flow.md) OAuth 2.0 per l'accesso dell'utente. Questo flusso è in due parti:

1. Richiedere un codice di autorizzazione. Questa parte delega alla piattaforma di identità Microsoft una finestra di dialogo privata con l'utente. L'utente accede e acconsente all'uso di API Web. Quando la finestra di dialogo privata viene terminata correttamente, l'applicazione riceve un codice di autorizzazione sul relativo URI di reindirizzamento.
1. Richiedere un token di accesso per l'API riscattando il codice di autorizzazione.

Lo [scenario degli utenti di accesso all'app Web](scenario-web-app-sign-user-overview.md) ha fatto solo la prima tappa. Informazioni su come modificare l'API Web per l'accesso degli utenti, in modo che ora chiami le API Web.

## <a name="libraries-supporting-web-app-scenarios"></a>Librerie che supportano scenari di app Web

Le librerie che supportano il flusso del codice di autorizzazione per le app Web sono:

| Libreria MSAL | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Le piattaforme supportate sono .NET Framework e le piattaforme .NET Core, non UWP, Novell. iOS e Novell. Android, perché queste piattaforme vengono usate per compilare applicazioni client pubbliche. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Sviluppo in corso-anteprima pubblica |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Sviluppo in corso-anteprima pubblica |

Selezionare la scheda corrispondente alla piattaforma a cui si è interessati:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dato che consentire a un utente di accedere è delegato al middleware Open ID Connect (OIDC), si vuole eseguire l'hook nel processo OIDC. Il modo in cui eseguire questa operazione è diverso a seconda del Framework in uso.
Nel caso di ASP.NET Core, si sottoscriveranno gli eventi middleware OIDC. Il principio è che:

- Si consentirà a ASP.NET Core di richiedere un codice di autorizzazione tramite il middleware Open ID Connect. Eseguendo questa operazione, ASP.NET/ASP.NET Core consentirà all'utente di accedere e acconsentire,
- Si sottoscriverà la ricezione del codice di autorizzazione dall'app Web. Questa sottoscrizione viene eseguita tramite un C# delegato.
- Quando viene ricevuto il codice di autorizzazione, si useranno le librerie MSAL per riscattare il codice e i token di accesso e i token di aggiornamento risultanti verranno archiviati nella cache dei token. Da qui, la cache può essere usata in altre parti dell'applicazione, ad esempio nei controller, per acquisire altri token in modo invisibile all'utente.

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall' [esercitazione incrementale ASP.NET Core app Web, capitolo 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). È possibile fare riferimento a questa esercitazione per i dettagli di implementazione completi.

> [!NOTE]
> Per comprendere completamente i frammenti di codice riportati di seguito, è necessario avere familiarità con [ASP.NET Core nozioni fondamentali](https://docs.microsoft.com/aspnet/core/fundamentals)e in particolare l' [inserimento delle dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) e le [Opzioni](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dato che consentire a un utente di accedere è delegato al middleware Open ID Connect (OIDC), si vuole eseguire l'hook nel processo OIDC. Il modo in cui eseguire questa operazione è diverso a seconda del Framework in uso.
Nel caso di ASP.NET, si sottoscriveranno gli eventi middleware OIDC. Il principio è che:

- Si consentirà a ASP.NET Core di richiedere un codice di autorizzazione tramite il middleware Open ID Connect. Eseguendo questa operazione, ASP.NET/ASP.NET Core consentirà all'utente di accedere e acconsentire,
- Si sottoscriverà la ricezione del codice di autorizzazione dall'app Web. Si tratta di C# un delegato.
- Quando si riceve il codice di autenticazione, si useranno le librerie MSAL per riscattare il codice. I token di accesso e i token di aggiornamento risultanti vengono quindi archiviati nella cache dei token. Da qui, la cache può essere usata in altre parti dell'applicazione, ad esempio nei controller, per acquisire altri token in modo invisibile all'utente.

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall' [esempio di app Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). È possibile fare riferimento a questo esempio per i dettagli di implementazione completi.

# <a name="javatabjava"></a>[Java](#tab/java)

I frammenti di codice in questo articolo e gli elementi seguenti vengono estratti dall' [applicazione Web Java che chiama](https://github.com/Azure-Samples/ms-identity-java-webapp) l'esempio di app Web Java MSAL di Microsoft Graph.
L'esempio attualmente consente a MSAL Java di produrre l'URL del codice di autorizzazione e gestisce la navigazione all'endpoint di autorizzazione della piattaforma di identità Microsoft. È anche possibile usare la sicurezza sprint per accedere all'utente. È possibile fare riferimento a questo esempio per i dettagli di implementazione completi.

# <a name="pythontabpython"></a>[Python](#tab/python)

I frammenti di codice in questo articolo e gli elementi seguenti vengono estratti dall' [applicazione Web Python che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Esempio di app Web Python.
L'esempio consente attualmente MSAL. Python produce l'URL del codice di autorizzazione e gestisce la navigazione all'endpoint di autorizzazione della piattaforma di identità Microsoft. È possibile fare riferimento a questo esempio per i dettagli di implementazione completi.

---

## <a name="code-that-redeems-the-authorization-code"></a>Codice che riscatta il codice di autorizzazione

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

In ASP.NET Core, il principio è che nel file di `Startup.cs`. È possibile sottoscrivere l'evento `OnAuthorizationCodeReceived` Open ID Connect e, da questo evento, chiamare MSAL. Il metodo di rete `AcquireTokenFromAuthorizationCode`, che ha l'effetto di archiviare nella cache dei token, il token di accesso per la `scopes`richiesta e un token di aggiornamento che verrà usato per aggiornare il token di accesso quando è prossimo alla scadenza o per ottenere un token per conto dello stesso utente, ma per un'altra risorsa.

In pratica, l' [esercitazione sull'app web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) tenta di fornire codice riutilizzabile per le app Web.

Ecco il codice [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) , con la chiamata al metodo `AddMicrosoftIdentityPlatformAuthentication` che aggiunge l'autenticazione all'app web e `AddMsal` che aggiunge la funzionalità di chiamata delle API Web. La chiamata a `AddInMemoryTokenCaches` sta per scegliere un'implementazione della cache dei token tra quelle possibili:

```CSharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead` è definito in [Constants. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```CSharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

È già stato studiato il contenuto di `AddMicrosoftIdentityPlatformAuthentication` nell' [app Web che consente agli utenti](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code) di accedere alla configurazione del codice

### <a name="the-addmsal-method"></a>Metodo AddMsal

Il codice per `AddMsal` si trova in [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```CSharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

Il metodo `AddMsal` garantisce che:

- l'app Web di ASP.NET Core richiede un IDToken per l'utente e un codice di autenticazione (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`)
- viene aggiunto l'ambito `offline_access`. È necessario in modo che l'utente acconsente a consentire all'applicazione di ottenere un token di aggiornamento.
- l'app sottoscrive l'evento OIDC `OnAuthorizationCodeReceived` e riscatta la chiamata usando MSAL.NET, che viene incapsulata in un componente riutilizzabile che implementa `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Metodo TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync

Il metodo `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` si trova in [Microsoft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Garantisce che:

- ASP.NET non tenta di riscattare il codice di autenticazione in parallelo a MSAL.NET (`context.HandleCodeRedemption();`)
- Le attestazioni in IDToken sono disponibili per MSAL per il calcolo di una chiave di cache del token per l'account dell'utente
- Se necessario, viene creata un'istanza dell'applicazione MSAL.NET
- il codice viene riscattato dall'applicazione MSAL.NET
- Il nuovo token ID viene condiviso con ASP.NET Core (durante la chiamata al `context.HandleCodeRedemption(null, result.IdToken);`). Il token di accesso non è condiviso con ASP.NET Core. Rimane nella cache dei token MSAL.NET associato all'utente, dove è pronto per essere usato nei controller ASP.NET Core.

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Metodo TokenAcquisition. BuildConfidentialClientApplication

In ASP.NET Core la creazione dell'applicazione client riservata usa le informazioni contenute in HttpContext. A cui si accede tramite la proprietà `CurrentHttpContext`, HttpContext, associato alla richiesta, conosce l'URL per l'app Web e l'utente che ha eseguito l'accesso (in una `ClaimsPrincipal`). Il `BuildConfidentialClientApplication` usa anche la configurazione ASP.NET Core, che include una sezione "AzureAD", e che è associata a:

- struttura di dati `_applicationOptions` di tipo [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)
- istanza `azureAdOptions` di tipo [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) definita in ASP.NET Core `Authentication.AzureAD.UI`. Infine, l'applicazione deve gestire le cache dei token. Per ulteriori informazioni, vedere la sezione successiva.

Il codice per il metodo `GetOrBuildConfidentialClientApplication()` si trova in [Microsoft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). USA i membri inseriti dall'inserimento delle dipendenze (passato nel costruttore di TokenAcquisition in [Microsoft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59))

```CSharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Summary

Per sommare, `AcquireTokenByAuthorizationCode` riscatta effettivamente il codice di autorizzazione richiesto da ASP.NET e ottiene i token aggiunti alla cache dei token utente di MSAL.NET. Da qui vengono usati nei controller di ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Il modo in cui ASP.NET gestisce le operazioni è simile a ASP.NET Core, ad eccezione del fatto che la configurazione di OpenIdConnect e la sottoscrizione all'evento `OnAuthorizationCodeReceived` si verificano nel file [app_start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Sono disponibili concetti simili come in ASP.NET Core, ad eccezione del fatto che in ASP.NET è necessario specificare RedirectUri nel [file Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Questa configurazione è leggermente meno affidabile rispetto a quanto avviene in ASP.NET Core, perché sarà necessario modificarla quando si distribuisce l'applicazione.

```CSharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
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
      // Upon successful sign in, get the access token & cache it using MSAL
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

# <a name="javatabjava"></a>[Java](#tab/java)

Vedere in [app Web che consente](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) di accedere alla configurazione del codice per comprendere come l'esempio Java ottiene il codice di autorizzazione. Una volta ricevuta dall'app, [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) delega al metodo `AuthHelper.processAuthenticationCodeRedirect` in [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)e quindi chiama `getAuthResultByAuthCode`:

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

Il metodo `getAuthResultByAuthCode` è definito in [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Viene creato un `ConfidentialClientApplication` MSAL e viene chiamato `acquireToken()` con `AuthorizationCodeParameters` creato dal codice di autorizzazione.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Una volta che il flusso del codice di autorizzazione è stato richiesto come visualizzato nell' [app Web che esegue l'accesso agli utenti-configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code), il codice di autorizzazione viene ricevuto sulla funzione `authorized` che Flask INSTRADA dall'URL/getAToken. Vedere [app. py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
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
L'utilizzo di asserzioni client è uno scenario avanzato, descritto in dettaglio in [asserzioni client](msal-net-client-assertions.md)

## <a name="token-cache"></a>Cache del token

> [!IMPORTANT]
> Nelle app Web (o API Web come in realtà), l'implementazione della cache dei token è diversa dalle implementazioni della cache dei token delle applicazioni desktop (spesso [basate su file](scenario-desktop-acquire-token.md#file-based-token-cache)).
> Per motivi di sicurezza e prestazioni, è importante assicurarsi che per le app Web e le API Web sia presente una cache dei token per ogni utente (per account). È necessario serializzare la cache dei token per ogni account.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

L'esercitazione ASP.NET Core usa l'inserimento delle dipendenze per consentire di decidere l'implementazione della cache dei token nel file Startup.cs per l'applicazione. Microsoft. Identity. Web viene fornita con diversi serializzatori della cache dei token predefiniti descritti in [serializzazione della cache dei token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Una possibilità interessante consiste nel scegliere ASP.NET Core [cache di memoria distribuita](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Per informazioni dettagliate sui provider di cache di token, vedere anche le [esercitazioni sulle app Web di ASP.NET Core | ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)Fase della cache dei token dell'esercitazione

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Nelle app Web (o API Web come in realtà), l'implementazione della cache dei token è diversa dalle implementazioni della cache dei token delle applicazioni desktop (spesso [basate su file](scenario-desktop-acquire-token.md#file-based-token-cache)). Può usare la sessione ASP.NET o la memoria del server. Vedere, ad esempio, il modo in cui l'implementazione della cache è collegata dopo la creazione dell'applicazione MSAL.NET in [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java fornisce metodi per serializzare e deserializzare la cache dei token. L'esempio Java gestisce la serializzazione dalla sessione, come illustrato nel metodo `getAuthResultBySilentFlow` in [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

I dettagli della classe `SessionManagementHelper` sono disponibili in [](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

Nell'esempio Python, nella cache per account viene garantita la ricreazione di un'applicazione client riservata per ogni richiesta e la relativa serializzazione nella cache della sessione Flask:

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

A questo punto, quando l'utente accede a un token viene archiviato nella cache dei token. Vediamo come viene usato in altre parti dell'app Web.

> [!div class="nextstepaction"]
> [Accedere all'app Web](scenario-web-app-call-api-sign-in.md)
