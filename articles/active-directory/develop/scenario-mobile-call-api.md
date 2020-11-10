---
title: Chiamare un'API Web da un'app per dispositivi mobili | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app per dispositivi mobili che chiama API Web e chiamare un'API Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: d7cad0592c5c4c0487f582ce5405c275b94b7bd0
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444028"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Chiamare un'API Web da un'app per dispositivi mobili

Dopo l'accesso di un utente all'app e la ricezione dei token, Microsoft Authentication Library (MSAL) espone le informazioni sull'utente, l'ambiente dell'utente e i token rilasciati. L'app può usare questi valori per chiamare un'API Web o visualizzare un messaggio di benvenuto all'utente.

In questo articolo si esaminerà per prima cosa il risultato di MSAL, quindi verrà illustrato come usare un token di accesso da `AuthenticationResult` o `result` per chiamare un'API Web protetta.

## <a name="msal-result"></a>Risultato di MSAL
MSAL restituisce i valori seguenti: 

- `AccessToken` chiama le API Web protette in una richiesta di connessione HTTP.
- `IdToken` contiene informazioni utili sull'utente connesso. Tali informazioni includono il nome dell'utente, il tenant principale e un identificatore univoco per l'archiviazione.
- `ExpiresOn` è la scadenza del token. MSAL gestisce l'aggiornamento automatico di un'app.
- `TenantId` è l'identificatore del tenant in cui l'utente ha eseguito l'accesso. Per gli utenti guest in Azure Active Directory (Azure AD) B2B, questo valore identifica il tenant in cui l'utente ha eseguito l'accesso, non il tenant principale dell'utente.  
- `Scopes` indica gli ambiti concessi con il token. Gli ambiti concessi potrebbero essere un sottoinsieme degli ambiti richiesti.

MSAL fornisce anche un'astrazione per il valore `Account`. Un valore `Account` rappresenta l'account connesso dell'utente corrente:

- `HomeAccountIdentifier` identifica il tenant principale dell'utente.
- `UserName` è il nome utente preferito dell'utente. Per gli utenti di Azure AD B2C, questo valore potrebbe essere vuoto.
- `AccountIdentifier` identifica l'utente connesso. Nella maggior parte dei casi, questo valore è uguale al valore di `HomeAccountIdentifier`, a meno che l'utente non sia un guest in un altro tenant.

## <a name="call-an-api"></a>Chiamare un'API

Dopo aver ottenuto il token di accesso, è possibile chiamare un'API Web. L'app userà il token per creare una richiesta HTTP e quindi eseguire la richiesta.

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

I metodi per acquisire i token restituiscono un oggetto `MSALResult`. `MSALResult` espone una proprietà `accessToken`. È possibile usare `accessToken` per chiamare un'API Web. Aggiungere questa proprietà all'intestazione dell'autorizzazione HTTP prima di effettuare la chiamata per accedere all'API Web protetta.

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

## <a name="make-several-api-requests"></a>Effettuare diverse richieste API

Se è necessario chiamare la stessa API più volte o chiamare più API, durante la creazione dell'app considerare gli argomenti seguenti.

- **Consenso incrementale** : Microsoft Identity Platform consente alle app di ottenere il consenso dell'utente quando sono necessarie le autorizzazioni, anziché interamente all'inizio. Ogni volta che l'app è pronta per chiamare un'API, dovrà richiedere solo gli ambiti necessari.

- **Accesso condizionale** : quando si effettuano diverse richieste API, in determinati scenari potrebbe essere necessario soddisfare requisiti di accesso condizionale aggiuntivi. In questo modo, i requisiti possono aumentare se la prima richiesta non ha criteri di accesso condizionale e l'app tenta di accedere automaticamente a una nuova API che richiede l'accesso condizionale. Per gestire questo problema, assicurarsi di rilevare gli errori dalle richieste automatiche e di essere preparati per effettuare una richiesta interattiva.  Per altre informazioni, vedere [Linee guida per l'accesso condizionale](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Chiamare diverse API usando il consenso incrementale e l'accesso condizionale

Se è necessario chiamare diverse API per lo stesso utente, dopo aver acquisito un token per l'utente è possibile evitare di chiedere ripetutamente le credenziali all'utente chiamando successivamente `AcquireTokenSilent` per ottenere un token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

L'interazione è necessaria nei casi seguenti:

- L'utente ha concesso il consenso per la prima API, ma deve ora fornirlo per altri ambiti. In questo caso, si usa il consenso incrementale.
- La prima API non richiede l'[autenticazione a più fattori](../authentication/concept-mfa-howitworks.md), ma l'API successiva la richiede.

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

Passare all'articolo successivo in questo scenario, [passare alla produzione](scenario-mobile-production.md).
