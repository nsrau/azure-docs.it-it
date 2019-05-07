---
title: App Web che chiama web API (chiama un'API web) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'app Web che chiama web API (chiama un'API web)
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
ms.openlocfilehash: dd44dda06b2f6fc48538f2fb74c0bf8e04d0362b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074636"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>App Web che chiama le API - web chiama un'API web

Dopo aver creato un token, è possibile chiamare un'API web protetta.

## <a name="aspnet-core"></a>ASP.NET Core

Ecco un codice semplificato dell'azione del `HomeController`. Questo codice ottiene un token per chiamare Microsoft Graph. È stato aggiunto questo codice in fase di, che illustra come chiamare Microsoft Graph come un'API REST.

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

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> È possibile usare lo stesso principio per chiamare qualsiasi API web.
>
> Più Azure web API forniscono un SDK che semplifica la chiamata. Questo è il caso di Microsoft Graph. Nel prossimo articolo si apprenderà dove trovare un'esercitazione che illustra questi aspetti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare alla produzione](scenario-web-app-call-api-production.md)
