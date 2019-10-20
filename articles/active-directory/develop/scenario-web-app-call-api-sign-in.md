---
title: App Web che chiama le API Web (accesso)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app Web che chiama le API Web (accesso)
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cb0190f76ddce79012a5bf97e2d813f40f9f018
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596371"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>App Web che chiama le API Web-accedi

Si sa già come aggiungere l'accesso all'app Web. Si apprende che nell' [app Web per l'accesso degli utenti si aggiunge l'accesso](scenario-web-app-sign-user-sign-in.md).

Ciò che è diverso qui è che, quando l'utente si è disconnesso, da questa applicazione o da qualsiasi applicazione, si vuole rimuovere dalla cache dei token i token associati all'utente.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Intercettazione del callback dopo l'accesso Single Sign-out

L'applicazione può intercettare il dopo `logout` evento, ad esempio per cancellare la voce della cache dei token associata all'account che ha effettuato la disconnessione. L'app Web archivia i token di accesso per l'utente in una cache. L'intercettazione di dopo `logout` callback consente all'applicazione Web di rimuovere l'utente dalla cache dei token.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Questo meccanismo è illustrato nel metodo `AddMsal()` di [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

L' **URL di disconnessione** registrato per l'applicazione consente di implementare l'accesso Single Sign-on. Microsoft Identity Platform `logout` endpoint chiamerà l'URL di **disconnessione** registrato con l'applicazione. Questa chiamata si verifica se la disconnessione è stata avviata dall'app Web o da un'altra app Web o dal browser. Per altre informazioni, vedere [Single Sign-out](v2-protocols-oidc.md#single-sign-out).

```CSharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Il codice per RemoveAccountAsync è disponibile da [Microsoft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

L'esempio ASP.NET non rimuove gli account dalla cache nella disconnessione globale

# <a name="javatabjava"></a>[Java](#tab/java)

L'esempio Java non rimuove gli account dalla cache nella disconnessione globale

# <a name="pythontabpython"></a>[Python](#tab/python)

L'esempio Python non rimuove gli account dalla cache nella disconnessione globale

---

## <a name="next-steps"></a>Passaggi successivi

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Acquisizione di un token per l'app Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Acquisizione di un token per l'app Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Acquisizione di un token per l'app Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Acquisizione di un token per l'app Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
