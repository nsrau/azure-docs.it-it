---
title: Chiamare un'API Web da un'app per dispositivi mobili | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app per dispositivi mobili che chiama le API Web. (Chiamare un'API Web).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 28f57c5657ce2f8537a654a7f67ed4481fab2c91
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882693"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Chiamare un'API Web da un'app per dispositivi mobili

Dopo che l'app ha eseguito l'accesso a un utente e riceve i token, Microsoft Authentication Library (MSAL) espone le informazioni sull'utente, l'ambiente dell'utente e i token emessi. L'app può usare questi valori per chiamare un'API Web o visualizzare un messaggio di benvenuto all'utente.

In questo articolo si esaminerà prima di tutto il risultato di MSAL. Si esaminerà quindi come usare un token di accesso da `AuthenticationResult` o `result` per chiamare un'API Web protetta.

## <a name="msal-result"></a>Risultato di MSAL
MSAL fornisce i valori seguenti: 

- `AccessToken`chiama API Web protette in una richiesta di portatore HTTP.
- `IdToken`contiene informazioni utili sull'utente che ha eseguito l'accesso. Queste informazioni includono il nome dell'utente, il tenant principale e un identificatore univoco per l'archiviazione.
- `ExpiresOn`Data di scadenza del token. MSAL gestisce l'aggiornamento automatico di un'app.
- `TenantId`identificatore del tenant in cui l'utente ha eseguito l'accesso. Per gli utenti guest in Azure Active Directory (Azure AD) B2B, questo valore identifica il tenant in cui l'utente ha eseguito l'accesso. Il valore non identifica il tenant principale dell'utente.  
- `Scopes`indica gli ambiti concessi con il token. Gli ambiti concessi possono essere un subset degli ambiti richiesti.

MSAL fornisce anche un'astrazione per `Account` un valore. Un `Account` valore rappresenta l'account di accesso dell'utente corrente:

- `HomeAccountIdentifier`identifica il tenant principale dell'utente.
- `UserName`nome utente preferito dell'utente. Questo valore può essere vuoto per Azure AD B2C utenti.
- `AccountIdentifier`identifica l'utente che ha eseguito l'accesso. Nella maggior parte dei casi, questo valore corrisponde al `HomeAccountIdentifier` valore a meno che l'utente non sia un guest in un altro tenant.

## <a name="call-an-api"></a>Chiamare un'API

Dopo aver avuto il token di accesso, è possibile chiamare un'API Web. L'app userà il token per compilare una richiesta HTTP e quindi eseguire la richiesta.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL per iOS e MacOS

I metodi per acquisire i token restituiscono `MSALResult` un oggetto. `MSALResult`espone una `accessToken` proprietà. È possibile usare `accessToken` per chiamare un'API Web. Aggiungere questa proprietà all'intestazione di autorizzazione HTTP prima di chiamare per accedere all'API Web protetta.

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

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Eseguire diverse richieste API

Se è necessario chiamare la stessa API più volte o se è necessario chiamare più API, considerare gli argomenti seguenti quando si compila l'app:

- **Consenso incrementale**: la piattaforma Microsoft Identity consente alle app di ottenere il consenso dell'utente quando sono necessarie le autorizzazioni anziché tutte all'inizio. Ogni volta che l'app è pronta per chiamare un'API, deve richiedere solo gli ambiti necessari.

- **Accesso condizionale**: quando si effettuano diverse richieste API, in determinati scenari potrebbe essere necessario soddisfare ulteriori requisiti di accesso condizionale. I requisiti possono aumentare in questo modo se la prima richiesta non ha criteri di accesso condizionale e l'app tenta di accedere automaticamente a una nuova API che richiede l'accesso condizionale. Per gestire questo problema, assicurarsi di intercettare gli errori dalle richieste automatiche e prepararsi a eseguire una richiesta interattiva.  Per altre informazioni, vedere [linee guida per l'accesso condizionale](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Chiamare diverse API usando il consenso incrementale e l'accesso condizionale

Se è necessario chiamare diverse API per lo stesso utente, dopo l'acquisizione di un token per l'utente, è possibile evitare di chiedere ripetutamente le credenziali dell'utente chiamando `AcquireTokenSilent` per ottenere un token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

L'interazione è obbligatoria nei casi seguenti:

- L'utente ha acconsentito alla prima API, ma ora deve fornire il consenso per più ambiti. In questo caso, si usa il consenso incrementale.
- La prima API non richiede l'autenticazione a più fattori, ma l'API successiva.

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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare in produzione](scenario-mobile-production.md)
