---
title: App Web che chiama le API Web (acquisisce un token per l'app)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app Web che chiama le API Web (acquisendo un token per l'app)
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
ms.openlocfilehash: a259fbcf3fde84edccafbcd2fd6594ddb623edfd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175338"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>App Web che chiama le API Web: acquisisce un token per l'app

Ora che è stato compilato l'oggetto applicazione client, questo verrà usato per acquisire un token per chiamare un'API Web. In ASP.NET o ASP.NET Core la chiamata a un'API Web viene quindi eseguita nel controller. Si tratta di:

- Ottenere un token per l'API Web usando la cache dei token. Per ottenere questo token, chiamare `AcquireTokenSilent`.
- Chiamata dell'API protetta con il token di accesso.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I metodi controller sono protetti da un attributo `[Authorize]` che impone agli utenti autenticati di usare l'app Web. Ecco il codice che chiama Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

Il servizio `ITokenAcquisition` viene inserito da ASP.NET tramite l'inserimento di dipendenze.


Di seguito è riportato un codice semplificato dell'azione di HomeController, che ottiene un token per chiamare il Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // use the access token to call a protected web API
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Per comprendere meglio il codice necessario per questo scenario, vedere il passaggio 2 ([2-1-chiamate app Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) dell'esercitazione [MS-Identity-aspnetcore-WebApp-Tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Esistono numerose complessità aggiuntive, ad esempio:

- Chiamata di diverse API,
- elaborazione del consenso incrementale e dell'accesso condizionale.

Questi passaggi avanzati vengono elaborati nel capitolo 3 dell'esercitazione [3-webapp-Multiapi](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Le cose sono simili in ASP.NET:

- Un'azione del controller protetta da un attributo [autorizzate] estrae l'ID tenant e l'ID utente del `ClaimsPrincipal` membro del controller. (ASP.NET usa `HttpContext.User`.)
- Da qui viene compilato un `IConfidentialClientApplication`MSAL.NET.
- Infine, viene chiamato il metodo di `AcquireTokenSilent` dell'applicazione client riservata.

Il codice è simile al codice illustrato per ASP.NET Core.

# <a name="javatabjava"></a>[Java](#tab/java)

Nell'esempio Java, il codice che chiama un'API si trova nel metodo getUsersFromGraph [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Tenta di chiamare `getAuthResultBySilentFlow`. Se l'utente deve fornire il consenso a più ambiti, il codice elabora il `MsalInteractionRequiredException` per richiedere l'intervento dell'utente.

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

            // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
            // so user can consent to new scopes
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
// Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Nell'esempio Python, il codice che chiama Microsoft Graph si trova in [app. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Tenta di ottenere un token dalla cache dei token e quindi chiama l'API EB dopo aver impostato l'intestazione dell'autorizzazione. In caso contrario, viene nuovamente firmato nell'utente.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamare un'API Web](scenario-web-app-call-api-call-api.md)
