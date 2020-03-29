---
title: Rimozione di account dalla cache dei token alla disconnessione - Piattaforma di identità Microsoft . Azure
description: Informazioni su come rimuovere un account dalla cache dei token alla disconnessione
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
ms.openlocfilehash: ea18538662dc63876a50f52e9e6a8b3fffb3b35a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758871"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Un'app Web che chiama le API Web: rimuovere gli account dalla cache dei token nella disconnessione globale

Si è appreso come aggiungere l'accesso all'app Web [nell'app Web che consente di accedere agli utenti: Accesso e disconnessione.](scenario-web-app-sign-user-sign-in.md)

La disconnessione è diversa per un'app Web che chiama api Web. Quando l'utente si disconnette dall'applicazione o da qualsiasi applicazione, è necessario rimuovere i token associati a tale utente dalla cache dei token.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercettare il callback dopo la disconnessione singola

Per cancellare la voce della cache dei token associata all'account che si è disconnesso, l'applicazione può intercettare l'evento after. `logout` Le app Web archiviano i token di accesso per ogni utente in una cache dei token. Intercettando il `logout` callback after, l'applicazione Web può rimuovere l'utente dalla cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Per ASP.NET Core, il meccanismo di `AddMsal()` intercettazione è illustrato nel metodo di [WebAppServiceCollectionExtensions.cs, L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

L'URL di disconnessione registrato in precedenza per l'applicazione consente di implementare la disconnessione singola. L'endpoint `logout` della piattaforma di identità Microsoft chiama l'URL di disconnessione. Questa chiamata si verifica se la disconnessione è iniziata dall'app Web o da un'altra app Web o dal browser. Per ulteriori informazioni, vedere [Disconnessione singola](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Il codice `RemoveAccountAsync` per è disponibile da [Microsoft.Identity.Web/TokenAcquisition.cs , L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

L'esempio ASP.NET non rimuove gli account dalla cache durante la disconnessione globale.

# <a name="java"></a>[Java](#tab/java)

Nell'esempio Java non vengono rimossi gli account dalla cache durante la disconnessione globale.

# <a name="python"></a>[Python](#tab/python)

L'esempio Python non rimuove gli account dalla cache durante la disconnessione globale.

---

## <a name="next-steps"></a>Passaggi successivi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Acquisire un token per l'app Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Acquisire un token per l'app Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Acquisire un token per l'app Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Acquisire un token per l'app Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
