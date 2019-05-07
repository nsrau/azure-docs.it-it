---
title: "App Web che chiama web API (acquisire un token per l'app): piattaforma delle identità Microsoft"
description: Informazioni su come compilare un'app Web che chiama web API (acquisire un token per l'app)
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
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074801"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>App Web che chiama le API - web acquisire un token per l'app

Dopo aver compilato è oggetto di applicazione client, si userà lo per acquisire un token che si userà quindi per chiamare un'API web. In ASP.NET o ASP.NET Core, la chiamata a una web che API viene quindi eseguita nel controller. Si tratta di:

- Ottenere un token per l'API web usando la cache dei token. A tale scopo, si chiama `AcquireTokenSilent`
- Chiamare le API protetta con il token di accesso

## <a name="aspnet-core"></a>ASP.NET Core

I metodi del controller sono protetti da un `[Authorize]` attributo che impedisca agli utenti di essere autenticati per usare l'App Web. Ecco il codice che chiama Microsoft Graph

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Ecco un codice semplificato dell'azione di HomeController, che ottiene un token per chiamare Microsoft Graph.

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

Se si desidera conoscere nei dettagli il totale del codice necessario per questo scenario, vedere la fase 2 [2-1-Web App chiama Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) passaggio delle [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) esercitazione

Esistono numerosi esistono complessità aggiuntive, ad esempio:

- implementazione di una cache dei token per l'App Web (esercitazione di presentare diverse implementazioni di)
- Rimozione dell'account dalla cache quando l'utente accede in uscita
- Chiamata di API diverse, tra cui consenso incrementale

## <a name="aspnet"></a>ASP.NET

Gli aspetti sono simili in ASP.NET:

- Estrae l'ID del tenant e ID utente di un'azione del controller protetta da un attributo [Authorize], il `ClaimsPrincipal` membro del controller (in ASP.NET viene utilizzato `HttpContext.User`)
- da qui si basa un MSAL.NET `IConfidentialClientApplication`
- Chiamata IT di `AcquireTokenSilent` metodo dell'applicazione client riservato 

il codice è simile al codice illustrato per ASP.NET Core

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamare un'API Web](scenario-web-app-call-api-call-api.md)
