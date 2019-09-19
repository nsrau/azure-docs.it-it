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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086466"
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

### <a name="application-registration"></a>Registrazione dell'applicazione

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Durante la registrazione dell'applicazione, è stato registrato un **URI di post-disconnessione**. In questa esercitazione è stata eseguita `https://localhost:44321/signout-oidc` la registrazione nel campo **URL di disconnessione** della sezione **Impostazioni avanzate** della pagina di **autenticazione** . Per informazioni dettagliate, vedere [registrare l'app webapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Durante la registrazione dell'applicazione, è stato registrato un **URI di post-disconnessione**. In questa esercitazione è stata eseguita `https://localhost:44308/Account/EndSession` la registrazione nel campo **URL di disconnessione** della sezione **Impostazioni avanzate** della pagina di **autenticazione** . Per informazioni dettagliate, vedere [registrare l'app webapp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="javatabjava"></a>[Java](#tab/java)

Durante la registrazione dell'applicazione verrà registrato un **URI di post-disconnessione**. In questa esercitazione è stata eseguita `http://localhost:8080/msal4jsample/` la registrazione nel campo **URL di disconnessione** della sezione **Impostazioni avanzate** della pagina di **autenticazione** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Durante la registrazione dell'applicazione non è necessario registrare un URL di disconnessione. L'esempio non implementa la disconnessione globale

---

### <a name="sign-out-button"></a>Pulsante di disconnessione

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core il pulsante di disconnessione viene esposto in `Views\Shared\_LoginPartial.cshtml` e visualizzato solo quando è presente un account autenticato, ovvero quando l'utente ha già eseguito l'accesso.

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC, il pulsante di disconnessione viene esposto in `Views\Shared\_LoginPartial.cshtml` e viene visualizzato solo quando è presente un account autenticato, ovvero quando l'utente ha già eseguito l'accesso.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Nella Guida introduttiva di Java il pulsante di disconnessione si trova nel file main/resources/templates/auth_page.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Nella Guida introduttiva di Python il pulsante di disconnessione si trova nel file [templates/display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`azione del controller

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET, se si preme il pulsante di **disconnessione** nell'app Web, `SignOut` l'azione sul `AccountController` controller viene attivata. Nelle versioni precedenti dei modelli di ASP.NET Core, il `Account` controller era incorporato con l'app Web, ma non è più il caso che fa ora parte del framework ASP.NET Core stesso.

Il codice per `AccountController` è disponibile dal repository ASP.NET Core in da [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Il controllo dell'account:

- Imposta un URI di reindirizzamento OpenID `/Account/SignedOut` su in modo che il controller venga richiamato quando Azure ad ha eseguito la disconnessione
- Chiama `Signout()`, che consente al middleware OpenIdConnect di contattare l'endpoint della `logout` piattaforma di identità Microsoft, che:

  - Cancella il cookie di sessione dal browser e
  - richiama infine l' **URL di disconnessione**, che, per impostazione predefinita, Visualizza la pagina di visualizzazione di cui è stato effettuato l'accesso [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) fornito anche come parte del ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, la `SignOut()` disconnessione viene attivata dal metodo su un controller (ad esempio, AccountController). Questo metodo non fa parte del framework ASP.NET (contrariamente a quanto accade in ASP.NET Core). Non usa `async`ed è per questo motivo:

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
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

In Java la disconnessione viene gestita chiamando direttamente l'endpoint di disconnessione della piattaforma Microsoft Identity e specificando il post_logout_redirect_uri.

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Intercettazione della chiamata all' `logout` endpoint

L'URI post-disconnessione consente alle applicazioni di partecipare alla disconnessione globale.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Il middleware ASP.NET Core OpenIdConnect consente all'app di intercettare la chiamata all'endpoint della piattaforma `logout` di identità Microsoft fornendo un evento OpenIdConnect `OnRedirectToIdentityProviderForSignOut`denominato. Vedere [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) per un esempio di come sottoscrivere questo evento (per cancellare la cache dei token)

```CSharp
               // Handling the global sign-out
                options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
                {
                    // Forget about the signed-in user
                };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET si delega al middleware per eseguire la disconnessione, cancellando il cookie di sessione:

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Nella Guida introduttiva di Java, l'URI di reindirizzamento post-disconnessione Visualizza solo la pagina index. html 

# <a name="pythontabpython"></a>[Python](#tab/python)

Nella Guida introduttiva di Python, l'URI di reindirizzamento post-disconnessione Visualizza solo la pagina index. html.

---

## <a name="protocol"></a>Protocol

Per altre informazioni sulla disconnessione, vedere la documentazione del protocollo, disponibile in [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passa all'ambiente di produzione](scenario-web-app-sign-user-production.md)
