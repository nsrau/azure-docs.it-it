---
title: Chiamare un'API Web da un'app Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app Web che chiama API Web (chiamando un'API Web protetta)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 016a2917c02deaecf35c526426cb2a6dbc89e338
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173191"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Un'app Web che chiama le API Web: chiamare un'API Web

Ora che si dispone di un token, è possibile chiamare un'API Web protetta. In genere si chiama un'API downstream dal controller o dalle pagine dell'app Web.

## <a name="call-a-protected-web-api"></a>Chiamare un'API Web protetta

La chiamata di un'API Web protetta dipende dal linguaggio e dal Framework scelti:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Quando si usa *Microsoft. Identity. Web*, sono disponibili tre opzioni di utilizzo per la chiamata di un'API:

- [Opzione 1: chiamare Microsoft Graph con l'SDK di Microsoft Graph](#option-1-call-microsoft-graph-with-the-sdk)
- [Opzione 2: chiamare un'API Web downstream con la classe helper](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Opzione 3: chiamare un'API Web downstream senza la classe helper](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Opzione 1: chiamare Microsoft Graph con l'SDK

Si desidera chiamare Microsoft Graph. In questo scenario è stato aggiunto `AddMicrosoftGraph` in *Startup.cs* come specificato nella [configurazione del codice](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)ed è possibile inserire direttamente `GraphServiceClient` nel controller o nel costruttore della pagina per l'uso nelle azioni. La pagina Razor di esempio seguente mostra la foto dell'utente che ha eseguito l'accesso.

```CSharp
[Authorize]
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
 private readonly GraphServiceClient _graphServiceClient;

 public IndexModel(GraphServiceClient graphServiceClient)
 {
    _graphServiceClient = graphServiceClient;
 }

 public async Task OnGet()
 {
  var user = await _graphServiceClient.Me.Request().GetAsync();
  try
  {
   using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
   {
    byte[] photoByte = ((MemoryStream)photoStream).ToArray();
    ViewData["photo"] = Convert.ToBase64String(photoByte);
   }
   ViewData["name"] = user.DisplayName;
  }
  catch (Exception)
  {
   ViewData["photo"] = null;
  }
 }
}
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>Opzione 2: chiamare un'API Web downstream con la classe helper

Si vuole chiamare un'API Web diversa da Microsoft Graph. In tal caso, è stato aggiunto `AddDownstreamWebApi` in *Startup.cs* come specificato nella [configurazione del codice](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)ed è possibile inserire direttamente un `IDownstreamWebApi` servizio nel controller o nel costruttore della pagina e usarlo nelle azioni:

```CSharp
[Authorize]
[AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
public class TodoListController : Controller
{
  private IDownstreamWebApi _downstreamWebApi;
  private const string ServiceName = "TodoList";

  public TodoListController(IDownstreamWebApi downstreamWebApi)
  {
    _downstreamWebApi = downstreamWebApi;
  }

  public async Task<ActionResult> Details(int id)
  {
    var value = await _downstreamWebApi.CallWebApiForUserAsync(
      ServiceName,
      options =>
      {
        options.RelativePath = $"me";
      });
      return View(value);
  }
}
```

`CallWebApiForUserAsync`Dispone inoltre di override generici fortemente tipizzati che consentono di ricevere direttamente un oggetto. Ad esempio, il metodo seguente riceve un' `Todo` istanza, ovvero una rappresentazione fortemente tipizzata del codice JSON restituito dall'API Web.

```CSharp
    // GET: TodoList/Details/5
    public async Task<ActionResult> Details(int id)
    {
        var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
            ServiceName,
            null,
            options =>
            {
                options.HttpMethod = HttpMethod.Get;
                options.RelativePath = $"api/todolist/{id}";
            });
        return View(value);
    }
   ```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Opzione 3: chiamare un'API Web downstream senza la classe helper

Si è deciso di acquisire un token manualmente usando il `ITokenAcquisition` servizio e ora è necessario usare il token. In tal caso, il codice seguente continua il codice di esempio illustrato in [un'app Web che chiama API Web: acquisire un token per l'app](scenario-web-app-call-api-acquire-token.md). Il codice viene chiamato nelle azioni dei controller dell'app Web.

Dopo aver acquisito il token, usarlo come bearer token per chiamare l'API downstream, in questo caso Microsoft Graph.

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
> La maggior parte delle API Web di Azure fornisce un SDK che semplifica la chiamata dell'API come nel caso di Microsoft Graph. Vedere, ad esempio, [creare un'applicazione Web che autorizza l'accesso all'archiviazione BLOB con Azure ad](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per un esempio di un'app Web con Microsoft. Identity. Web e con Azure Storage SDK.

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

Passare all'articolo successivo in questo scenario, [passare alla produzione](scenario-web-app-call-api-production.md).