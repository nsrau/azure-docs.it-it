---
title: Configurare un'app Web che chiama le API Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come configurare il codice di un'app Web che chiama le API Web
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759161"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Un'app Web che chiama le API Web: Configurazione del codice

Come illustrato [nell'app Web che accede](scenario-web-app-sign-user-overview.md) allo scenario degli utenti, l'app Web usa il flusso del codice di [autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md) per accedere all'utente. Questo flusso prevede due passaggi:This flow has two steps:

1. Richiedere un codice di autorizzazione. Questa parte delega un dialogo privato con l'utente alla piattaforma di identità Microsoft.This part delegates a private dialogue with the user to the Microsoft identity platform. Durante tale dialogo, l'utente accede e acconsente all'uso delle API Web. Quando la finestra di dialogo privata termina correttamente, l'app Web riceve un codice di autorizzazione sul relativo URI di reindirizzamento.
1. Richiedere un token di accesso per l'API riscattando il codice di autorizzazione.

[L'app Web che accede agli](scenario-web-app-sign-user-overview.md) scenari degli utenti ha coperto solo il primo passaggio. Qui si apprenderà come modificare l'app Web in modo che non solo eseridi gli utenti, ma chiami anche le API Web.

## <a name="libraries-that-support-web-app-scenarios"></a>Librerie che supportano scenari di app Web

Le librerie seguenti nella libreria di autenticazione Microsoft (MSAL) supportano il flusso del codice di autorizzazione per le app Web:

| Libreria MSAL | Descrizione |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Supporto per piattaforme .NET Framework e .NET Core. Non sono supportate sono Universal Windows Platform (UWP), Xamarin.iOS e Xamarin.Android, perché tali piattaforme vengono utilizzate per creare applicazioni client pubbliche. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL per Python | Supporto per applicazioni web Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL per Java | Supporto per applicazioni Web Java. |

Seleziona la scheda della piattaforma che ti interessa:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Poiché l'accesso utente viene delegato al middleware Open ID connect (OIDC), è necessario interagire con il processo OIDC. La modalità di interazione dipende dal framework utilizzato.

Per ASP.NET Core, ti iscrivi agli eventi OIDC middleware:

- Si richiedere ASP.NET un codice di autorizzazione tramite il middleware Open ID Connect. ASP.NET o ASP.NET Core consentirà all'utente di accedere e acconsentire.
- Sottoscriverai l'app Web per ricevere il codice di autorizzazione. Questa sottoscrizione viene eseguita utilizzando un delegato di C.
- Quando il codice di autorizzazione viene ricevuto, si utilizzerà le librerie MSAL per riscattarlo. I token di accesso e i token di aggiornamento risultanti vengono archiviati nella cache dei token. La cache può essere utilizzata in altre parti dell'applicazione, ad esempio i controller, per acquisire altri token in modo invisibile all'utente.

Esempi di codice in questo articolo e quello seguente vengono estratti [dall'esercitazione incrementale dell'app Web ASP.NET Core, capitolo 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). È possibile fare riferimento a tale esercitazione per i dettagli di implementazione completa.

> [!NOTE]
> Per comprendere appieno gli esempi di codice qui, è necessario avere familiarità con [ASP.NET fondamentali di core](https://docs.microsoft.com/aspnet/core/fundamentals)e in particolare con l'inserimento di [dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) e [le opzioni](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Poiché l'accesso utente viene delegato al middleware Open ID connect (OIDC), è necessario interagire con il processo OIDC. La modalità di interazione dipende dal framework utilizzato.

Per ASP.NET, ti iscrivi agli eventi OIDC middleware:

- Si richiedere ASP.NET un codice di autorizzazione tramite il middleware Open ID Connect. ASP.NET o ASP.NET Core consentirà all'utente di accedere e acconsentire.
- Sottoscriverai l'app Web per ricevere il codice di autorizzazione. Questa sottoscrizione viene eseguita utilizzando un delegato di C.
- Quando il codice di autorizzazione viene ricevuto, si utilizzerà le librerie MSAL per riscattarlo. I token di accesso e i token di aggiornamento risultanti vengono archiviati nella cache dei token. La cache può essere utilizzata in altre parti dell'applicazione, ad esempio i controller, per acquisire altri token in modo invisibile all'utente.

Esempi di codice in questo articolo e quello seguente vengono estratti [dall'esempio di app Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). È possibile fare riferimento a tale esempio per i dettagli di implementazione completi.

# <a name="java"></a>[Java](#tab/java)

Esempi di codice in questo articolo e quello seguente vengono estratti [dall'applicazione Web Java che chiama Microsoft Graph,](https://github.com/Azure-Samples/ms-identity-java-webapp)un esempio di app Web che utilizza MSAL per Java.
L'esempio consente attualmente a MSAL per Java di produrre l'URL del codice di autorizzazione e gestisce la navigazione verso l'endpoint di autorizzazione per la piattaforma di identità Microsoft.The sample currently lets MSAL for Java produce the authorization-code URL and handles the navigation to the authorization endpoint for the Microsoft identity platform. È anche possibile utilizzare la sicurezza Sprint per accedere all'utente. È possibile fare riferimento all'esempio per i dettagli di implementazione completi.

# <a name="python"></a>[Python](#tab/python)

Esempi di codice in questo articolo e quello seguente vengono estratti [dall'applicazione Web Python che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), un esempio di app Web che utilizza MSAL. Python.
L'esempio consente attualmente MSAL. Python produce l'URL del codice di autorizzazione e gestisce la navigazione verso l'endpoint di autorizzazione per la piattaforma di identità Microsoft. È possibile fare riferimento all'esempio per i dettagli di implementazione completi.

---

## <a name="code-that-redeems-the-authorization-code"></a>Codice che riscatta il codice di autorizzazione

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

In ASP.NET Core, `Startup.cs` nel file, `OnAuthorizationCodeReceived` si sottoscrive l'evento OpenID Connect. Da questo evento, `AcquireTokenFromAuthorizationCode` chiamare il metodo MSAL.NET. Questo metodo archivia i token seguenti nella cache dei token:This method stores the following tokens in the token cache:

- Token *di accesso* `scopes`per l'oggetto .
- Un *token di aggiornamento*. Questo token verrà usato per aggiornare il token di accesso quando è vicino alla scadenza o per ottenere un altro token per conto dello stesso utente ma per una risorsa diversa.

[L'esercitazione ASP.NET'app Web di base](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) offre codice riutilizzabile per le app Web.

Di seguito è riportato il codice da [Startup.cs, L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). È dotato di chiamate a:

- Metodo `AddMicrosoftIdentityPlatformAuthentication` che aggiunge l'autenticazione all'app Web.
- Il `AddMsal` metodo, che aggiunge la capacità di chiamare le API Web.
- Il `AddInMemoryTokenCaches` metodo , che riguarda la scelta di un'implementazione della cache di token.

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

`Constants.ScopeUserRead`è definito in [Constants.cs:L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

È già stato studiato `AddMicrosoftIdentityPlatformAuthentication` il contenuto [dell'app Web che accede agli utenti - Configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Il metodo AddMsal

Il codice `AddMsal` per si trova in [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs-L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

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

Il `AddMsal` metodo garantisce che:

- L'app Web ASP.NET Core richiede sia un token`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`ID per l'utente che un codice di autenticazione ( ).
- L'ambito `offline_access` viene aggiunto. Questo ambito ottiene il consenso dell'utente per l'applicazione per ottenere un token di aggiornamento.
- L'app sottoscrive l'evento OIDC `OnAuthorizationCodeReceived` e riscatta la chiamata utilizzando MSAL.NET, che `ITokenAcquisition`viene incapsulato in un componente riutilizzabile che implementa .

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Il metodo TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync

Il `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` metodo si trova in [Microsoft.Identity.Web/TokenAcquisition.cs , L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Garantisce che:

- ASP.NET non tenta di riscattare il codice`context.HandleCodeRedemption();`di autenticazione in parallelo a MSAL.NET ( ).
- Le attestazioni nel token ID sono disponibili per MSAL per calcolare una chiave di cache del token per l'account dell'utente.
- Se necessario, viene creata un'istanza dell'applicazione MSAL.NET.
- Il codice viene riscattato dall'applicazione MSAL.NET.
- Il nuovo token ID viene condiviso con `context.HandleCodeRedemption(null, result.IdToken);`ASP.NET Core durante la chiamata a . Il token di accesso non è condiviso con ASP.NET Core.The access token is not shared with ASP.NET Core. Rimane nella cache dei token di MSAL.NET associata all'utente, dove è pronta per essere usata nei controller ASP.NET Core.

Ecco il codice pertinente per: `TokenAcquisition`

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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Il metodo TokenAcquisition.BuildConfidentialClientApplication

In ASP.NET Core, la compilazione dell'applicazione `HttpContext`client riservata utilizza le informazioni contenute nel file . L'accesso `HttpContext` alla richiesta associato `CurrentHttpContext` viene eseguito tramite la proprietà . `HttpContext`contiene informazioni sull'URL dell'app Web e sull'utente `ClaimsPrincipal`connesso (in un oggetto ). 

Il `BuildConfidentialClientApplication` metodo utilizza anche la configurazione di ASP.NET Core. La configurazione include una sezione "AzureAD" ed è associata anche a entrambi gli elementi seguenti:The configuration has an "AzureAD" section, and also is bound to both of the following elements:

- Struttura `_applicationOptions` dei dati di tipo [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- L'istanza `azureAdOptions` di tipo [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) `Authentication.AzureAD.UI`, definita in ASP.NET Core .

Infine, l'applicazione deve gestire le cache dei token. Scoprirai di più su questo nella sezione successiva.

Il codice `GetOrBuildConfidentialClientApplication()` per il metodo si trova in [Microsoft.Identity.Web/TokenAcquisition.cs.L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Vengono utilizzati i membri che sono stati inseriti `TokenAcquisition` mediante inserimento di dipendenze (passato nel costruttore di in [Microsoft.Identity.Web/TokenAcquisition.cs, L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Ecco il codice `GetOrBuildConfidentialClientApplication`per:

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

`AcquireTokenByAuthorizationCode`è in realtà il metodo che riscatta il codice di autorizzazione che ASP.NET richiede e che ottiene i token che vengono aggiunti alla cache di MSAL.NET token utente. Dalla cache, i token vengono quindi utilizzati nei controller ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET gestisce le operazioni in modo analogo a ASP.NET Core, `OnAuthorizationCodeReceived` ad eccezione del fatto che la configurazione di OpenID Connect e la sottoscrizione all'evento si verificano nel file [App_Start.Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) I concetti sono simili a quelli in ASP.NET Core, `RedirectUri` ad eccezione del fatto che in ASP.NET è necessario specificare il in [Web.config .](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15) Questa configurazione è un po'meno robusta di quella in ASP.NET Core, perché sarà necessario modificarla quando si distribuisce l'applicazione.

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

Vedere [Web app che accede agli utenti: configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) per comprendere come l'esempio Java ottiene il codice di autorizzazione. Dopo che l'app riceve il codice, [il file AuthFilter.java-L51-L56:](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56)

1. Delega al `AuthHelper.processAuthenticationCodeRedirect` metodo in [AuthHelper.java-L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
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

Il `getAuthResultByAuthCode` metodo è definito in [AuthHelper.java:L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Crea un MSAL `ConfidentialClientApplication`, `acquireToken()` quindi `AuthorizationCodeParameters` chiama con created dal codice di autorizzazione.

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

Il flusso del codice di autorizzazione viene richiesto come illustrato [nell'app Web che accede agli utenti: Configurazione del codice](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Il codice viene quindi `authorized` ricevuto sulla funzione, `/getAToken` che Flask instrada dall'URL. Vedere [app.py-L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) per il contesto completo di questo codice:

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

Anziché un segreto client, l'applicazione client riservata può anche dimostrare la propria identità utilizzando un certificato client o un'asserzione client.
L'utilizzo di asserzioni client è uno scenario avanzato, descritto in [Asserzioni client](msal-net-client-assertions.md).

## <a name="token-cache"></a>Cache del token

> [!IMPORTANT]
> L'implementazione della cache dei token per le app Web o le API Web è diversa dall'implementazione per le applicazioni desktop, che spesso [è basata su file](scenario-desktop-acquire-token.md#file-based-token-cache).
> Per motivi di sicurezza e prestazioni, è importante assicurarsi che per le app Web e le API Web sia presente una cache di token per ogni account utente. È necessario serializzare la cache dei token per ogni account.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

L'esercitazione di base di ASP.NET usa l'inserimento delle dipendenze per consentire di decidere l'implementazione della cache dei token nel file di Startup.cs per l'applicazione. Microsoft.Identity.Web viene fornito con serializzatori di cache token predefiniti descritti in [Serializzazione della cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)di token . Una possibilità interessante è quella di scegliere ASP.NET cache di [memoria distribuita](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)Core :

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

Per informazioni dettagliate sui provider di token-cache, vedere anche le esercitazioni di [ASP.NET Core Web app . Fase](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) di cache dei token dell'esercitazione.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

L'implementazione della cache dei token per le app Web o le API Web è diversa dall'implementazione per le applicazioni desktop, che spesso [è basata su file](scenario-desktop-acquire-token.md#file-based-token-cache).

L'implementazione dell'app Web può usare la sessione ASP.NET o la memoria del server. Vedere, ad esempio, come l'implementazione della cache viene collegata dopo la creazione dell'applicazione MSAL.NET in [MsalAppBuilder.cs, L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

MSAL Java fornisce metodi per serializzare e deserializzare la cache dei token. Nell'esempio Java viene gestita la serializzazione dalla sessione, come illustrato nel `getAuthResultBySilentFlow` metodo in [AuthHelper.java-L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

I dettagli `SessionManagementHelper` della classe sono forniti nell'esempio [MSAL per Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

Nell'esempio Python, una cache per account viene garantita ricreando un'applicazione client riservata per ogni richiesta e quindi serializzandola nella cache della sessione Flask:In the Python sample, one cache per account is ensured by recreating a confidential client application for each request and then serializing it in the Flask session cache:

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

A questo punto, quando l'utente accede, un token viene archiviato nella cache dei token. Vediamo come viene poi utilizzato in altre parti dell'app web.

> [!div class="nextstepaction"]
> [Un'app Web che chiama le API Web: rimuovere gli account dalla cache nella disconnessione globale](scenario-web-app-call-api-sign-in.md)
