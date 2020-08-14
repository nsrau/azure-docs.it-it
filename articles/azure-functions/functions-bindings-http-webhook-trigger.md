---
title: Trigger HTTP di Funzioni di Azure
description: Informazioni su come chiamare una funzione di Azure tramite HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 29ec547a6033b77d92ad7949df286dc94e3243a2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213928"
---
# <a name="azure-functions-http-trigger"></a>Trigger HTTP di Funzioni di Azure

Un trigger HTTP consente di richiamare una funzione con una richiesta HTTP e può essere usato per compilare API senza server e rispondere ai webhook.

Il valore restituito predefinito per una funzione attivata tramite HTTP è:

- `HTTP 204 No Content` con un corpo vuoto in Funzioni 2.x e versioni successive
- `HTTP 200 OK` con un corpo vuoto in Funzioni 1.x

Per modificare la risposta HTTP, configurare un'[associazione di output](./functions-bindings-http-webhook-output.md).

Per ulteriori informazioni sulle associazioni HTTP, vedere la [panoramica](./functions-bindings-http-webhook.md) e i [riferimenti all’associazione di output](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente mostra una [funzione in C#](functions-dotnet-class-library.md) che cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP. Si noti che il valore restituito viene usato per l'associazione di output, ma non è necessario un attributo di valore restituito.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente mostra un'associazione di trigger in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

Ecco il file *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C# associato a un oggetto `HttpRequest`:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

È possibile associare a un oggetto personalizzato invece di `HttpRequest`. Questo oggetto viene creato dal corpo della richiesta e analizzato come JSON. Analogamente, un tipo può essere passato all’associazione di output della risposta HTTP e restituito come corpo della risposta, con un codice di stato `200`.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente illustra un'associazione di trigger in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

Ecco il file *function.json*:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

L'esempio seguente mostra un'associazione di trigger in un file *function.json* e una [funzione Python](functions-reference-python.md) che usa l'associazione. La funzione cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

Ecco il file *function.json*:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

# <a name="java"></a>[Java](#tab/java)

* [Leggere un parametro dalla stringa di query](#read-parameter-from-the-query-string)
* [Leggere il corpo da una richiesta POST](#read-body-from-a-post-request)
* [Leggere un parametro da una route](#read-parameter-from-a-route)
* [Leggere il corpo POJO da una richiesta POST](#read-pojo-body-from-a-post-request)

Gli esempi riportati di seguito illustrano l’associazione con trigger HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Leggere un parametro dalla stringa di query

Questo esempio legge un parametro, denominato `id`, dalla stringa di query e lo usa per creare un documento JSON restituito al client, con tipo di contenuto `application/json`.

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Leggere il corpo da una richiesta POST

Questo esempio legge il corpo di una richiesta POST, come `String`, e lo usa per creare un documento JSON restituito al client, con tipo di contenuto `application/json`.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Leggere un parametro da una route

Questo esempio legge un parametro obbligatorio, denominato `id`, e un parametro facoltativo `name` dal percorso della route e usa tali parametri per creare un documento JSON restituito al client, con tipo di contenuto `application/json`. T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>Leggere il corpo POJO da una richiesta POST

Ecco il codice per la classe `ToDoItem` a cui si fa riferimento in questo esempio:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Questo esempio legge il corpo di una richiesta POST. Il corpo della richiesta viene automaticamente deserializzato in un oggetto `ToDoItem` e viene restituito al client, con tipo di contenuto `application/json`. Il parametro `ToDoItem` viene serializzato dal runtime di Funzioni poiché è assegnato alla proprietà `body` della classe `HttpMessageResponse.Builder`.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

Nelle [librerie delle classi C#](functions-dotnet-class-library.md) e Java, è disponibile l’attributo `HttpTrigger` per configurare la funzione.

È possibile impostare il livello di autorizzazione e i metodi HTTP consentiti nei parametri di costruttore dell'attributo, nel tipo di webhook e un modello di route. Per ulteriori informazioni su queste impostazioni, vedere [configurazione](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Questo esempio illustra come usare l'attributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs).

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Per un esempio completo, vedere l’[esempio di trigger](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Questo esempio illustra come usare l'attributo [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java).

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Per un esempio completo, vedere l’[esempio di trigger](#example).

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `HttpTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
| **type** | n/d| Obbligatoria. Deve essere impostata su `httpTrigger`. |
| **direction** | n/d| Obbligatoria. Deve essere impostata su `in`. |
| **nome** | n/d| Obbligatoria. Nome della variabile usato nel codice della funzione per la richiesta o il corpo della richiesta. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina le eventuali chiavi che devono essere presenti nella richiesta per richiamare la funzione. Il livello di autorizzazione può corrispondere a uno dei valori seguenti: <ul><li><code>anonymous</code>&mdash;Non è richiesta nessuna chiave API.</li><li><code>function</code>&mdash;È richiesta una chiave API specifica della funzione. Questo è il valore predefinito se non ne viene specificato nessuno.</li><li><code>admin</code>&mdash;È richiesta la chiave master.</li></ul> Per altre informazioni, consultare la sezione sulle [chiavi di autorizzazione](#authorization-keys). |
| **methods** |**Metodi** | Matrice di metodi HTTP a cui la funzione risponde. Se non viene specificata, la funzione risponde a tutti i metodi HTTP. Vedere [Personalizzare l'endpoint HTTP](#customize-the-http-endpoint). |
| **route** | **Route** | Definisce il modello di route, controllando a quali URL di richiesta risponde la funzione. Il valore predefinito, se non ne viene specificato nessuno, è `<functionname>`. Per altre informazioni, vedere [Personalizzare l'endpoint HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Supportato solo per la versione di runtime 1.x._<br/><br/>Configura il trigger HTTP perché funga da ricevitore [webhook](https://en.wikipedia.org/wiki/Webhook) per il provider specificato. Non impostare la proprietà `methods` se si imposta questa proprietà. Il tipo di webhook può essere uno dei seguenti:<ul><li><code>genericJson</code>&mdash;Endpoint di webhook per uso generico senza logica per un provider specifico. Questa impostazione limita le richieste solo a quelle che usano HTTP POST e con il tipo di contenuto `application/json`.</li><li><code>github</code>&mdash;La funzione risponde ai [webhook GitHub](https://developer.github.com/webhooks/). Non usare la proprietà _authLevel_ con webhook GitHub. Per altre informazioni, vedere la sezione con relativa ai webhook GitHub più avanti in questo articolo.</li><li><code>slack</code>&mdash;La funzione risponde ai [webhook Slack](https://api.slack.com/outgoing-webhooks). Non usare la proprietà _authLevel_ con webhook Slack. Per altre informazioni, vedere la sezione con relativa ai webhook Slack più avanti in questo articolo.</li></ul>|

## <a name="payload"></a>Payload

Il tipo di input del trigger viene dichiarato come `HttpRequest` o come tipo personalizzato. Se si sceglie `HttpRequest`, si ottiene accesso completo all'oggetto richiesta. Per un tipo personalizzato, il runtime cerca di analizzare il corpo della richiesta JSON per impostare le proprietà dell'oggetto.

## <a name="customize-the-http-endpoint"></a>Personalizzare l'endpoint HTTP

Per impostazione predefinita, quando si crea una funzione per un trigger HTTP la funzione può essere indirizzata con una route nel formato seguente:

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

È possibile personalizzare questa route tramite la proprietà `route` facoltativa nell'associazione di input del trigger HTTP. Ad esempio, il file *function.json* seguente definisce una proprietà `route` per un trigger HTTP:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Con questa configurazione, la funzione può ora essere indirizzata con la route seguente invece che con quella originale.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Questa configurazione consente al codice della funzione di supportare due parametri nell'indirizzo, _category_ e _id_.

# <a name="c"></a>[C#](#tab/csharp)

I parametri sono compatibili con qualsiasi [vincolo di route dell'API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints). Il codice di funzione C# seguente usa entrambi i parametri.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

I parametri sono compatibili con qualsiasi [vincolo di route dell'API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints). Il codice di funzione C# seguente usa entrambi i parametri.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In Node, il runtime di Funzioni fornisce il corpo della richiesta dall'oggetto `context`. Per altre informazioni, vedere l'[esempio di trigger JavaScript](#example).

L’esempio seguente mostra come leggere i parametri di route da `context.bindingData`.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

Il contesto di esecuzione della funzione viene esposto tramite un parametro dichiarato come `func.HttpRequest`. Questa istanza consente a una funzione di accedere ai parametri di route di dati, ai valori della stringa di query e ai metodi che consentono di restituire le risposte HTTP.

Dopo che sono stati definiti, i parametri di route sono disponibili alla funzione tramite chiamata al metodo `route_params`.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

Il contesto di esecuzione della funzione sono le proprietà come dichiarate nell'attributo `HttpTrigger`. L'attributo consente di definire i parametri di route, i livelli di autorizzazione, i verbi HTTP e l'istanza della richiesta in ingresso.

I parametri di route vengono definiti tramite l'attributo `HttpTrigger`.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Per impostazione predefinita, tutte le route di funzione sono precedute da *api*. È inoltre possibile personalizzare o rimuovere il prefisso con la proprietà `extensions.http.routePrefix` nel file [host.json](functions-host-json.md). Nell'esempio seguente viene rimosso il prefisso della route *api* usando una stringa vuota per il prefisso nel file *host.json*.

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>Uso dei parametri di route

I parametri di route che definiscono il modello di `route` di una funzione sono disponibili per ogni associazione. Se, ad esempio, è presente una route definita come `"route": "products/{id}"`, un'associazione di archiviazione tabelle può usare il valore del parametro `{id}` nella configurazione dell'associazione.

La configurazione seguente illustra in che modo il parametro `{id}` viene passato a `rowKey` dell'associazione.

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>Utilizzo delle identità client

Se l'app per le funzioni usa l'[autenticazione/autorizzazione di Servizio app ](../app-service/overview-authentication-authorization.md), è possibile visualizzare informazioni sui client autenticati dal codice. Queste informazioni sono disponibili come [intestazioni delle richieste inserite dalla piattaforma](../app-service/app-service-authentication-how-to.md#access-user-claims). 

È anche possibile leggere queste informazioni dai dati di binding. Questa funzionalità è disponibile solo per il runtime di Funzioni 2.x e versioni successive. È anche al momento disponibile solo per i linguaggi .NET.

# <a name="c"></a>[C#](#tab/csharp)

Le informazioni relative ai client autenticati sono disponibili come [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal è disponibile come parte del contesto della richiesta, come illustrato nell'esempio seguente:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

In alternativa, è possibile includere semplicemente ClaimsPrincipal come parametro aggiuntivo nella firma della funzione:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Le informazioni relative ai client autenticati sono disponibili come [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal è disponibile come parte del contesto della richiesta, come illustrato nell'esempio seguente:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

In alternativa, è possibile includere semplicemente ClaimsPrincipal come parametro aggiuntivo nella firma della funzione:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'utente autenticato è disponibile tramite [Intestazioni HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

L'utente autenticato è disponibile tramite [Intestazioni HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="java"></a>[Java](#tab/java)

L'utente autenticato è disponibile tramite [Intestazioni HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>Chiavi di accesso alle funzioni

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>Ottenere le chiavi

Le chiavi vengono archiviate come parte dell'app per le funzioni in Azure e crittografate inattive. Per visualizzare le chiavi, crearne di nuove o aggiornare le chiavi con nuovi valori, passare a una delle funzioni attivate da HTTP nel [portale di Azure](https://portal.azure.com) e selezionare **Gestisci**.

![Gestire le chiavi di funzione nel portale.](./media/functions-bindings-http-webhook/manage-function-keys.png)

È possibile ottenere le chiavi di funzione a livello di codice tramite l’uso di [API di gestione delle chiavi](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

## <a name="api-key-authorization"></a>Autorizzazione della chiave API

La maggior parte dei modelli di trigger HTTP richiedono una chiave API nella richiesta. La richiesta HTTP è quindi in genere simile al seguente URL:

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

La chiave può essere inclusa in una variabile della stringa di query denominata `code`, come sopra. Può anche essere inclusa in un'intestazione HTTP `x-functions-key`. Il valore della chiave può essere una chiave di funzione definita per la funzione o una chiave host.

È possibile consentire le richieste anonime, che non richiedono chiavi. È anche possibile richiedere l'uso della chiave master. Per modificare il livello di autorizzazione predefinito, usare la proprietà `authLevel` nel file JSON di binding. Per altre informazioni, vedere [Trigger - configurazione](#configuration)

> [!NOTE]
> Quando si eseguono funzioni localmente, l'autorizzazione viene disabilitata indipendentemente dall'impostazione del livello di autorizzazione specificata. Dopo la pubblicazione in Azure, viene applicata l'impostazione `authLevel` del trigger. Le chiavi sono comunque necessarie durante l’[esecuzione in locale in un contenitore](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Proteggere un endpoint HTTP nell'ambiente di produzione

Per proteggere completamente gli endpoint di funzione nell'ambiente di produzione, è consigliabile implementare una delle opzioni di sicurezza a livello di app per le funzioni seguenti. Quando si usa uno di questi metodi di sicurezza a livello di app per le funzioni, è necessario impostare il livello di autorizzazione delle funzioni attivate da HTTP su `anonymous`.

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>Distribuire l'app per le funzioni in isolamento

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhook

> [!NOTE]
> La modalità webhook è disponibile solo per il runtime di funzioni versione 1.x. Questa modifica è stata apportata per migliorare le prestazioni dei trigger HTTP nella versione 2.x e successive.

Nella versione 1.x, i modelli webhook forniscono una convalida aggiuntiva per i payload di webhook. Nella versione 2.x e successive, il trigger HTTP di base è ancora funzionante ed è l'approccio consigliato per i webhook. 

### <a name="github-webhooks"></a>Webhook GitHub

Per rispondere ai webhook GitHub, creare prima di tutto la funzione con un trigger HTTP e impostare la proprietà **webHookType** su `github`. Copiare quindi l'URL e la chiave API nella pagina **Aggiungi webhook** del repository GitHub. 

![Screenshot che illustra come aggiungere un webhook per la funzione.](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Webhook Slack

Il webhook Slack genera automaticamente un token invece di consentire di specificarlo, quindi è necessario configurare una chiave specifica della funzione con il token da Slack. Vedere [Chiavi di autorizzazione](#authorization-keys).

## <a name="webhooks-and-keys"></a>Chiavi e webhook

L'autorizzazione webhook viene gestita dal componente ricevitore dei webhook, che fa parte del trigger HTTP, e il meccanismo varia in base al tipo di webhook. Ogni meccanismo si basa su una chiave. Per impostazione predefinita, viene usata la chiave di funzione denominata "default". Per usare una chiave diversa, configurare il provider di webhook per inviare il nome della chiave con la richiesta in uno dei modi seguenti:

* **Stringa di query**: il provider passa il nome della chiave nel parametro della stringa di query `clientid`, ad esempio `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Intestazione della richiesta**: il provider passa il nome della chiave nell'intestazione `x-functions-clientid`.

## <a name="content-types"></a>Tipi di contenuto

Per passare dati binari e del modulo a una funzione non C # è necessario usare l'intestazione Content-Type appropriata. I tipi di contenuto supportati includono `octet-stream` per i dati binari e i [tipi multipart](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart).

### <a name="known-issues"></a>Problemi noti

Nelle funzioni non C #, le richieste inviate con Content-Type `image/jpeg` generano un `string` valore passato alla funzione. In casi come questi, è possibile convertire manualmente il `string` valore in una matrice di byte per accedere ai dati binari non elaborati.

## <a name="limits"></a>Limiti

La lunghezza della richiesta HTTP è limitata a 100 MB (104.857.600 byte) e la lunghezza dell'URL è limitata a 4 KB (4096 byte). Questi limiti vengono specificati dall'elemento `httpRuntime` del [file web.config](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config) del runtime.

Se una funzione che usa il trigger HTTP non viene completata entro 230 secondi, [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) raggiungerà il timeout e restituirà un errore HTTP 502. La funzione rimarrà in esecuzione, ma non riuscirà a restituire una risposta HTTP. Per le funzioni con esecuzione prolungata, è consigliabile seguire modelli asincroni e restituire una posizione in cui è possibile effettuare il ping dello stato della richiesta. Per informazioni su quanto tempo può durare l'esecuzione di una funzione, vedere [Scalabilità e hosting - Piano a consumo](functions-scale.md#timeout).


## <a name="next-steps"></a>Passaggi successivi

- [Restituire una risposta HTTP da una funzione](./functions-bindings-http-webhook-output.md)
