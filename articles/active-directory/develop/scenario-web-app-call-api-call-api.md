---
title: Chiamare un'API Web da un'app Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'app Web che chiama API Web (chiamando un'API Web protetta)
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
ms.openlocfilehash: 12802ab6dcfbbe5a1c5576ab672ead864dd0b4ae
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559871"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Un'app Web che chiama le API Web: chiamare un'API Web

Ora che si dispone di un token, è possibile chiamare un'API Web protetta.

## <a name="call-a-protected-web-api"></a>Chiamare un'API Web protetta

La chiamata di un'API Web protetta dipende dal linguaggio e dal Framework scelti:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Di seguito è riportato il codice semplificato per `HomeController`l'azione di. Questo codice ottiene un token per chiamare Microsoft Graph. Il codice è stato aggiunto per illustrare come chiamare Microsoft Graph come API REST. L'URL per l'API Microsoft Graph viene fornito nel file appSettings. JSON e viene letto in una variabile denominata `webOptions`:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 
  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```

> [!NOTE]
> È possibile usare lo stesso principio per chiamare qualsiasi API Web.
>
> La maggior parte delle API Web di Azure fornisce un SDK che semplifica la chiamata dell'API. Questo vale anche per Microsoft Graph. Nell'articolo successivo si apprenderà come trovare un'esercitazione che illustra l'uso dell'API.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

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
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare in produzione](scenario-web-app-call-api-production.md)
