---
title: App desktop di chiamate di web API (chiama un'API web) - piattaforma delle identità Microsoft
description: Informazioni su come creare un'app Desktop che chiama web API (chiama un'API web)
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
ms.openlocfilehash: 6fb240b54c3d698ead9d3427f603acca2b53745a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075926"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>App desktop che chiama le API - web chiama un'API web

Dopo aver creato un token, è possibile chiamare un'API web protetta.

## <a name="calling-a-web-api-from-net"></a>Chiamare un'API web da .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>La chiamata a varie API - consenso incrementale e l'accesso condizionale

Se è necessario chiamare varie API per lo stesso utente, una volta ottenuto un token per la prima API, è possibile chiamare solo `AcquireTokenSilent`, e si otterrà un token per le altre API automaticamente la maggior parte dei casi.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

I casi in cui è necessaria l'interazione è quando:

- L'utente autorizzato per la prima API, ma a questo punto è necessario fornire il consenso per ambiti più (consenso incrementale)
- La prima API non richiedono l'autenticazione a più fattori, a differenza quello successivo.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare alla produzione](scenario-desktop-production.md)
