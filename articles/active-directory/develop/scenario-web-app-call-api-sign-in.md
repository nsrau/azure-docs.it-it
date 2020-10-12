---
title: 'Rimuovere gli account dalla cache dei token alla disconnessione: piattaforma di identità Microsoft | Azure'
description: Informazioni su come rimuovere un account dalla cache dei token durante la disconnessione
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5246100815fde569c55027a555464c44a240d4b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119998"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Un'app Web che chiama le API Web: rimuovere gli account dalla cache dei token per la disconnessione globale

Si è appreso come aggiungere l'accesso all'app Web nell'app Web per l'accesso [degli utenti: accesso e disconnessione](scenario-web-app-sign-user-sign-in.md).

La disconnessione è diversa per un'app Web che chiama le API Web. Quando l'utente si disconnette dall'applicazione o da qualsiasi applicazione, è necessario rimuovere i token associati a tale utente dalla cache dei token.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercettare il callback dopo Single Sign-out

Per cancellare la voce della cache dei token associata all'account che ha effettuato la disconnessione, l'applicazione può intercettare l' `logout` evento After. Le app Web archiviano i token di accesso per ogni utente in una cache dei token. Intercettando il callback after `logout` , l'applicazione Web può rimuovere l'utente dalla cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web si occupa dell'implementazione di disconnessione. Per informazioni dettagliate [, vedere codice sorgente Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

L'esempio ASP.NET non rimuove gli account dalla cache nella disconnessione globale.

# <a name="java"></a>[Java](#tab/java)

L'esempio Java non rimuove gli account dalla cache nella disconnessione globale.

# <a name="python"></a>[Python](#tab/python)

L'esempio Python non rimuove gli account dalla cache nella disconnessione globale.

---

## <a name="next-steps"></a>Passaggi successivi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Acquisire un token per l'app Web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Acquisire un token per l'app Web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Acquisire un token per l'app Web](./scenario-web-app-call-api-acquire-token.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Acquisire un token per l'app Web](./scenario-web-app-call-api-acquire-token.md?tabs=python)

---