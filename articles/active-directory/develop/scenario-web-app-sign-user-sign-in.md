---
title: App Web di accesso degli utenti (accesso) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'app web dagli utenti segni-in (Accedi)
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074621"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Web app che consente agli utenti - Accedi

Informazioni su come aggiungere l'accesso al codice per l'app web che gli utenti esegue l'accesso.

## <a name="sign-in"></a>Accesso

Il codice che abbiamo nell'articolo precedente [configurazione del codice dell'app](scenario-web-app-sign-user-app-configuration.md) è sufficiente implementare disconnessione. Una volta che l'utente ha eseguito l'accesso aggiuntivo all'App, consigliabile consentire loro di disconnettersi. ASP.NET core gestisce disconnessione per l'utente.

## <a name="what-sign-out-involves"></a>Che cosa disconnettersi implica

Disconnessione da un'app web è su più di rimuovere le informazioni relative all'account eseguito dallo stato dell'app web.
L'app web anche necessario reindirizzare l'utente a Microsoft identity platform 2.0 `logout` endpoint per la disconnessione. Quando l'app web reindirizza l'utente per il `logout` endpoint, questo endpoint Cancella la sessione dell'utente dal browser. Se l'app non andare per il `logout` endpoint, l'utente potrebbe ripetere l'autenticazione all'App senza dover immettere nuovamente le proprie credenziali perché hanno sarebbe una sessione single valida Accedi con l'endpoint 2.0 di Microsoft Identity platform.

Per altre informazioni, vedere la [invia una richiesta di disconnessione](v2-protocols-oidc.md#send-a-sign-out-request) sezione il [Microsoft Identity platform v2.0 e il protocollo OpenID Connect](v2-protocols-oidc.md) documentazione concettuale.

## <a name="application-registration"></a>Registrazione dell'applicazione

Durante la registrazione dell'applicazione, verrà registrato un **post URI di disconnessione**. Nel corso di questa esercitazione è registrato `https://localhost:44321/signout-oidc` nel **Logout URL** campo il **impostazioni avanzate** sezione la **autenticazione** pagina. Per informazioni dettagliate, vedere [ registrare l'app di App Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Codice di ASP.NET Core

### <a name="signout-button"></a>Pulsante di disconnessione

Il pulsante di disconnessione viene esposto `Views\Shared\_LoginPartial.cshtml` e visualizzata solo quando è presente un account autenticato (ad esempio quando l'utente ha precedentemente eseguito l'accesso).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` azione del `AccountController`

Premendo il **disconnettersi** pulsante sui trigger di app web di `SignOut` azione nel `Account` controller. Nelle versioni precedenti dei modelli ASP.NET core, il `Account` controller è stato incorporato con l'app web, ma questo non è più il caso che è ora parte del framework di ASP.NET Core stesso. 

Il codice per il `AccountController` è disponibile dal repository ASP.NET core dal [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Il controllo dell'account:

- URI di reindirizzamento set un'OpenID `/Account/SignedOut` in modo che il controller viene richiamato quando Azure AD ha eseguito la disconnessione
- Le chiamate `Signout()`, che consente il middleware openid Connect contattare la piattaforma delle identità Microsoft `logout` endpoint che:

  - Cancella il cookie di sessione dal browser, e
  - Chiama infine richiama il **URL di disconnessione**, che) per impostazione predefinita, viene visualizzato con il segno pagina visualizzazione [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) forniti come parte di ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Intercettazione della chiamata al `logout` endpoint

Il middleware openid Connect di ASP.NET Core consente all'app di intercettare la chiamata alla piattaforma delle identità Microsoft `logout` endpoint fornendo un evento di openid Connect denominato `OnRedirectToIdentityProviderForSignOut`. L'app web lo usa per tentare di evitare la finestra di dialogo Selezione account devono essere presentati all'utente quando la disconnessione. L'intercettazione avviene nella `AddAzureAdV2Authentication` del `Microsoft.Identity.Web` libreria riutilizzabile. Vedere [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>Codice ASP.NET

In ASP.NET, la disconnessione viene generata dal metodo SignOut () in un Controller (ad esempio AccountController). Questo metodo non fa parte del framework di ASP.NET (contrariamente a quanto accade in ASP.NET Core) e non usa l'approccio asincrono e per tale motivo è:

- Invia una richiesta di disconnessione di OpenId
- Cancella la cache
- Reindirizza alla pagina di cui che vuole

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

Se non si desidera usare ASP.NET Core o ASP.NET, è possibile esaminare la documentazione del protocollo, che è disponibile dal [Openid Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare alla produzione](scenario-web-app-sign-user-production.md)
