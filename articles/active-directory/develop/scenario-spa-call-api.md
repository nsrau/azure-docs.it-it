---
title: Applicazione a singola pagina (chiamare un'API web) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione a singola pagina (chiamare un'API web)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a4ed01ac55a1153a62c672b33056a543b912ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545602"
---
# <a name="single-page-application---call-a-web-api"></a>Applicazione a singola pagina - chiamata di un API web

Si consiglia di chiamare il `acquireTokenSilent` metodo per acquistare o rinnovare un token di accesso prima di chiamare un'API web. Dopo aver ottenuto un token, è possibile chiamare un'API web protetta.

## <a name="call-a-web-api"></a>Chiamare un'API Web

### <a name="javascript"></a>JavaScript

Usare il token di accesso acquisito come una connessione in una richiesta HTTP per chiamare qualsiasi API web, ad esempio Microsoft Graph API. Ad esempio:

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

### <a name="angular"></a>Angular

Come accennato nel [acquisizione sezione token](scenario-spa-acquire-token.md), il wrapper MSAL Angular sfrutta l'intercettore HTTP per acquisire token di accesso in modo invisibile e collegarli alle richieste HTTP alle API automaticamente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare alla produzione](scenario-spa-production.md)
