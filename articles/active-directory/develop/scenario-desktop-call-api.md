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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268289"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>App desktop che chiama le API Web-chiamare un'API Web

Ora che si dispone di un token, è possibile chiamare un'API Web protetta.

## <a name="calling-a-web-api-from-net"></a>Chiamata di un'API Web da .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Chiamata di un'API Web in MSAL per iOS e macOS

I metodi per acquisire i token restituiscono `MSALResult` un oggetto. `MSALResult`espone una `accessToken` proprietà che può essere usata per chiamare un'API Web. Il token di accesso deve essere aggiunto all'intestazione di autorizzazione HTTP, prima di effettuare la chiamata per accedere all'API Web protetta.

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

Swift

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Chiamata di diverse API: consenso incrementale e accesso condizionale

Se è necessario chiamare diverse API per lo stesso utente, una volta ottenuto un token per la prima API, è possibile chiamare `AcquireTokenSilent`solo e si otterrà un token per le altre API in modo invisibile all'utente nella maggior parte dei casi.

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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passa all'ambiente di produzione](scenario-desktop-production.md)
