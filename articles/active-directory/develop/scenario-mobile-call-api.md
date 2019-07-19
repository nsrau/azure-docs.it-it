---
title: App per dispositivi mobili che chiama API Web-chiamata a un'API Web | Piattaforma di identità Microsoft
description: Informazioni su come creare un'app per dispositivi mobili che chiama API Web (chiamando un'API Web)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1408c06570babfd93c46fdfc7a3c6754000bcbc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320847"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>App per dispositivi mobili che chiama API Web-chiamare un'API Web

Dopo che l'app ha eseguito l'accesso a un utente e ricevuto i token, MSAL espone diverse informazioni sull'utente, l'ambiente dell'utente e i token emessi. L'app può usare questi valori per chiamare un'API Web o visualizzare un messaggio di benvenuto all'utente.

In primo luogo, si esaminerà il risultato di MSAL. Si esaminerà quindi come usare un token di accesso da `AuthenticationResult` o `result` per chiamare un'API Web protetta.

## <a name="msal-result"></a>Risultato di MSAL
MSAL fornisce i valori seguenti: 

- `AccessToken`: Usato per chiamare le API Web protette in una richiesta di portatore HTTP.
- `IdToken`: Contiene informazioni utili sull'utente che ha eseguito l'accesso, ad esempio il nome dell'utente, il tenant principale e un identificatore univoco per l'archiviazione.
- `ExpiresOn`: Data di scadenza del token. MSAL gestisce l'aggiornamento automatico per le app.
- `TenantId`: Identificatore del tenant con cui l'utente ha eseguito l'accesso. Per gli utenti Guest (Azure Active Directory B2B), questo valore identificherà il tenant con cui l'utente ha eseguito l'accesso, non il tenant principale dell'utente.  
- `Scopes`: Gli ambiti concessi con il token. Gli ambiti concessi possono essere un subset degli ambiti richiesti.

MSAL fornisce anche un'astrazione per `Account`un oggetto. Un `Account` oggetto rappresenta l'account di accesso dell'utente corrente.

- `HomeAccountIdentifier`: Identificatore del tenant Home dell'utente.
- `UserName`: Nome utente preferito dell'utente. Questa operazione potrebbe essere vuota per Azure Active Directory B2C utenti.
- `AccountIdentifier`: Identificatore dell'utente che ha eseguito l'accesso. Questo valore sarà uguale `HomeAccountIdentifier` al valore nella maggior parte dei casi, a meno che l'utente non sia un guest in un altro tenant.

## <a name="call-an-api"></a>Chiamare un'API

Dopo aver avuto il token di accesso, è facile chiamare un'API Web. L'app userà il token per costruire una richiesta HTTP e quindi eseguirà la richiesta.

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

## <a name="making-several-api-requests"></a>Esecuzione di diverse richieste API

Se è necessario chiamare la stessa API più volte o se è necessario chiamare più API, tenere in considerazione quanto segue quando si compila l'app:

- **Consenso incrementale**: Microsoft Identity Platform consente alle app di ottenere il consenso dell'utente in quanto le autorizzazioni sono obbligatorie, anziché tutte all'inizio. Ogni volta che l'app è pronta per chiamare un'API, deve richiedere solo gli ambiti che deve usare.
- **Accesso condizionale**: In alcuni scenari, è possibile ottenere ulteriori requisiti di accesso condizionale quando si effettuano diverse richieste API. Questo problema può verificarsi se alla prima richiesta non sono applicati criteri di accesso condizionale e l'app tenta di accedere automaticamente a una nuova API che richiede l'accesso condizionale. Per gestire questo scenario, assicurarsi di intercettare gli errori dalle richieste automatiche e prepararsi a eseguire una richiesta interattiva.  Per altre informazioni, vedere [linee guida per l'accesso condizionale](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passa all'ambiente di produzione](scenario-mobile-production.md)
