---
title: App Web che esegue l'accesso degli utenti (accesso)-piattaforma di identità Microsoft
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b41e46401d600ebaba1febb86aafbd55c8399a
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482569"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>App Web per l'accesso degli utenti: accesso e disconnessione

Informazioni su come aggiungere l'accesso al codice per l'app Web che accede agli utenti. Quindi, informazioni su come lasciarli disconnettersi.

## <a name="sign-in"></a>Accesso

L'accesso è costituito da due parti:

- Pulsante di accesso nella pagina HTML
- Azione di accesso nel code-behind nel controller

### <a name="sign-in-button"></a>Pulsante Accedi

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core il pulsante di accesso viene esposto in `Views\Shared\_LoginPartial.cshtml`. Viene visualizzato solo quando non è presente alcun account autenticato. Ovvero, viene visualizzato quando l'utente non ha ancora eseguito l'accesso o si è disconnesso.

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC il pulsante di disconnessione viene esposto in `Views\Shared\_LoginPartial.cshtml`. Viene visualizzato solo quando è presente un account autenticato. Ovvero viene visualizzato quando l'utente ha già eseguito l'accesso.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Nella Guida introduttiva di Java il pulsante di accesso si trova nel file [Main/Resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) .

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Nella Guida introduttiva di Python non è disponibile alcun pulsante di accesso. Il code-behind chiede automaticamente all'utente di accedere quando raggiunge la radice dell'app Web. Vedere [app. py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` azione del controller

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET, se si seleziona il pulsante di **accesso** nell'app Web, viene attivata l'azione `SignIn` sul controller `AccountController`. Nelle versioni precedenti dei modelli di ASP.NET Core, il controller di `Account` era incorporato con l'app Web. Non è più così perché il controller fa ora parte del framework ASP.NET Core.

Il codice per `AccountController` è disponibile dal repository ASP.NET Core in [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Il controllo dell'account richiede l'intervento dell'utente mediante il reindirizzamento all'endpoint della piattaforma Microsoft Identity. Per informazioni dettagliate, vedere il metodo [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) fornito come parte di ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, la disconnessione viene attivata dal metodo `SignOut()` su un controller, ad esempio [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23). Questo metodo non fa parte del framework ASP.NET (contrariamente a quanto accade in ASP.NET Core). Invia una richiesta di accesso OpenID dopo aver proposto un URI di reindirizzamento.

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

In Java la disconnessione viene gestita chiamando direttamente l'endpoint della piattaforma Microsoft Identity `logout` e specificando il valore di `post_logout_redirect_uri`. Per informazioni dettagliate, vedere [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Diversamente da altre piattaforme, MSAL Python si occupa di consentire all'utente di accedere dalla pagina di accesso. Vedere [app. py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

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

Il metodo `_build_msal_app()` è definito in [app. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) come indicato di seguito:

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

Dopo che l'utente ha eseguito l'accesso all'app, è opportuno abilitarne la disconnessione.

## <a name="sign-out"></a>Disconnessione

La disconnessione da un'app Web prevede più di rimuovere le informazioni relative all'account connesso dallo stato dell'app Web.
L'app Web deve anche reindirizzare l'utente alla piattaforma Microsoft Identity `logout` endpoint per la disconnessione. 

Quando l'app Web reindirizza l'utente all'endpoint `logout`, questo endpoint Cancella la sessione dell'utente dal browser. Se l'app non è stata inviata all'endpoint `logout`, l'utente eseguirà di nuovo l'autenticazione all'app senza immettere di nuovo le credenziali. Il motivo è che avranno una sessione Single Sign-on valida con l'endpoint della piattaforma di identità Microsoft.

Per altre informazioni, vedere la sezione [inviare una richiesta](v2-protocols-oidc.md#send-a-sign-out-request) di disconnessione nella [piattaforma di identità Microsoft e nella documentazione del protocollo OpenID Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Registrazione dell'applicazione

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Durante la registrazione dell'applicazione, si registra un URI di post-disconnessione. In questa esercitazione è stata eseguita la registrazione di `https://localhost:44321/signout-oidc` nel campo **Logout URL** della sezione **Advanced Settings** della pagina **Authentication** . Per informazioni dettagliate, vedere [registrare l'app webapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Durante la registrazione dell'applicazione, si registra un URI di post-disconnessione. In questa esercitazione è stata eseguita la registrazione di `https://localhost:44308/Account/EndSession` nel campo **Logout URL** della sezione **Advanced Settings** della pagina **Authentication** . Per informazioni dettagliate, vedere [registrare l'app webapp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="javatabjava"></a>[Java](#tab/java)

Durante la registrazione dell'applicazione, si registra un URI di post-disconnessione. In questa esercitazione è stata eseguita la registrazione di `http://localhost:8080/msal4jsample/sign_out` nel campo **Logout URL** della sezione **Advanced Settings** della pagina **Authentication** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Durante la registrazione dell'applicazione, non è necessario registrare un URL di disconnessione aggiuntivo. L'app verrà richiamata sull'URL principale.

---

### <a name="sign-out-button"></a>Pulsante di disconnessione

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core il pulsante di disconnessione viene esposto in `Views\Shared\_LoginPartial.cshtml`. Viene visualizzato solo quando è presente un account autenticato. Ovvero viene visualizzato quando l'utente ha già eseguito l'accesso.

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

In ASP.NET MVC il pulsante di disconnessione viene esposto in `Views\Shared\_LoginPartial.cshtml`. Viene visualizzato solo quando è presente un account autenticato. Ovvero viene visualizzato quando l'utente ha già eseguito l'accesso.

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

Nella Guida introduttiva di Java il pulsante di disconnessione si trova nel file main/resources/templates/auth_page.html.

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

Nella Guida introduttiva di Python il pulsante di disconnessione si trova nel file [templates/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

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

### <a name="signout-action-of-the-controller"></a>`SignOut` azione del controller

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET, se si seleziona il pulsante di **disconnessione** nell'app Web, viene attivata l'azione `SignOut` sul controller `AccountController`. Nelle versioni precedenti dei modelli di ASP.NET Core, il controller `Account` era incorporato con l'app Web. Non è più così perché il controller fa ora parte del framework ASP.NET Core.

Il codice per il `AccountController` è disponibile dal repository principale di ASP.NET in [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Il controllo dell'account:

- Imposta un URI di reindirizzamento OpenID su `/Account/SignedOut` in modo che il controller venga richiamato quando Azure AD ha completato la disconnessione.
- Chiama `Signout()`, che consente al middleware di OpenID Connect di contattare l'endpoint di Microsoft Identity Platform `logout`. L'endpoint:

  - Cancella il cookie di sessione dal browser.
  - Richiama l'URL di disconnessione. Per impostazione predefinita, l'URL di disconnessione Visualizza la pagina di visualizzazione di cui è stato effettuato l'accesso [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Questa pagina viene fornita anche come parte di ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, la disconnessione viene attivata dal metodo `SignOut()` su un controller, ad esempio [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31). Questo metodo non fa parte del framework ASP.NET, contrariamente a quanto accade in ASP.NET Core. È

- Invia una richiesta di disconnessione OpenID.
- Cancella la cache.
- Reindirizza alla pagina che desidera.

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

In Java la disconnessione viene gestita chiamando direttamente l'endpoint della piattaforma Microsoft Identity `logout` e specificando il valore di `post_logout_redirect_uri`. Per informazioni dettagliate, vedere [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

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

Il codice che effettua la disattivazione dell'utente si trova in [app. py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Intercettazione della chiamata all'endpoint `logout`

L'URI di post-disconnessione consente alle applicazioni di partecipare alla disconnessione globale.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Il middleware di OpenID Connect ASP.NET Core consente all'app di intercettare la chiamata all'endpoint `logout` della piattaforma di identità Microsoft fornendo un evento OpenID Connect denominato `OnRedirectToIdentityProviderForSignOut`. Per un esempio di come sottoscrivere questo evento (per cancellare la cache dei token), vedere [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

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

Nella Guida introduttiva per Java, l'URI di reindirizzamento di post-disconnessione Visualizza solo la pagina index. html.

# <a name="pythontabpython"></a>[Python](#tab/python)

Nella Guida introduttiva di Python, l'URI di reindirizzamento di post-disconnessione Visualizza solo la pagina index. html.

---

## <a name="protocol"></a>Protocol

Per altre informazioni sulla disconnessione, vedere la documentazione del protocollo disponibile in [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passa all'ambiente di produzione](scenario-web-app-sign-user-production.md)
