---
title: App Web che chiama le API Web (configurazione del codice)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app Web che chiama le API Web (configurazione del codice dell'app)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 391546b4d3ac9ad3674897b39284fdd16e9025a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562263"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>App Web che chiama API Web-configurazione del codice

Come illustrato nello [scenario degli utenti](scenario-web-app-sign-user-overview.md)per l'accesso all'app Web, dato che l'utente che esegue l'accesso è delegato al middleware Open ID Connect (OIDC), si vuole associare il processo OIDC. Il modo in cui eseguire questa operazione varia a seconda del Framework usato (in questo caso ASP.NET e ASP.NET Core), ma alla fine si eseguirà la sottoscrizione agli eventi OIDC del middleware. Il principio è che:

- Si consentirà a ASP.NET o ASP.NET Core di richiedere un codice di autorizzazione. Eseguendo questa operazione, ASP.NET/ASP.NET Core consentirà all'utente di accedere e acconsentire,
- Si sottoscriverà la ricezione del codice di autorizzazione dall'app Web.
- Quando si riceve il codice di autenticazione, si useranno le librerie MSAL per riscattare il codice e i token di accesso e i token di aggiornamento risultanti nella cache dei token. Da qui, la cache può essere usata in altre parti dell'applicazione per acquisire altri token in modo invisibile all'utente.

> [!NOTE]
> I frammenti di codice di questo articolo sono estratti dagli esempi seguenti in GitHub, che sono completamente funzionanti:
>
> - [Esercitazione incrementale di ASP.NET Core app Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)
> - [Esempio di app Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

## <a name="libraries-supporting-web-app-scenarios"></a>Librerie che supportano scenari di app Web

Le librerie che supportano il flusso del codice di autorizzazione per le app Web sono:

| Libreria MSAL | DESCRIZIONE |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Le piattaforme supportate sono .NET Framework e le piattaforme .NET Core, non UWP, Novell. iOS e Novell. Android, perché queste piattaforme vengono usate per compilare applicazioni client pubbliche. |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Sviluppo in corso-anteprima pubblica |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Sviluppo in corso-anteprima pubblica |

## <a name="aspnet-core-configuration"></a>Configurazione di ASP.NET Core

In ASP.NET Core, le operazioni vengono eseguite `Startup.cs` nel file. Si vuole sottoscrivere l' `OnAuthorizationCodeReceived` evento Open ID Connect e da questo evento chiamare MSAL. Metodo `AcquireTokenFromAuthorizationCode` di NET che ha l'effetto di archiviare nella cache dei token, il token di accesso per l' `scopes`oggetto richiesto e un token di aggiornamento che verrà usato per aggiornare il token di accesso quando è prossimo alla scadenza o per ottenere un token per conto dello stesso utente , ma per un'altra risorsa.

```CSharp
string[] scopes = new string[]{ "user.read" };
string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };
```

I commenti nel codice riportato di seguito consentiranno di comprendere alcuni aspetti complicati della tessitura di MSAL.NET e ASP.NET Core. I dettagli completi sono disponibili nell' [esercitazione incrementale ASP.NET Core app Web, capitolo 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add("offline_access");
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

In ASP.NET Core la creazione dell'applicazione client riservata usa le informazioni contenute in HttpContext. Conosce l'URL per l'app Web e l'utente che ha eseguito l'accesso (in un `ClaimsPrincipal`). `HttpContext` 

USA anche la configurazione ASP.NET Core, che include una sezione "AzureAD", e che è associata a:

- struttura di dati di tipo [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) `_applicationOptions`
- istanza di tipo [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) definita in ASP.NET Core `Authentication.AzureAD.UI`. `azureAdOptions` Infine, l'applicazione deve gestire le cache dei token.

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
 if (UserTokenCacheProvider != null)
 {
  UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (AppTokenCacheProvider != null)
 {
  AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

Per informazioni dettagliate sui provider di cache di token, vedere le esercitazioni sulle [app Web di ASP.NET Core | Cache di token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/455d32f09f4f6647b066ebee583f1a708376b12f/2-WebApp-graph-user/2-2-TokenCache)

> [!NOTE]
> `AcquireTokenByAuthorizationCode`Riscatta effettivamente il codice di autorizzazione richiesto da ASP.NET e ottiene i token aggiunti alla cache dei token utente di MSAL.NET. Da qui vengono usati nei controller di ASP.NET Core.

## <a name="aspnet-configuration"></a>Configurazione di ASP.NET

Il modo `OnAuthorizationCodeReceived` `App_Start\Startup.Auth.cs` in cui ASP.NET gestisce gli elementi è simile, ad eccezione del fatto che la configurazione di OpenIdConnect e la sottoscrizione all'evento si verifica nel file. Sono disponibili concetti simili, ad eccezione del fatto che qui è necessario specificare il RedirectUri nel file di configurazione, un po' meno affidabile:

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

Infine, anziché un segreto client, l'applicazione client riservata può anche dimostrare la propria identità usando un certificato client o un'asserzione client.
L'utilizzo di asserzioni client è uno scenario avanzato, descritto in dettaglio in [asserzioni client](msal-net-client-assertions.md)

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Cache dei token MSAL.NET per un'app Web ASP.NET (Core)

Nelle app Web (o API Web come in realtà), l'implementazione della cache dei token è diversa dalle implementazioni della cache dei token delle applicazioni desktop (spesso [basate su file](scenario-desktop-acquire-token.md#file-based-token-cache)). Può usare la sessione principale ASP.NET/ASP.NET, una cache Redis o un database o anche l'archivio BLOB di Azure. Nel frammento di codice precedente, si tratta dell'oggetto della `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` chiamata al metodo, che associa un servizio cache. Il dettaglio di ciò che accade qui esula dall'ambito di questa guida allo scenario, ma i collegamenti sono disponibili di seguito.

> [!IMPORTANT]
> Un aspetto molto importante da tenere presente è che, per le app Web e le API Web, deve essere presente una cache di token per ogni utente (per account). È necessario serializzare la cache dei token per ogni account.

Gli esempi di come usare le cache di token per le app Web e le API Web sono disponibili nell' [esercitazione ASP.NET Core app Web](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) nella [cache dei token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)della fase 2-2. Per le implementazioni, esaminare la cartella [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) nella libreria [microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (nella cartella [Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web)).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, quando l'utente accede a un token viene archiviato nella cache dei token. Vediamo come viene usato in altre parti dell'app Web.

> [!div class="nextstepaction"]
> [Accedere all'app Web](scenario-web-app-call-api-sign-in.md)
