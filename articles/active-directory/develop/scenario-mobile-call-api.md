---
title: "App per dispositivi mobili di chiamate di web API: chiamare un'API web | Piattaforma delle identità Microsoft"
description: Informazioni su come compilare un'app per dispositivi mobili che chiama le API Web (chiamando un'API Web)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075116"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>App per dispositivi mobili che chiama le API - web chiama un'API web

Una volta che l'app ha eseguito l'accesso utente e i token ricevuti, MSAL espone diversi tipi di informazioni sull'utente, il proprio ambiente e i token rilasciati. L'app può usare questi valori per visualizzare un messaggio di benvenuto a un utente o di chiamare un'API web.

In primo luogo, verrà presa in esame il risultato MSAL, quindi come usare un token di accesso dal `AuthenticationResult` o `result` per chiamare un'API web protetta.

## <a name="msal-result"></a>Risultato MSAL

- `AccessToken`: Utilizzato per chiamare le API web protetta in una richiesta di connessione HTTP.
- `IdToken`: Contiene attestazioni utili sull'utente connesso, ad esempio il nome, un tenant home e un identificatore univoco per l'archiviazione.
- `ExpiresOn`: l'ora di scadenza del token. MSAL gestisce l'aggiornamento automatico per le app.
- `TenantId`: Identificatore del tenant dell'utente usato per l'accesso. Per gli utenti guest (B2B di Azure AD), questo sarà il tenant di cui l'utente connesso con, non il tenant principale.  
- `Scopes`: gli ambiti che sono state concesse con il token. Può trattarsi di un subset di quella richiesta.

Inoltre, MSAL fornisce anche un'astrazione per un `Account`. Un Account rappresenta l'utente corrente firmato nell'account.

- `HomeAccountIdentifier`: Identificatore del tenant home dell'utente.
- `UserName`: Nome utente preferito dell'utente. Ciò può essere vuoto per gli utenti di Azure AD B2C.
- `AccountIdentifier`: Identificatore dell'utente connesso. Questo sarà lo stesso come il `HomeAccountIdentifier` nella maggior parte dei casi, a meno che l'utente è un utente guest in un altro tenant.

## <a name="calling-an-api"></a>Chiama un'API

Dopo aver creato il token di accesso pronto, è semplice chiamare un'API web. L'app verrà tenere questo token, costruire una richiesta HTTP ed eseguirlo.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
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

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put Access token in HTTP Request
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Creazione di richieste API diverse

Se è necessario chiamare la stessa API più volte o più API, esistono ulteriori considerazioni quando si compila l'app:

- ***Consenso incrementale***: Piattaforma delle identità di Microsoft consente alle App ottenere il consenso dell'utente, come l'autorizzazione sono necessarie, piuttosto che tutte le iniziali. Ogni volta che l'app è pronta chiamare un'API, è necessario richiedere solo gli ambiti che intende utilizzare.
- ***Accesso condizionale***: In alcuni scenari, è possibile ottenere ulteriori requisiti di accesso condizionale quando si effettuano richieste API diverse. Per gestire questo scenario, assicurarsi di intercettare gli errori da richieste invisibile all'utente e prepararsi a eseguire una richiesta interattiva. Questa situazione può verificarsi se la prima richiesta non ha applicato alcun criterio di accesso condizionale e l'app tenta di accedere automaticamente a una nuova API che richiede l'accesso condizionale. Per altre informazioni, vedere [linee guida per l'accesso condizionale](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare alla produzione](scenario-mobile-production.md)
