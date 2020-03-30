---
title: Crea un'app a pagina singola che chiama un'API Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'applicazione a pagina singola che chiama un'API Web
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159999"
---
# <a name="single-page-application-call-a-web-api"></a>Applicazione a pagina singola: chiamare un'API Web

È consigliabile chiamare `acquireTokenSilent` il metodo per acquisire o rinnovare un token di accesso prima di chiamare un'API Web. Dopo aver creato un token, è possibile chiamare un'API Web protetta.

## <a name="call-a-web-api"></a>Chiamare un'API Web

# <a name="javascript"></a>[Javascript](#tab/javascript)

Usare il token di accesso acquisito come portatore in una richiesta HTTP per chiamare qualsiasi API Web, ad esempio l'API Microsoft Graph.Use the acquired access token as a bearer in an HTTP request to call any web API, such as Microsoft Graph API. Ad esempio:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

Il wrapper angolare MSAL sfrutta l'intercettore HTTP per acquisire automaticamente i token di accesso in modo invisibile all'utente e collegarli alle richieste HTTP alle API. Per altre informazioni, vedere [Acquisire un token per chiamare un'API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare in produzione](scenario-spa-production.md)
