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
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860630"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>App Web che chiama le API Web: acquisisce un token per l'app

Ora che è stato compilato l'oggetto applicazione client, questo verrà usato per acquisire un token per chiamare un'API Web. In ASP.NET o ASP.NET Core la chiamata a un'API Web viene quindi eseguita nel controller. Si tratta di:

- Ottenere un token per l'API Web usando la cache dei token. Per ottenere questo token, chiamare `AcquireTokenSilent`.
- Chiamata dell'API protetta con il token di accesso.

## <a name="aspnet-core"></a>ASP.NET Core

I metodi controller sono protetti da un `[Authorize]` attributo che impone agli utenti autenticati di usare l'app Web. Ecco il codice che chiama Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Di seguito è riportato un codice semplificato dell'azione di HomeController, che ottiene un token per chiamare il Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

Per comprendere meglio il codice necessario per questo scenario, vedere il passaggio 2 ([2-1-chiamate app Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) dell'esercitazione [MS-Identity-aspnetcore-WebApp-Tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Esistono numerose complessità aggiuntive, ad esempio:

- Implementazione di una cache di token per l'app Web (l'esercitazione presenta diverse implementazioni)
- Rimozione dell'account dalla cache quando l'utente si disconnette
- Chiamata di diverse API, incluso il consenso incrementale

## <a name="aspnet"></a>ASP.NET

Le cose sono simili in ASP.NET:

- Un'azione del controller protetta da un attributo [autorizzate] estrae l'ID tenant e l'ID `ClaimsPrincipal` utente del membro del controller. (ASP.NET usa `HttpContext.User`).
- Da qui, compila un MSAL.NET `IConfidentialClientApplication`.
- Infine, viene chiamato il `AcquireTokenSilent` metodo dell'applicazione client riservata.

Il codice è simile al codice illustrato per ASP.NET Core.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamare un'API Web](scenario-web-app-call-api-call-api.md)
