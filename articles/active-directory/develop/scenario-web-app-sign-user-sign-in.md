---
title: App Web che consente agli utenti di accedere a Microsoft Identity Platform
description: Informazioni su come creare un'app Web per l'accesso degli utenti (accesso)
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
ms.openlocfilehash: 9766b530b4d795d0f35f097de20155cdd17687ca
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812387"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>App Web che consente agli utenti di accedere

Informazioni su come aggiungere l'accesso al codice per l'app Web che accede agli utenti.

## <a name="sign-in"></a>Accesso

Il codice esaminato nella [configurazione del codice dell'app](scenario-web-app-sign-user-app-configuration.md) precedente è sufficiente per implementare l'accesso.
Dopo che l'utente ha eseguito l'accesso all'app, è probabile che si voglia abilitarne la disconnessione. ASP.NET Core gestisce automaticamente la disconnessione.

## <a name="what-sign-out-involves"></a>Cosa implica la disconnessione

La disconnessione da un'app Web è più che rimuovere le informazioni sull'account che ha eseguito l'accesso dallo stato dell'app Web.
L'app Web deve anche reindirizzare l'utente all'endpoint della `logout` piattaforma Microsoft Identity per disconnettersi. Quando l'app Web reindirizza l'utente all' `logout` endpoint, questo endpoint Cancella la sessione dell'utente dal browser. Se l'app non è stata inviata `logout` all'endpoint, l'utente esegue di nuovo l'autenticazione all'app senza immettere di nuovo le credenziali, perché avrebbe una sessione Single Sign-in valida con l'endpoint della piattaforma di identità Microsoft.

Per altre informazioni, vedere la sezione [inviare una richiesta](v2-protocols-oidc.md#send-a-sign-out-request) di disconnessione nella [piattaforma di identità Microsoft e la documentazione concettuale del protocollo OpenID Connect](v2-protocols-oidc.md) .

## <a name="application-registration"></a>Registrazione dell'applicazione

Durante la registrazione dell'applicazione, è stato registrato un **URI di post-disconnessione**. In questa esercitazione è stata eseguita `https://localhost:44321/signout-oidc` la registrazione nel campo **URL di disconnessione** della sezione **Impostazioni avanzate** della pagina di **autenticazione** . Per informazioni dettagliate, vedere [registrare l'app webapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Codice ASP.NET Core

### <a name="signout-button"></a>Pulsante di disconnessione

Il pulsante di disconnessione viene esposto `Views\Shared\_LoginPartial.cshtml` in e visualizzato solo quando è presente un account autenticato, ovvero quando l'utente ha già eseguito l'accesso.

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`azione del`AccountController`

Quando si preme il pulsante di **disconnessione** nell'app Web `SignOut` , l'azione `Account` sul controller viene attivata. Nelle versioni precedenti dei modelli di ASP.NET Core, il `Account` controller era incorporato con l'app Web, ma questo non è più il caso che fa ora parte del framework ASP.NET Core stesso. 

Il codice per `AccountController` è disponibile dal repository ASP.NET Core in da [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Il controllo dell'account:

- Imposta un URI di reindirizzamento OpenID `/Account/SignedOut` su in modo che il controller venga richiamato quando Azure ad ha eseguito la disconnessione
- Chiama `Signout()`, che consente al middleware OpenIdConnect di contattare l'endpoint della `logout` piattaforma di identità Microsoft, che:

  - Cancella il cookie di sessione dal browser e
  - Chiama infine l'URL di **disconnessione**, che per impostazione predefinita Visualizza la pagina di visualizzazione di cui è stato effettuato l'accesso [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) , fornito anche come parte di ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Intercettazione della chiamata all' `logout` endpoint

Il middleware ASP.NET Core OpenIdConnect consente all'app di intercettare la chiamata all'endpoint della piattaforma `logout` di identità Microsoft fornendo un evento OpenIdConnect `OnRedirectToIdentityProviderForSignOut`denominato. L'app Web la usa per tentare di evitare la finestra di dialogo Seleziona account da presentare all'utente al momento della disconnessione. Questa intercettazione viene eseguita nell'oggetto `AddAzureAdV2Authentication` `Microsoft.Identity.Web` della libreria riutilizzabile. Vedere [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

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

In ASP.NET, la disconnessione viene attivata dal metodo Sign out () su un controller (ad esempio, AccountController). Questo metodo non fa parte del framework ASP.NET (contrariamente a quanto accade in ASP.NET Core) e non usa async ed è per questo motivo:

- Invia una richiesta di disconnessione OpenId
- Cancella la cache
- reindirizza alla pagina che desidera

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

Se non si vuole usare ASP.NET Core o ASP.NET, è possibile esaminare la documentazione del protocollo, disponibile in [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passa all'ambiente di produzione](scenario-web-app-sign-user-production.md)
