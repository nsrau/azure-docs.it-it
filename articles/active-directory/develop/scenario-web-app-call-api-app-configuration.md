---
title: Configurare un'app Web che chiama API Web-piattaforma di identità Microsoft | Azure
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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759161"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Un'app Web che chiama le API Web: configurazione del codice

Come illustrato nell' [app Web che](scenario-web-app-sign-user-overview.md) esegue lo scenario degli utenti, l'app Web usa il [flusso del codice di autorizzazione OAuth 2,0](v2-oauth2-auth-code-flow.md) per accedere all'utente. Questo flusso è costituito da due passaggi:

1. Richiedere un codice di autorizzazione. Questa parte delega un dialogo privato con l'utente alla piattaforma di identità Microsoft. Durante questo dialogo, l'utente accede e acconsente all'uso di API Web. Quando il dialogo privato termina correttamente, l'app Web riceve un codice di autorizzazione sul relativo URI di reindirizzamento.
1. Richiedere un token di accesso per l'API riscattando il codice di autorizzazione.

L' [app Web che](scenario-web-app-sign-user-overview.md) esegue l'accesso agli scenari degli utenti ha coperto solo il primo passaggio. In questo articolo viene illustrato come modificare l'app Web in modo che non solo gli utenti vengano firmati ma che ora chiama anche le API Web.

## <a name="libraries-that-support-web-app-scenarios"></a>Librerie che supportano scenari di app Web

Le librerie seguenti in Microsoft Authentication Library (MSAL) supportano il flusso del codice di autorizzazione per le app Web:

| Libreria MSAL | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Supporto per le piattaforme .NET Framework e .NET Core. Non supportati sono piattaforma UWP (Universal Windows Platform) (UWP), Novell. iOS e Novell. Android, perché queste piattaforme vengono usate per compilare applicazioni client pubbliche. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL per Python | Supporto per le applicazioni Web Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL per Java | Supporto per le applicazioni Web Java. |

Selezionare la scheda per la piattaforma a cui si è interessati:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Poiché l'accesso utente viene delegato al middleware Open ID Connect (OIDC), è necessario interagire con il processo OIDC. La modalità di interazione dipende dal Framework usato.

Per ASP.NET Core, è necessario sottoscrivere gli eventi middleware OIDC:

- Si consentirà ASP.NET Core richiedere un codice di autorizzazione tramite il middleware Open ID Connect. ASP.NET o ASP.NET Core consentirà all'utente di accedere e acconsentire.
- L'app Web verrà sottoscritta per ricevere il codice di autorizzazione. Questa sottoscrizione viene eseguita utilizzando un C# delegato.
- Quando viene ricevuto il codice di autorizzazione, si useranno le librerie MSAL per riscattarlo. I token di accesso e i token di aggiornamento risultanti vengono archiviati nella cache dei token. La cache può essere usata in altre parti dell'applicazione, ad esempio i controller, per acquisire altri token in modo invisibile all'utente.

Gli esempi di codice in questo articolo e quelli seguenti sono estratti dall' [esercitazione incrementale ASP.NET Core app Web, capitolo 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). È possibile fare riferimento a questa esercitazione per i dettagli di implementazione completi.

> [!NOTE]
> Per comprendere completamente gli esempi di codice, è necessario avere familiarità con [ASP.NET Core nozioni fondamentali](https://docs.microsoft.com/aspnet/core/fundamentals)e in particolare con l' [inserimento di dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) e le [Opzioni](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Poiché l'accesso utente viene delegato al middleware Open ID Connect (OIDC), è necessario interagire con il processo OIDC. La modalità di interazione dipende dal Framework usato.

Per ASP.NET, si sottoscriveranno gli eventi middleware OIDC:

- Si consentirà ASP.NET Core richiedere un codice di autorizzazione tramite il middleware Open ID Connect. ASP.NET o ASP.NET Core consentirà all'utente di accedere e acconsentire.
- L'app Web verrà sottoscritta per ricevere il codice di autorizzazione. Questa sottoscrizione viene eseguita utilizzando un C# delegato.
- Quando viene ricevuto il codice di autorizzazione, si useranno le librerie MSAL per riscattarlo. I token di accesso e i token di aggiornamento risultanti vengono archiviati nella cache dei token. La cache può essere usata in altre parti dell'applicazione, ad esempio i controller, per acquisire altri token in modo invisibile all'utente.

Gli esempi di codice in questo articolo e quelli elencati di seguito sono estratti dall' [esempio di app Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). È possibile fare riferimento a tale esempio per i dettagli di implementazione completi.

# <a name="javatabjava"></a>[Java](#tab/java)

Gli esempi di codice in questo articolo e quelli riportati di seguito vengono estratti dall' [applicazione Web Java che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), un esempio di app Web che usa MSAL per Java.
L'esempio consente attualmente a MSAL per Java di produrre l'URL del codice di autorizzazione e gestisce la navigazione all'endpoint di autorizzazione per la piattaforma di identità Microsoft. È anche possibile usare la sicurezza sprint per accedere all'utente. È possibile fare riferimento all'esempio per i dettagli di implementazione completi.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gli esempi di codice in questo articolo e quelli seguenti vengono estratti dall' [applicazione Web Python che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), un esempio di app Web che usa MSAL. Python.
L'esempio consente attualmente MSAL. Python produce l'URL del codice di autorizzazione e gestisce la navigazione all'endpoint di autorizzazione per la piattaforma di identità Microsoft. È possibile fare riferimento all'esempio per i dettagli di implementazione completi.

---

## <a name="code-that-redeems-the-authorization-code"></a>Codice che riscatta il codice di autorizzazione

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

In ASP.NET Core, nel file di `Startup.cs`, si sottoscrive l'evento `OnAuthorizationCodeReceived` OpenID Connect. Da questo evento, chiamare il metodo MSAL.NET `AcquireTokenFromAuthorizationCode`. Questo metodo archivia i token seguenti nella cache dei token:

- Il *token di accesso* per la `scopes`richiesta.
- Un *token di aggiornamento*. Questo token verrà usato per aggiornare il token di accesso quando è prossimo alla scadenza o per ottenere un altro token per conto dello stesso utente, ma per una risorsa diversa.

L' [esercitazione sull'app web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) fornisce codice riutilizzabile per le app Web.

Di seguito è riportato il codice di [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Include le chiamate a:

- Il metodo `AddMicrosoftIdentityPlatformAuthentication`, che aggiunge l'autenticazione all'app Web.
- Il metodo `AddMsal`, che consente di aggiungere le funzionalità di chiamata delle API Web.
- Il metodo `AddInMemoryTokenCaches`, ovvero la scelta di un'implementazione della cache dei token.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead` è definito in [Constants. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

È già stato studiato il contenuto di `AddMicrosoftIdentityPlatformAuthentication` nell' [app Web che accede alla configurazione del codice degli utenti](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Metodo AddMsal

Il codice per `AddMsal` si trova in [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
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

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
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

- Il ASP.NET Core app Web richiede un token ID per l'utente e un codice di autenticazione (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- Viene aggiunto l'ambito `offline_access`. Questo ambito ottiene il consenso dell'utente per ottenere un token di aggiornamento da parte dell'applicazione.
- L'app sottoscrive l'evento OIDC `OnAuthorizationCodeReceived` e riscatta la chiamata usando MSAL.NET, che è incapsulata in un componente riutilizzabile che implementa `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Metodo TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync

Il metodo `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` si trova in [Microsoft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Garantisce che:

- ASP.NET non tenta di riscattare il codice di autenticazione in parallelo a MSAL.NET (`context.HandleCodeRedemption();`).
- Le attestazioni nel token ID sono disponibili per MSAL per calcolare una chiave di cache del token per l'account dell'utente.
- Se necessario, viene creata un'istanza dell'applicazione MSAL.NET.
- Il codice viene riscattato dall'applicazione MSAL.NET.
- Il nuovo token ID viene condiviso con ASP.NET Core durante la chiamata al `context.HandleCodeRedemption(null, result.IdToken);`. Il token di accesso non è condiviso con ASP.NET Core. Rimane nella cache dei token MSAL.NET associato all'utente, dove è pronto per essere usato nei controller ASP.NET Core.

Ecco il codice pertinente per `TokenAcquisition`:

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
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
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

In ASP.NET Core la creazione dell'applicazione client riservata usa le informazioni presenti nel `HttpContext`. Il `HttpContext` associato alla richiesta è accessibile tramite la proprietà `CurrentHttpContext`. `HttpContext` contiene informazioni sull'URL per l'app Web e sull'utente che ha eseguito l'accesso (in una `ClaimsPrincipal`). 

Il metodo `BuildConfidentialClientApplication` usa anche la configurazione ASP.NET Core. La configurazione include una sezione "AzureAD" ed è associata anche a entrambi gli elementi seguenti:

- Struttura di dati `_applicationOptions` di tipo [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- Istanza `azureAdOptions` di tipo [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs), definita in ASP.NET Core `Authentication.AzureAD.UI`.

Infine, l'applicazione deve gestire le cache dei token. Per ulteriori informazioni, vedere la sezione successiva.

Il codice per il metodo `GetOrBuildConfidentialClientApplication()` si trova in [Microsoft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). USA i membri inseriti dall'inserimento delle dipendenze (passato nel costruttore di `TokenAcquisition` in [Microsoft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Ecco il codice per `GetOrBuildConfidentialClientApplication`:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
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

### <a name="summary"></a>Riepilogo

`AcquireTokenByAuthorizationCode` è effettivamente il metodo che riscatta il codice di autorizzazione richiesto da ASP.NET e che ottiene i token aggiunti alla cache del token utente MSAL.NET. Dalla cache, i token vengono quindi utilizzati nei controller di ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET gestisce elementi in modo analogo a ASP.NET Core, ad eccezione del fatto che la configurazione di OpenID Connect e la sottoscrizione all'evento `OnAuthorizationCodeReceived` si verificano nel file [app_start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . I concetti sono simili anche a quelli in ASP.NET Core, ad eccezione del fatto che in ASP.NET è necessario specificare il `RedirectUri` in [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Questa configurazione è leggermente meno affidabile rispetto a quella in ASP.NET Core, perché sarà necessario modificarla quando si distribuisce l'applicazione.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Vedere [app Web per l'accesso degli utenti: configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) per comprendere come l'esempio Java ottiene il codice di autorizzazione. Dopo che l'app ha ricevuto il codice, [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delega al metodo `AuthHelper.processAuthenticationCodeRedirect` in [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
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

Il metodo `getAuthResultByAuthCode` è definito in [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Viene creato un `ConfidentialClientApplication`MSAL, quindi viene chiamato `acquireToken()` con `AuthorizationCodeParameters` creato dal codice di autorizzazione.

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

Il flusso del codice di autorizzazione viene richiesto come illustrato nell' [app Web che esegue l'accesso agli utenti: configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Il codice viene quindi ricevuto nella funzione `authorized`, che Flask instrada dall'URL del `/getAToken`. Per il contesto completo del codice, vedere [app. py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) :

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

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

L'esercitazione ASP.NET Core usa l'inserimento delle dipendenze per consentire di decidere l'implementazione della cache dei token nel file Startup.cs per l'applicazione. Microsoft. Identity. Web è dotato di serializzatori di cache dei token predefiniti descritti in [serializzazione della cache dei token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Una possibilità interessante consiste nel scegliere ASP.NET Core [cache di memoria distribuita](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

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

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java fornisce metodi per serializzare e deserializzare la cache dei token. L'esempio Java gestisce la serializzazione dalla sessione, come illustrato nel metodo `getAuthResultBySilentFlow` in [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

Il dettaglio della classe `SessionManagementHelper` viene fornito nell' [esempio MSAL per Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="pythontabpython"></a>[Python](#tab/python)

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
