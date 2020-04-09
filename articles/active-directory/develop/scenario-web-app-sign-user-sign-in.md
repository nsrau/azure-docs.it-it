---
title: Scrivere un'app Web che esegue l'accesso/esterno degli utenti - Piattaforma di identità Microsoft . Azure
description: Informazioni su come creare un'app Web che inserigli/spedisci utenti
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
ms.openlocfilehash: 317ca55adb9f680dc93343a185395abad08889da
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881316"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>App Web che consente di accedere agli utenti: accesso e disconnessione

Informazioni su come aggiungere l'accesso al codice per l'app Web che consente di accedere agli utenti. Quindi, scopri come lasciarli disconnettersi.

## <a name="sign-in"></a>Accesso

L'accesso è costituito da due parti:

- Il pulsante di accesso nella pagina HTML
- L'azione di accesso nel code-behind nel controller

### <a name="sign-in-button"></a>Pulsante di accesso

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core, il pulsante `Views\Shared\_LoginPartial.cshtml`di accesso è esposto in . Viene visualizzato solo quando non è presente alcun account autenticato. Ovvero, viene visualizzato quando l'utente non ha ancora eseguito l'accesso o si è disconnesso.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC il pulsante di `Views\Shared\_LoginPartial.cshtml`disconnessione viene esposto in . Viene visualizzato solo quando è presente un account autenticato. Ovvero, viene visualizzato quando l'utente ha eseguito l'accesso in precedenza.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

Nella guida introduttiva di Java, il pulsante di accesso si trova nel file [di main/resources/templates/index.html.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="python"></a>[Python](#tab/python)

Nella guida introduttiva di Python non è disponibile alcun pulsante di accesso. Il code-behind richiede automaticamente all'utente l'accesso quando raggiunge la radice dell'app Web. Vedere [app.py-L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`azione del controllore

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET, la selezione del pulsante **Accedi** `SignIn` nell'app `AccountController` Web attiva l'azione sul controller. Nelle versioni precedenti dei modelli `Account` principali di ASP.NET, il controller era incorporato con l'app Web. Questo non è più il caso perché il controller è ora parte del framework ASP.NET Core.

Il codice `AccountController` per è disponibile dal repository ASP.NET Core in [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Il controllo dell'account sfida l'utente reindirizzando all'endpoint della piattaforma di identità Microsoft.The account control challenges the user by redirecting to the Microsoft identity platform endpoint. Per informazioni dettagliate, vedere il metodo SignIn fornito come parte di ASP.NET Core.For details, see the [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) method provided as part of ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, la `SignOut()` disconnessione viene attivata dal metodo in un controller (ad esempio, [AccountController.cs, L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Questo metodo non fa parte del framework di ASP.NET (contrariamente a quanto accade in ASP.NET Core). Invia una richiesta di accesso OpenID dopo aver proposto un URI di reindirizzamento.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

In Java, la disconnessione viene gestita `logout` chiamando direttamente `post_logout_redirect_uri` l'endpoint della piattaforma di identità Microsoft e fornendo il valore. Per informazioni dettagliate, vedere [AuthPageController.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="python"></a>[Python](#tab/python)

A differenza di altre piattaforme, MSAL Python si occupa di consentire all'utente di accedere dalla pagina di accesso. Vedere [app.py-L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

Il `_build_msal_app()` metodo è definito in [app.py-L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) come segue:

```Python
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

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Dopo che l'utente ha eseguito l'accesso all'app, è consigliabile abilitare l'accesso.

## <a name="sign-out"></a>Disconnessione

La disconnessione da un'app Web implica molto più che rimuovere le informazioni sull'account di accesso dallo stato dell'app Web.
L'app Web deve inoltre reindirizzare `logout` l'utente all'endpoint della piattaforma di identità Microsoft per disconnettersi. 

Quando l'app Web reindirizza `logout` l'utente all'endpoint, questo endpoint cancella la sessione dell'utente dal browser. Se l'app non è `logout` andata all'endpoint, l'utente eseguirà nuovamente l'autenticazione nell'app senza immettere nuovamente le credenziali. Il motivo è che avranno una sessione di accesso singolo valida con l'endpoint della piattaforma di identità Microsoft.The reason is that they'll have a valid single sign-in session with the Microsoft identity platform endpoint.

Per altre informazioni, vedere la sezione Inviare una richiesta di disconnessione nella piattaforma Microsoft Identity e nella documentazione del protocollo OpenID Connect.To learn more, see the [Send a di-out request](v2-protocols-oidc.md#send-a-sign-out-request) section in the Microsoft identity platform and the [OpenID Connect protocol](v2-protocols-oidc.md) documentation.

### <a name="application-registration"></a>Registrazione dell'applicazione

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Durante la registrazione dell'applicazione, si registra un URI di post-disconnessione. Nel nostro tutorial, `https://localhost:44321/signout-oidc` ti sei registrato nel campo URL di **disconnessione** della sezione **Impostazioni avanzate** della pagina **Autenticazione.** Per informazioni dettagliate, vedere [Registrare l'app webApp.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Durante la registrazione dell'applicazione, si registra un URI di post-disconnessione. Nel nostro tutorial, `https://localhost:44308/Account/EndSession` ti sei registrato nel campo URL di **disconnessione** della sezione **Impostazioni avanzate** della pagina **Autenticazione.** Per informazioni dettagliate, vedere [Registrare l'app webApp.](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="java"></a>[Java](#tab/java)

Durante la registrazione dell'applicazione, si registra un URI di post-disconnessione. Nel nostro tutorial, `http://localhost:8080/msal4jsample/sign_out` ti sei registrato nel campo URL di **disconnessione** della sezione **Impostazioni avanzate** della pagina **Autenticazione.**

# <a name="python"></a>[Python](#tab/python)

Durante la registrazione dell'applicazione, non è necessario registrare un URL di disconnessione aggiuntivo. L'app verrà richiamata sull'URL principale.

---

### <a name="sign-out-button"></a>Pulsante Disconnetti

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core, il pulsante `Views\Shared\_LoginPartial.cshtml`di disconnessione è esposto in . Viene visualizzato solo quando è presente un account autenticato. Ovvero, viene visualizzato quando l'utente ha eseguito l'accesso in precedenza.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC il pulsante di `Views\Shared\_LoginPartial.cshtml`disconnessione viene esposto in . Viene visualizzato solo quando è presente un account autenticato. Ovvero, viene visualizzato quando l'utente ha eseguito l'accesso in precedenza.

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

# <a name="java"></a>[Java](#tab/java)

Nella guida introduttiva di Java, il pulsante di disconnessione si trova nel file di main/resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

Nella guida introduttiva di Python, il pulsante di disconnessione si trova nel file [templates/index.html-L10.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut`azione del controllore

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET, la selezione del pulsante **Disconnetti** nell'app Web attiva l'azione `SignOut` sul `AccountController` controller. Nelle versioni precedenti dei modelli `Account` ASP.NET Core, il controller era incorporato con l'app Web. Questo non è più il caso perché il controller è ora parte del framework ASP.NET Core.

Il codice `AccountController` per il è disponibile dal repository principale ASP.NET [in AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Il controllo dell'account:

- Imposta un URI di `/Account/SignedOut` reindirizzamento OpenID in modo che il controller venga richiamato quando Azure AD ha completato la disconnessione.
- Calls `Signout()`, che consente al middleware OpenID `logout` Connect di contattare l'endpoint della piattaforma di identità Microsoft. L'endpoint è quindi:

  - Cancella il cookie di sessione dal browser.
  - Richiama l'URL di disconnessione. Per impostazione predefinita, nell'URL di disconnessione viene visualizzata la pagina di visualizzazione [disconnessa SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Questa pagina viene fornita anche come parte di ASP.NET Core.This page is also provided as part of ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, la `SignOut()` disconnessione viene attivata dal metodo in un controller (ad esempio, [AccountController.cs, L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Questo metodo non fa parte del framework di ASP.NET, contrariamente a quanto accade in ASP.NET Core. esso:

- Invia una richiesta di verifica di disconnessione OpenID.
- Cancella la cache.
- Reindirizza alla pagina che desidera.

```csharp
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

# <a name="java"></a>[Java](#tab/java)

In Java, la disconnessione viene gestita `logout` chiamando direttamente `post_logout_redirect_uri` l'endpoint della piattaforma di identità Microsoft e fornendo il valore. Per informazioni dettagliate, vedere [AuthPageController.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)

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

# <a name="python"></a>[Python](#tab/python)

Il codice che si disconnette l'utente si trova in [app.py-L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Intercettazione della chiamata `logout` all'endpoint

L'URI di post-disconnessione consente alle applicazioni di partecipare alla disconnessione globale.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Il middleware OpenID Connect di ASP.NET consente all'app `logout` di intercettare la chiamata `OnRedirectToIdentityProviderForSignOut`all'endpoint della piattaforma di identità Microsoft fornendo un evento OpenID Connect denominato . Per un esempio di come sottoscrivere questo evento (per cancellare la cache dei token), vedere [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs.L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, si delega al middleware per eseguire la disconnessione, cancellando il cookie di sessione:

```csharp
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

# <a name="java"></a>[Java](#tab/java)

Nella guida introduttiva di Java, l'URI di reindirizzamento post-logout visualizza solo la pagina index.html.

# <a name="python"></a>[Python](#tab/python)

Nella guida introduttiva di Python, l'URI di reindirizzamento post-logout visualizza solo la pagina index.html.

---

## <a name="protocol"></a>Protocollo

Per ulteriori informazioni sulla disconnessione, leggere la documentazione del protocollo disponibile in [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare in produzione](scenario-web-app-sign-user-production.md)
