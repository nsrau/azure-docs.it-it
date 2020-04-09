---
title: Chiamare un'API Web da un'app daemon - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'app daemon che chiama le API Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e440628526dada7655cc71f63fc9fff006cc5ef5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885447"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>App Daemon che chiama API Web- chiama un'API Web dall'app

Le app daemon .NET possono chiamare un'API Web.NET daemon apps can call a web API. Le app daemon .NET possono anche chiamare diverse API Web preapprovate.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Chiamata di un'API Web da un'applicazione daemonCalling a web API from a daemon application

Ecco come usare il token per chiamare un'API:Here's how to use the token to call an API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

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

## <a name="calling-several-apis"></a>Chiamata di più API

Per le app daemon, le API Web chiamate devono essere preapprovate. Non c'è consenso incrementale con le app daemon. (Non esiste alcuna interazione con l'utente.) L'amministratore tenant deve fornire il consenso in anticipo per l'applicazione e tutte le autorizzazioni API. Se si desidera chiamare più API, è necessario acquisire un token `AcquireTokenForClient`per ogni risorsa, ogni volta che si chiama . MSAL utilizzerà la cache dei token dell'applicazione per evitare chiamate di servizio non necessarie.

## <a name="next-steps"></a>Passaggi successivi

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon app - passare alla produzione](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon app - passare alla produzione](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon app - passare alla produzione](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
