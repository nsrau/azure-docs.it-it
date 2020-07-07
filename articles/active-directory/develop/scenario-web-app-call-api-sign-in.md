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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e138b3513b42dda47b0a114d866d657e18e3e393
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82181648"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Un'app Web che chiama le API Web: rimuovere gli account dalla cache dei token per la disconnessione globale

Si è appreso come aggiungere l'accesso all'app Web nell'app Web per l'accesso [degli utenti: accesso e disconnessione](scenario-web-app-sign-user-sign-in.md).

La disconnessione è diversa per un'app Web che chiama le API Web. Quando l'utente si disconnette dall'applicazione o da qualsiasi applicazione, è necessario rimuovere i token associati a tale utente dalla cache dei token.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercettare il callback dopo Single Sign-out

Per cancellare la voce della cache dei token associata all'account che ha effettuato la disconnessione, l'applicazione può intercettare l' `logout` evento After. Le app Web archiviano i token di accesso per ogni utente in una cache dei token. Intercettando il callback after `logout` , l'applicazione Web può rimuovere l'utente dalla cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web si occupa dell'implementazione di disconnessione.

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
