---
title: Ottenere un token in un'app Web che chiama le API Web - Piattaforma di identità Microsoft . Azure
description: Informazioni su come acquisire un token per un'app Web che chiama le API Web
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
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759075"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Un'app Web che chiama le API Web: acquisire un token per l'app

L'oggetto applicazione client è stato compilato. A questo punto, si userà per acquisire un token per chiamare un'API web. In ASP.NET o ASP.NET Core, la chiamata di un'API Web viene eseguita nel controller:In the server and ASP.NET Core, calling a web API is done in the controller:

- Ottenere un token per l'API Web usando la cache dei token. Per ottenere questo token, `AcquireTokenSilent` chiamare il metodo .
- Chiamare l'API protetta, passando il token di accesso come parametro.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I metodi del controller `[Authorize]` sono protetti da un attributo che impone agli utenti autenticati di utilizzare l'app Web. Ecco il codice che chiama Microsoft Graph:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

Il `ITokenAcquisition` servizio viene inserito da ASP.NET utilizzando l'inserimento delle dipendenze.

Ecco il codice semplificato per `HomeController`l'azione di , che ottiene un token per chiamare Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Per comprendere meglio il codice necessario per questo scenario, vedere il passaggio della fase 2 ([2-1-Web App Calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) dell'esercitazione [ms-identity-aspnetcore-webapp-tutorial.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Ci sono altre varianti complesse, come:

- Chiamata di diverse API.
- Elaborazione del consenso incrementale e dell'accesso condizionale.

Questi passaggi avanzati sono illustrati nel capitolo 3 dell'esercitazione [3-WebApp-multi-API.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Il codice per ASP.NET è simile al codice visualizzato per ASP.NET Core:

- Un'azione del controller, protetta da un attributo [Authorize], estrae l'ID tenant e l'ID utente del `ClaimsPrincipal` membro del controller. (ASP.NET `HttpContext.User`utilizza .)
- Da lì, costruisce un `IConfidentialClientApplication` oggetto MSAL.NET.
- Infine, chiama `AcquireTokenSilent` il metodo dell'applicazione client riservata.

# <a name="java"></a>[Java](#tab/java)

Nell'esempio Java, il codice che chiama un'API si trova nel metodo getUsersFromGraph in [AuthPageController.java.L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Il metodo tenta `getAuthResultBySilentFlow`di chiamare . Se l'utente deve acconsentire a più ambiti, il codice elabora l'oggetto `MsalInteractionRequiredException` per contestare l'utente.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio Python, il codice che chiama Microsoft Graph si trova in [app.py-L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Il codice tenta di ottenere un token dalla cache dei token. Quindi, dopo aver impostato l'intestazione di autorizzazione, chiama l'API web. Se non riesce a ottenere un token, firma nuovamente l'utente.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamare un'API Web](scenario-web-app-call-api-call-api.md)
