---
title: App desktop che chiama le API Web (chiamata a un'API Web)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app desktop che chiama API Web (chiamando un'API Web)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8160ec489f891764b102b5ba23a687b53376f738
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175369"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>App desktop che chiama le API Web-chiamare un'API Web

Ora che si dispone di un token, è possibile chiamare un'API Web protetta.

## <a name="calling-a-web-api"></a>Chiamata a un'API Web

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Chiamata di un'API Web in MSAL per iOS e macOS

I metodi per acquisire i token restituiscono un oggetto `MSALResult`. `MSALResult` espone una proprietà `accessToken` che può essere usata per chiamare un'API Web. Il token di accesso deve essere aggiunto all'intestazione di autorizzazione HTTP, prima di effettuare la chiamata per accedere all'API Web protetta.

Objective-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };

NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift:

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Chiamata di diverse API: consenso incrementale e accesso condizionale

Se è necessario chiamare diverse API per lo stesso utente, una volta ottenuto un token per la prima API, è possibile chiamare semplicemente `AcquireTokenSilent`e si otterrà un token per le altre API in modo invisibile all'utente nella maggior parte dei casi.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

I casi in cui è necessaria l'interazione sono i seguenti:

- L'utente ha acconsentito alla prima API, ma ora deve fornire il consenso per più ambiti (consenso incrementale)
- La prima API non richiede l'autenticazione a più fattori, ma la successiva.

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
---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passa all'ambiente di produzione](scenario-desktop-production.md)
