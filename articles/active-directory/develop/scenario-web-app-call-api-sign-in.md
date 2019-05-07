---
title: App Web che chiama web API (accesso) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'app Web che chiama web API (Accedi)
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074561"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>App Web che chiama le API - web di accesso

Sai già come aggiungere l'accesso all'App web. Si apprenderà che in [app Web che esegue l'accesso gli utenti - aggiungere accesso](scenario-web-app-sign-user-sign-in.md).

Che cosa è diversa in questo caso, è che, quando l'utente ha connesso orizzontale, da questa applicazione o da qualsiasi applicazione, si desidera rimuovere dalla cache dei token, i token associati all'utente.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Intercettazione di callback dopo la disconnessione - Single Sign-Out

L'applicazione può intercettare il dopo `logout` evento, ad esempio per eliminare la voce della cache del token associata all'account che disconnesso. Si vedrà nella seconda parte di questa esercitazione (sull'app Web chiama un'API Web), che l'app web archivierà i token di accesso per l'utente in una cache. Intercettazione di dopo `logout` callback consente alle applicazioni web rimuovere l'utente dalla cache dei token. Questo meccanismo è illustrato nel `AddMsal()` metodo [StartupHelper.cs L137 143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

Il **Url di disconnessione** che è stato registrato per l'applicazione è possibile implementare single sign-out. La piattaforma delle identità Microsoft `logout` chiamerà l'endpoint di **URL di disconnessione** registrati con l'applicazione. Questa chiamata si verifica se la disconnessione è stata avviata dall'app web o da un'altra app web o il browser. Per altre informazioni, vedere [Single Sign-Out](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) nella documentazione concettuale.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisire un token per l'app web](scenario-web-app-call-api-acquire-token.md)
