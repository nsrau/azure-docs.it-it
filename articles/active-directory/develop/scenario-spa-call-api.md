---
title: Applicazione a singola pagina (chiamare un'API Web)-piattaforma di identità Microsoft
description: Informazioni su come creare un'applicazione a singola pagina (chiamare un'API Web)
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
ms.openlocfilehash: 4170a6642d35802581b5d1ff28eb802a6eb3482b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766158"
---
# <a name="single-page-application-call-a-web-api"></a>Applicazione a pagina singola: chiamare un'API Web

È consigliabile chiamare il metodo `acquireTokenSilent` per acquisire o rinnovare un token di accesso prima di chiamare un'API Web. Una volta che si dispone di un token, è possibile chiamare un'API Web protetta.

## <a name="call-a-web-api"></a>Chiamare un'API Web

### <a name="javascript"></a>JavaScript

Usare il token di accesso acquisito come portatore in una richiesta HTTP per chiamare qualsiasi API Web, ad esempio Microsoft Graph API. ad esempio:

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

Il wrapper angolare MSAL sfrutta l'intercettore HTTP per acquisire automaticamente i token di accesso in modo invisibile all'utente e associarli alle richieste HTTP alle API. Per altre informazioni, vedere [acquisire un token per chiamare un'API](scenario-spa-acquire-token.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passa all'ambiente di produzione](scenario-spa-production.md)
