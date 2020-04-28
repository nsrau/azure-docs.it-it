---
title: Ottenere un token in un'app Web che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come acquisire un token per un'app Web che chiama API Web
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
ms.openlocfilehash: 40e788099a159e1f60c0af02deccd7e3bef82744
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181733"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Un'app Web che chiama le API Web: acquisire un token per l'app

L'oggetto applicazione client è stato compilato. A questo punto, verrà usato per acquisire un token per chiamare un'API Web. In ASP.NET o ASP.NET Core la chiamata a un'API Web viene eseguita nel controller:

- Ottenere un token per l'API Web usando la cache dei token. Per ottenere questo token, chiamare il metodo MSAL `AcquireTokenSilent` (o l'equivalente in Microsoft. Identity. Web).
- Chiamare l'API protetta, passando il token di accesso come parametro.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I metodi controller sono protetti da un `[Authorize]` attributo che impone agli utenti autenticati di usare l'app Web. Ecco il codice che chiama Microsoft Graph:

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

Il `ITokenAcquisition` servizio viene inserito da ASP.NET usando l'inserimento di dipendenze.

Di seguito è riportato il codice semplificato per `HomeController`l'azione di, che ottiene un token da chiamare Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Per comprendere meglio il codice necessario per questo scenario, vedere il passaggio 2 ([2-1-chiamate app Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) dell'esercitazione [MS-Identity-aspnetcore-WebApp-Tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Sono presenti altre varianti complesse, ad esempio:

- Chiamata di diverse API.
- Elaborazione del consenso incrementale e dell'accesso condizionale.

Questi passaggi avanzati sono trattati nel capitolo 3 dell'esercitazione [3-webapp-multiapi](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Il codice per ASP.NET è simile al codice illustrato per ASP.NET Core:

- Un'azione del controller, protetta da un attributo [autorizzate], estrae l'ID tenant e l'ID `ClaimsPrincipal` utente del membro del controller. (ASP.NET usa `HttpContext.User`).
- Da qui, compila un oggetto MSAL.NET `IConfidentialClientApplication` .
- Infine, viene chiamato il `AcquireTokenSilent` metodo dell'applicazione client riservata.

# <a name="java"></a>[Java](#tab/java)

Nell'esempio Java, il codice che chiama un'API si trova nel metodo getUsersFromGraph in [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Il metodo tenta di chiamare `getAuthResultBySilentFlow`. Se l'utente deve fornire il consenso a più ambiti, il codice elabora `MsalInteractionRequiredException` l'oggetto per richiedere l'intervento dell'utente.

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

Nell'esempio Python il codice che chiama Microsoft Graph si trova in [app. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Il codice tenta di ottenere un token dalla cache dei token. Quindi, dopo aver impostato l'intestazione Authorization, viene chiamata l'API Web. Se non è possibile ottenere un token, l'utente viene nuovamente firmato.

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

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamare un'API Web](scenario-web-app-call-api-call-api.md)
