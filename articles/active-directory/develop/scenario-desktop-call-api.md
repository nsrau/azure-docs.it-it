---
title: Chiamare le API Web da un'app desktop - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'app desktop che chiama le API Web
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
ms.openlocfilehash: 2b3d9fdc163d0661670f3d0cf6e6a276c8b691bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702165"
---
# <a name="desktop-app-that-calls-web-apis-call-a-web-api"></a>App desktop che chiama API Web: chiamare un'API Web

Ora che si dispone di un token, è possibile chiamare un'API Web protetta.

## <a name="call-a-web-api"></a>Chiamare un'API Web

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
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

# <a name="macos"></a>[Macos](#tab/macOS)

## <a name="call-a-web-api-in-msal-for-ios-and-macos"></a>Chiamare un'API Web in MSAL per iOS e macOSCall a web API in MSAL for iOS and macOS

I metodi per acquisire `MSALResult` token restituiscono un oggetto. `MSALResult`espone una `accessToken` proprietà che può essere utilizzata per chiamare un'API Web. Aggiungere un token di accesso all'intestazione di autorizzazione HTTP prima di effettuare la chiamata per accedere all'API Web protetta.

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

## <a name="call-several-apis-incremental-consent-and-conditional-access"></a>Chiamare diverse API: consenso incrementale e accesso condizionaleCall several APIs: Incremental consent and conditional access

Per chiamare più API per lo stesso utente, dopo aver ottenuto un token per la prima API, chiamare `AcquireTokenSilent`. Otterrai un token per le altre API in modo invisibile all'utente la maggior parte del tempo.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

L'interazione è necessaria quando:

- L'utente ha acconsentito per la prima API, ma ora deve acconsentire a più ambiti. Questo tipo di consenso è noto come consenso incrementale.
- La prima API non richiede l'autenticazione a più fattori, ma quella successiva.

```csharp
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
> [Passare in produzione](scenario-desktop-production.md)
