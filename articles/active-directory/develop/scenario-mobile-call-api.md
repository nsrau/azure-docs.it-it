---
title: "App per dispositivi mobili di chiamate di web API: chiamare un'API web | Piattaforma delle identità Microsoft"
description: Informazioni su come compilare un'app per dispositivi mobili che chiama web API (chiama un'API web)
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
ms.openlocfilehash: 46d8b138a566727f9172b627b8df3353e7216fa5
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550313"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>App per dispositivi mobili che chiama le API - web chiama un'API web

Dopo che l'app ha eseguito l'accesso utente e i token ricevuti, MSAL espone diversi tipi di informazioni sull'utente, l'ambiente dell'utente e i token rilasciati. L'app può usare questi valori per chiamare un'API web o visualizzare un messaggio di benvenuto all'utente.

In primo luogo, esamineremo il risultato MSAL. Quindi esamineremo come un token di accesso da usare la `AuthenticationResult` o `result` per chiamare un'API web protetta.

## <a name="msal-result"></a>Risultato MSAL
MSAL fornisce i valori seguenti: 

- `AccessToken`: Utilizzato per chiamare le API web protetta in una richiesta di connessione HTTP.
- `IdToken`: Contiene informazioni utili relative all'utente connesso, come il nome dell'utente, il tenant principale e un identificatore univoco per l'archiviazione.
- `ExpiresOn`: L'ora di scadenza del token. MSAL gestisce l'aggiornamento automatico per le app.
- `TenantId`: L'identificatore del tenant di cui l'utente connesso in a. Per gli utenti guest (Azure Active Directory B2B), questo valore identifica il tenant di cui l'utente connesso con, non i tenant home dell'utente.  
- `Scopes`: Gli ambiti che sono state concesse con il token. Gli ambiti concessi potrebbero essere un subset degli ambiti richiesto.

MSAL fornisce anche un'astrazione per un `Account`. Un `Account` rappresenta account di accesso dell'utente corrente.

- `HomeAccountIdentifier`: Identificatore del tenant home dell'utente.
- `UserName`: Nome utente preferito dell'utente. Questo potrebbe essere vuoto per gli utenti di Azure Active Directory B2C.
- `AccountIdentifier`: L'identificatore dell'utente connesso. Questo valore sarà lo stesso come il `HomeAccountIdentifier` valore nella maggior parte dei casi, a meno che l'utente sia un utente guest in un altro tenant.

## <a name="call-an-api"></a>Chiamare un'API

Dopo aver ottenuto il token di accesso, è facile chiamare un'API web. L'app userà il token per costruire una richiesta HTTP, quindi eseguire la richiesta.

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

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
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

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Creazione di richieste API diverse

Se è necessario chiamare più volte la stessa API o se è necessario chiamare più API, prendere in considerazione quanto segue quando si compila l'app:

- **Consenso incrementale**: Piattaforma delle identità di Microsoft consente alle App ottenere il consenso dell'utente quando sono necessarie le autorizzazioni, anziché tutti all'inizio. Ogni volta che l'app è pronta chiamare un'API, è necessario richiedere solo gli ambiti che deve essere utilizzato.
- **Accesso condizionale**: In alcuni scenari, si potrebbero ottenere i requisiti di accesso condizionale aggiuntive quando si apportano molte richieste API. Questa situazione può verificarsi se la prima richiesta non ha applicato alcun criterio di accesso condizionale e l'app tenta di accedere automaticamente a una nuova API che richiede l'accesso condizionale. Per gestire questo scenario, assicurarsi di intercettare gli errori da richieste invisibile all'utente e prepararsi a eseguire una richiesta interattiva.  Per altre informazioni, vedere [linee guida per l'accesso condizionale](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare alla produzione](scenario-mobile-production.md)
