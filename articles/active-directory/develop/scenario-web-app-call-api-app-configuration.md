---
title: App Web che chiama web API (configurazione del codice) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'app Web che chiama web API (configurazione del codice dell'app)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c78a951258e3c279f96f44ceac469e4c38cf22c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785563"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>App Web che chiama l'API - configurazione del codice web

Come illustrato nel [scenario esegue l'accesso agli utenti di app Web](scenario-web-app-sign-user-overview.md), dato che l'accesso utente viene delegata a Open ID connect (OIDC) middleware, si desidera verticale hook nel processo di OIDC. Il modo per eseguire questa operazione è diverso a seconda del framework è stato usato in questo caso ASP.NET e ASP.NET Core, ma alla fine, è possibile sottoscrivere gli eventi di middleware OIDC. Si basa sul principio che:

- Si riceverà una ASP.NET o ASP.NET core richiedere un codice di autorizzazione. In questo modo verranno core ASP.NET/ASP.NET consentire all'utente di accedere e fornire il consenso,
- È possibile sottoscrivere la ricezione del codice di autorizzazione dall'app Web.
- Quando viene ricevuto il codice di autorizzazione, si userà le librerie MSAL per riscattare il codice e i token di accesso risultante e archivio di token nella cache dei token di aggiornamento. Da qui, la cache è utilizzabile in altre parti dell'applicazione per acquisire altri token invisibile all'utente.

## <a name="libraries-supporting-web-app-scenarios"></a>Librerie che supportano scenari di App Web

Le librerie che supportano il flusso del codice di autorizzazione per le app Web sono:

| Libreria MSAL | DESCRIZIONE |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Piattaforme supportate sono le piattaforme .NET Framework e .NET Core (non UWP, xamarin. IOS e xamarin. Android come tali piattaforme vengono utilizzati per compilare applicazioni client pubblico) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Sviluppo in corso - disponibile in anteprima pubblica |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Sviluppo in corso - disponibile in anteprima pubblica |

## <a name="aspnet-core-configuration"></a>Configurazione di ASP.NET Core

In ASP.NET Core, in cui vengono eseguite operazioni di `Startup.cs` file. È opportuno sottoscrivere il `OnAuthorizationCodeReceived` aprire l'ID evento connect e da questo evento, chiamare MSAL. Metodo di NET `AcquireTokenFromAuthorizationCode` che ha l'effetto di memorizzazione nella cache dei token, token di accesso per gli ambiti richiesti e un token di aggiornamento che verrà utilizzato per aggiornare il token di accesso quando è nelle scadenza, o per ottenere un token per conto dell'utente stesso , ma per un'altra risorsa.

I commenti nel codice seguente consentirà di comprendere alcuni aspetti complesse di Componi MSAL.NET e ASP.NET Core. Vengono forniti i dettagli completi nel [Web ASP.NET Core app incrementale dell'esercitazione, capitolo 2:](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

In ASP.NET Core, la compilazione dell'applicazione client riservato utilizza le informazioni che si trova in HttpContext. Questo HttpContext conosce l'URL per l'App Web e l'utente connesso (in un `ClaimsPrincipal`). Usa anche la configurazione di ASP.NET Core, con una sezione "AzureAD", e che è associato ai `_applicationOptions` struttura dei dati. Infine l'applicazione deve gestire token memorizzati nella cache.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` veramente Riscatta il codice di autorizzazione richiesto da ASP.NET e ottiene i token che vengono aggiunti alla cache dei token utente MSAL.NET. Da qui, risultano quindi usato, il controller di ASP.NET Core.

## <a name="aspnet-configuration"></a>Configurazione di ASP.NET

Il modo in cui ASP.NET gestisce cose è simile, ad eccezione del fatto che la configurazione di openid Connect e la sottoscrizione per il `OnAuthorizationCodeReceived` evento si verifica nel `App_Start\Startup.Auth.cs` file. Sono disponibili concetti simili, ad eccezione del fatto che in questo caso è necessario specificare l'URI di reindirizzamento nel file di configurazione, che è un po' meno affidabile:

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Cache dei Token MSAL.NET per un'app Web ASP.NET (Core)

Nell'App web (o web API come una questione di fatto), l'implementazione della cache dei token è diversa dalle implementazioni della cache dei token applicazioni Desktop (che sono spesso [basati su file](scenario-desktop-acquire-token.md#file-based-token-cache). È possibile usare la sessione ASP.NET/ASP.NET Core, o una cache Redis, o un database o persino nell'archivio Blob di Azure. Nel codice di frammento di codice di sopra di questo è l'oggetto del `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` chiamata al metodo, che esegue l'associazione di un servizio cache. I dettagli di ciò che succede qui non rientra nell'ambito della Guida in questo scenario, ma sono disponibili collegamenti sotto.

> [!IMPORTANT]
> Un aspetto molto importante da tenere presente è che per le app web e API web, deve essere una cache dei token per ogni utente (per ogni account). È necessario serializzare la cache dei token per ogni account.

Esempi di come usare i token memorizzati nella cache per le app Web e API web sono disponibili nel [esercitazione sulle app Web ASP.NET Core](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) nella fase [Cache dei Token 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Per le implementazioni, esaminare la cartella [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) nella libreria [microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (nella cartella [Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web)).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, quando l'utente accede un token viene memorizzato nella cache dei token. Di seguito viene illustrato come viene quindi usato in altre parti dell'app Web.

> [!div class="nextstepaction"]
> [Accedi all'App Web](scenario-web-app-call-api-sign-in.md)
