---
title: Creare un'app a singola pagina che chiama un'API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'applicazione a singola pagina che chiama un'API Web
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 3b71f66dfcbd33cdecc1a6fea46871ba0cda687d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442889"
---
# <a name="single-page-application-call-a-web-api"></a>Applicazione a pagina singola: chiamare un'API Web

È consigliabile chiamare il `acquireTokenSilent` metodo per acquisire o rinnovare un token di accesso prima di chiamare un'API Web. Una volta che si dispone di un token, è possibile chiamare un'API Web protetta.

## <a name="call-a-web-api"></a>Chiamare un'API Web

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Usare il token di accesso acquisito come portatore in una richiesta HTTP per chiamare qualsiasi API Web, ad esempio Microsoft Graph API. Esempio:

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

Il wrapper angolare MSAL sfrutta l'intercettore HTTP per acquisire automaticamente i token di accesso in modo invisibile all'utente e associarli alle richieste HTTP alle API. Per altre informazioni, vedere [acquisire un token per chiamare un'API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, [passare alla produzione](scenario-spa-production.md).