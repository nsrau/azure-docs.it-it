---
title: App daemon che chiama le API Web (chiamata alle API Web)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app daemon che chiama API Web (chiamando API Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eacb574f20abeb63a9d0ab8caf534eb7abb9784
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056353"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>App daemon che chiama le API Web: chiama un'API Web dall'app

Un'app daemon può chiamare un'API Web da un'applicazione daemon .NET o chiamare diverse API Web pre-approvate.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Chiamata di un'API Web da un'applicazione daemon .NET

Ecco come usare il token per chiamare un'API

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API" 
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Chiamata di diverse API

Per le app daemon, le API Web chiamate devono essere pre-approvate. Non ci sarà alcun consenso incrementale con le app daemon (non c'è alcuna interazione con l'utente). L'amministratore del tenant deve pre-concedere il consenso dell'applicazione e di tutte le autorizzazioni dell'API. Per chiamare diverse API, è necessario acquisire un token per ogni risorsa, ogni volta che si chiama `AcquireTokenForClient`. MSAL utilizzerà la cache del token dell'applicazione per evitare chiamate al servizio non necessarie.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App daemon-sposta in produzione](./scenario-daemon-production.md)