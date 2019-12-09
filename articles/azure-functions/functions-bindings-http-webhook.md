---
title: Trigger e associazioni HTTP di Funzioni di Azure
description: Informazioni su come usare i trigger e le associazioni HTTP in Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 481e2ab63263f77b513e6443479827cc9e168bbb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926348"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Trigger e associazioni HTTP di Funzioni di Azure

Questo articolo illustra come usare trigger e associazioni di output HTTP in Funzioni di Azure.

Un trigger HTTP può essere personalizzato per rispondere ai [webhook](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

Il codice in questo articolo usa per impostazione predefinita la sintassi che usa .NET Core, usata nelle funzioni versione 2. x e successive. Per informazioni sulla sintassi 1.x, consultare i [modelli delle funzioni 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Le associazioni HTTP sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) versione 1.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages-Functions 2. x e versioni successive

Le associazioni HTTP sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/).

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Un trigger HTTP consente di richiamare una funzione con una richiesta HTTP e può essere usato per compilare API senza server e rispondere ai webhook.

Per impostazione predefinita, un trigger HTTP restituisce HTTP 200 OK con un corpo vuoto in functions 1. x o HTTP 204 nessun contenuto con un corpo vuoto in functions 2. x e versioni successive. Per modificare la risposta, configurare un'[associazione di output HTTP](#output).

## <a name="trigger---example"></a>Trigger - esempio

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

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

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

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

È possibile associare a un oggetto personalizzato invece di `HttpRequest`. Questo oggetto viene creato dal corpo della richiesta e analizzato come JSON. Analogamente, un tipo può essere passato al binding di output della risposta HTTP e restituito come corpo della risposta, con un codice di stato 200.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

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

# <a name="javatabjava"></a>[Java](#tab/java)

* [Leggere un parametro dalla stringa di query](#read-parameter-from-the-query-string)
* [Leggere il corpo da una richiesta POST](#read-body-from-a-post-request)
* [Leggere un parametro da una route](#read-parameter-from-a-route)
* [Leggere il corpo POJO da una richiesta POST](#read-pojo-body-from-a-post-request)

L'esempio seguente mostra l'associazione di trigger HTTP in un file *function.json* e le rispettive [funzioni Java](functions-reference-java.md) che usano l'associazione. 

Ecco il file *function.json*:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
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

#### <a name="read-parameter-from-the-query-string"></a>Leggi parametro dalla stringa di query

Questo esempio legge un parametro, denominato ```id```, dalla stringa di query e lo usa per creare un documento JSON restituito al client, con tipo di contenuto ```application/json```. 

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

#### <a name="read-body-from-a-post-request"></a>Legge il corpo da una richiesta POST

Questo esempio legge il corpo di una richiesta POST, come ```String```, e lo usa per creare un documento JSON restituito al client, con tipo di contenuto ```application/json```.

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

#### <a name="read-parameter-from-a-route"></a>Leggi parametro da una route

Questo esempio legge un parametro obbligatorio, denominato ```id```, e un parametro facoltativo ```name``` dal percorso della route e usa tali parametri per creare un documento JSON restituito al client, con tipo di contenuto ```application/json```. T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Leggi il corpo di POJO da una richiesta POST

Ecco il codice per la classe ```ToDoItem``` a cui si fa riferimento in questo esempio:

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

Questo esempio legge il corpo di una richiesta POST. Il corpo della richiesta viene automaticamente deserializzato in un oggetto ```ToDoItem``` e viene restituito al client, con tipo di contenuto ```application/json```. Il parametro ```ToDoItem``` viene serializzato dal runtime di Funzioni poiché è assegnato alla proprietà ```body``` della classe ```HttpMessageResponse.Builder```.

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

## <a name="trigger---attributes"></a>Trigger - attributi

Nelle [ C# librerie di classi](functions-dotnet-class-library.md) e in Java, l'attributo `HttpTrigger` è disponibile per configurare la funzione.

È possibile impostare il livello di autorizzazione e i metodi HTTP consentiti nei parametri del costruttore dell'attributo, nel tipo di Webhook e in un modello di route. Per altre informazioni su queste impostazioni, vedere [Trigger - configurazione](#trigger---configuration).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Questo esempio illustra come usare l'attributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Per un esempio completo, vedere l' [esempio di trigger](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Questo esempio illustra come usare l'attributo [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) .

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

Per un esempio completo, vedere l' [esempio di trigger](#trigger---example).

---

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `HttpTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Description|
|---------|---------|----------------------|
| **type** | N/D| Obbligatoria. Deve essere impostata su `httpTrigger`. |
| **direction** | N/D| Obbligatoria. Deve essere impostata su `in`. |
| **nome** | N/D| Obbligatoria. Nome della variabile usato nel codice della funzione per la richiesta o il corpo della richiesta. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina le eventuali chiavi che devono essere presenti nella richiesta per richiamare la funzione. Il livello di autorizzazione può corrispondere a uno dei valori seguenti: <ul><li><code>anonymous</code>&mdash;Non è richiesta nessuna chiave API.</li><li><code>function</code>&mdash;È richiesta una chiave API specifica della funzione. Questo è il valore predefinito se non ne viene specificato nessuno.</li><li><code>admin</code>&mdash;È richiesta la chiave master.</li></ul> Per altre informazioni, consultare la sezione sulle [chiavi di autorizzazione](#authorization-keys). |
| **methods** |**Metodi** | Matrice di metodi HTTP a cui la funzione risponde. Se non viene specificata, la funzione risponde a tutti i metodi HTTP. Vedere [Personalizzare l'endpoint HTTP](#customize-the-http-endpoint). |
| **route** | **Route** | Definisce il modello di route, controllando a quali URL di richiesta risponde la funzione. Il valore predefinito, se non ne viene specificato nessuno, è `<functionname>`. Per altre informazioni, vedere [Personalizza l'endpoint HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Supportato solo per la versione di runtime 1.x._<br/><br/>Configura il trigger HTTP perché funga da ricevitore [webhook](https://en.wikipedia.org/wiki/Webhook) per il provider specificato. Non impostare la proprietà `methods` se si imposta questa proprietà. Il tipo di webhook può essere uno dei seguenti:<ul><li><code>genericJson</code>&mdash;Endpoint di webhook per uso generico senza logica per un provider specifico. Questa impostazione limita le richieste solo a quelle che usano HTTP POST e con il tipo di contenuto `application/json`.</li><li><code>github</code>&mdash;La funzione risponde ai [webhook GitHub](https://developer.github.com/webhooks/). Non usare la proprietà _authLevel_ con webhook GitHub. Per altre informazioni, vedere la sezione con relativa ai webhook GitHub più avanti in questo articolo.</li><li><code>slack</code>&mdash;La funzione risponde ai [webhook Slack](https://api.slack.com/outgoing-webhooks). Non usare la proprietà _authLevel_ con webhook Slack. Per altre informazioni, vedere la sezione con relativa ai webhook Slack più avanti in questo articolo.</li></ul>|

## <a name="trigger---usage"></a>Trigger - uso

Il tipo di input del trigger viene dichiarato come `HttpRequest` o come tipo personalizzato. Se si sceglie `HttpRequest`, si ottiene accesso completo all'oggetto richiesta. Per un tipo personalizzato, il runtime cerca di analizzare il corpo della richiesta JSON per impostare le proprietà dell'oggetto.

### <a name="customize-the-http-endpoint"></a>Personalizzare l'endpoint HTTP

Per impostazione predefinita, quando si crea una funzione per un trigger HTTP la funzione può essere indirizzata con una route nel formato seguente:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

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

In questo modo il codice della funzione può supportare due parametri nell'indirizzo, _category_ e _id_.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Nel nodo, il runtime di funzioni fornisce il corpo della richiesta dall'oggetto `context`. Per altre informazioni, vedere l'[esempio di trigger JavaScript](#trigger---example).

Nell'esempio seguente viene illustrato come leggere i parametri di route da `context.bindingData`.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Il contesto di esecuzione della funzione viene esposto tramite un parametro dichiarato come `func.HttpRequest`. Questa istanza consente a una funzione di accedere ai parametri della route di dati, ai valori della stringa di query e ai metodi che consentono di restituire risposte HTTP.

Una volta definito, i parametri della route sono disponibili per la funzione chiamando il metodo `route_params`.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Il contesto di esecuzione della funzione è proprietà dichiarate nell'attributo `HttpTrigger`. L'attributo consente di definire parametri di route, livelli di autorizzazione, verbi HTTP e l'istanza della richiesta in ingresso.

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

Per impostazione predefinita, tutte le route di funzione sono precedute da *api*. È inoltre possibile personalizzare o rimuovere il prefisso con la proprietà `http.routePrefix` nel file [host.json](functions-host-json.md). Nell'esempio seguente viene rimosso il prefisso della route *api* usando una stringa vuota per il prefisso nel file *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Utilizzo delle identità client

Se l'app per le funzioni usa l'[autenticazione/autorizzazione di Servizio app ](../app-service/overview-authentication-authorization.md), è possibile visualizzare informazioni sui client autenticati dal codice. Queste informazioni sono disponibili come [intestazioni delle richieste inserite dalla piattaforma](../app-service/app-service-authentication-how-to.md#access-user-claims). 

È anche possibile leggere queste informazioni dai dati di binding. Questa funzionalità è disponibile solo per il runtime di funzioni in 2. x e versioni successive. È anche al momento disponibile solo per i linguaggi .NET.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Le informazioni relative ai client autenticati sono disponibili come [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal è disponibile come parte del contesto della richiesta, come illustrato nell'esempio seguente:

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Le informazioni relative ai client autenticati sono disponibili come [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal è disponibile come parte del contesto della richiesta, come illustrato nell'esempio seguente:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L'utente autenticato è disponibile tramite [intestazioni HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="pythontabpython"></a>[Python](#tab/python)

L'utente autenticato è disponibile tramite [intestazioni HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javatabjava"></a>[Java](#tab/java)

L'utente autenticato è disponibile tramite [intestazioni HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

### <a name="authorization-keys"></a>Chiavi di autorizzazione

Funzioni consente di usare in fase di sviluppo le chiavi che rendono più difficile accedere agli endpoint di funzione HTTP.  Un trigger HTTP standard può richiedere nella richiesta sia presente una chiave API di questo tipo. 

> [!IMPORTANT]
> Mentre le chiavi possono risultare per offuscare gli endpoint HTTP durante lo sviluppo, non sono progettate come un modo per proteggere un trigger HTTP nell'ambiente di produzione. Per altre informazioni, vedere [Proteggere un endpoint HTTP nell'ambiente di produzione](#secure-an-http-endpoint-in-production).

> [!NOTE]
> Nel runtime 1.x di Funzioni i provider di webhook possono usare le chiavi per autorizzare le richieste in svariati modi, a seconda di ciò che il provider supporta. Questa procedura è illustrata in [Webhook e chiavi](#webhooks-and-keys). Il runtime di funzioni nella versione 2. x e versioni successive non include il supporto incorporato per i provider di webhook.

Esistono due tipi di chiavi:

* **Chiavi host**: queste chiavi vengono condivise da tutte le funzioni nell'app per le funzioni. Quando vengono usate come chiave API, consentono l'accesso a tutte le funzioni nell'app per le funzioni.
* **Chiavi di funzione**: queste chiavi si applicano solo alle funzioni specifiche sotto le quali vengono definite. Quando vengono usate come chiave API, consentono l'accesso solo a tale funzione.

Ogni chiave viene denominata per riferimento ed esiste una chiave predefinita (denominata "default") a livello di funzione e di host. Le chiavi di funzione hanno la precedenza sulle chiavi host. Se sono definite due chiavi con lo stesso nome, viene usata sempre la chiave di funzione.

Ogni app per le funzioni ha anche una speciale **chiave master**. Questa chiave è una chiave host denominata `_master`, che fornisce l'accesso amministrativo alle API di runtime. Questa chiave non può essere revocata. Quando si imposta un livello di autorizzazione `admin`, le richieste devono usare la chiave master. Qualsiasi altra chiave provoca un errore di autorizzazione.

> [!CAUTION]  
> Date le autorizzazioni elevate concesse dalla chiave master nell'app per le funzioni, è consigliabile non condividere questa chiave con terze parti o distribuirla in applicazioni client native. Fare attenzione quando si sceglie il livello di autorizzazione di amministratore.

### <a name="obtaining-keys"></a>Ottenere le chiavi

Le chiavi vengono archiviate come parte dell'app per le funzioni in Azure e crittografate inattive. Per visualizzare le chiavi, crearne di nuove o aggiornare le chiavi con nuovi valori, passare a una delle funzioni attivate da HTTP nel [portale di Azure](https://portal.azure.com) e selezionare **Gestisci**.

![Gestire le chiavi di funzione nel portale.](./media/functions-bindings-http-webhook/manage-function-keys.png)

È possibile ottenere le chiavi della funzione a livello di codice usando le [API di gestione delle chiavi](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

### <a name="api-key-authorization"></a>Autorizzazione della chiave API

La maggior parte dei modelli di trigger HTTP richiedono una chiave API nella richiesta. La richiesta HTTP è quindi in genere simile al seguente URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

La chiave può essere inclusa in una variabile della stringa di query denominata `code`, come sopra. Può anche essere inclusa in un'intestazione HTTP `x-functions-key`. Il valore della chiave può essere una chiave di funzione definita per la funzione o una chiave host.

È possibile consentire le richieste anonime, che non richiedono chiavi. È anche possibile richiedere l'uso della chiave master. Per modificare il livello di autorizzazione predefinito, usare la proprietà `authLevel` nel file JSON di binding. Per altre informazioni, vedere [Trigger - configurazione](#trigger---configuration)

> [!NOTE]
> Quando si eseguono funzioni localmente, l'autorizzazione viene disabilitata indipendentemente dall'impostazione del livello di autenticazione specificata. Dopo la pubblicazione in Azure, viene applicata l'impostazione `authLevel` del trigger. Le chiavi sono comunque necessarie quando si esegue [localmente in un contenitore](functions-create-function-linux-custom-image.md#run-the-image-locally).


### <a name="secure-an-http-endpoint-in-production"></a>Proteggere un endpoint HTTP nell'ambiente di produzione

Per proteggere completamente gli endpoint di funzione nell'ambiente di produzione, è consigliabile implementare una delle opzioni di sicurezza a livello di app per le funzioni seguenti:

* Attivare l'autenticazione/autorizzazione del servizio app per l'app per le funzioni. La piattaforma del servizio app consente di usare Azure Active Directory (AAD) e diversi provider di identità di terze parti per autenticare i client. È possibile usarla per implementare regole di autorizzazione personalizzate per le funzioni ed è possibile lavorare con le informazioni utente dal codice di funzione. Per altre informazioni, vedere [Autenticazione e autorizzazione in Servizio app di Azure](../app-service/overview-authentication-authorization.md) e [Utilizzo delle identità client](#working-with-client-identities).

* Usare Gestione API di Azure (APIM) per autenticare le richieste. APIM offre un'ampia gamma di opzioni di sicurezza di API per le richieste in ingresso. Per altre informazioni, vedere [Criteri di autenticazione di Gestione API di Azure](../api-management/api-management-authentication-policies.md). Con APIM, è possibile configurare l'app per le funzioni in modo che accetti le richieste solo dall'indirizzo IP dell'istanza APIM. Per altre informazioni, vedere [Restrizioni degli indirizzi IP](ip-addresses.md#ip-address-restrictions).

* Distribuire l'app per le funzioni in un ambiente di servizio app di Azure (ASE). L'ASE fornisce un ambiente di hosting dedicato in cui eseguire le funzioni. L'ASE consente di configurare un singolo gateway front-end che è possibile usare per autenticare tutte le richieste in ingresso. Per altre informazioni, vedere [Configurazione di un web application firewall (WAF) per l'ambiente del servizio app](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Quando si usa uno di questi metodi di sicurezza a livello di app per le funzioni, è necessario impostare il livello di autenticazione delle funzioni attivate da HTTP su `anonymous`.

### <a name="webhooks"></a>Webhook

> [!NOTE]
> La modalità webhook è disponibile solo per il runtime di funzioni versione 1.x. Questa modifica è stata apportata per migliorare le prestazioni dei trigger HTTP nella versione 2. x e versioni successive.

Nella versione 1.x, i modelli webhook forniscono una convalida aggiuntiva per i payload di webhook. Nella versione 2. x e versioni successive, il trigger HTTP di base continua a funzionare ed è l'approccio consigliato per i webhook. 

#### <a name="github-webhooks"></a>Webhook GitHub

Per rispondere ai webhook GitHub, creare prima di tutto la funzione con un trigger HTTP e impostare la proprietà **webHookType** su `github`. Copiare quindi l'URL e la chiave API nella pagina **Aggiungi webhook** del repository GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Webhook Slack

Il webhook Slack genera automaticamente un token invece di consentire di specificarlo, quindi è necessario configurare una chiave specifica della funzione con il token da Slack. Vedere [Chiavi di autorizzazione](#authorization-keys).

### <a name="webhooks-and-keys"></a>Chiavi e webhook

L'autorizzazione webhook viene gestita dal componente ricevitore dei webhook, che fa parte del trigger HTTP, e il meccanismo varia in base al tipo di webhook. Ogni meccanismo si basa su una chiave. Per impostazione predefinita, viene usata la chiave di funzione denominata "default". Per usare una chiave diversa, configurare il provider di webhook per inviare il nome della chiave con la richiesta in uno dei modi seguenti:

* **Stringa di query**: il provider passa il nome della chiave nel parametro della stringa di query `clientid`, ad esempio `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Intestazione della richiesta**: il provider passa il nome della chiave nell'intestazione `x-functions-clientid`.

## <a name="trigger---limits"></a>Trigger - Limiti

La lunghezza della richiesta HTTP è limitata a 100 MB (104.857.600 byte) e la lunghezza dell'URL è limitata a 4 KB (4096 byte). Questi limiti vengono specificati dall'elemento `httpRuntime` del [file web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config) del runtime.

Se una funzione che usa il trigger HTTP non viene completata entro 2,5 minuti circa, il gateway raggiungerà il timeout e restituirà un errore HTTP 502. La funzione rimarrà in esecuzione, ma non riuscirà a restituire una risposta HTTP. Per le funzioni con esecuzione prolungata, è consigliabile seguire modelli asincroni e restituire una posizione in cui è possibile effettuare il ping dello stato della richiesta. Per informazioni su quanto tempo può durare l'esecuzione di una funzione, vedere [Scalabilità e hosting - Piano a consumo](functions-scale.md#timeout).

## <a name="output"></a>Output

Usare l'associazione di output HTTP per rispondere al mittente della richiesta HTTP. Questa associazione richiede un trigger HTTP e consente di personalizzare la risposta associata alla richiesta del trigger. Se non viene specificata un'associazione di output HTTP, un trigger HTTP restituisce HTTP 200 OK con un corpo vuoto in functions 1. x o HTTP 204 nessun contenuto con un corpo vuoto in functions 2. x e versioni successive.

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json*. Per le librerie di classe C# non vi sono proprietà di attributo che corrispondano a queste proprietà *function.json*.

|Proprietà  |Description  |
|---------|---------|
| **type** |Il valore deve essere impostato su `http`. |
| **direction** | Il valore deve essere impostato su `out`. |
| **nome** | Nome della variabile usato nel codice della funzione per la risposta, o `$return`per usare il valore restituito. |

## <a name="output---usage"></a>Output - uso

Per inviare una risposta HTTP, usare modelli di risposta standard del linguaggio. In C# o nello script C#, eseguire il tipo restituito della funzione `IActionResult` o `Task<IActionResult>`. In C#, non è necessario un attributo del valore restituito.

Per le risposte di esempio, vedere l'[esempio di trigger](#trigger---example).

## <a name="hostjson-settings"></a>impostazioni host.json

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione nelle versioni 2. x e successive. Il file host. JSON di esempio seguente contiene solo le impostazioni della versione 2. x + per questa associazione. Per ulteriori informazioni sulle impostazioni di configurazione globali nelle versioni 2. x e successive, vedere informazioni di [riferimento su host. JSON per funzioni di Azure](functions-host-json.md).

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Proprietà  |Predefinito | Description |
|---------|---------|---------| 
| customHeaders|None|Consente di impostare intestazioni personalizzate nella risposta HTTP. Nell'esempio precedente l'intestazione `X-Content-Type-Options` viene aggiunta alla risposta per evitare l'analisi dei tipi di contenuto. |
|dynamicThrottlesEnabled|true<sup>\*</sup>|Quando è abilitata, questa impostazione determina la pipeline di elaborazione della richiesta per il controllo periodico delle prestazioni dei contatori del sistema, ad esempio connessioni/thread/processi/memoria/CPU e così via. Se uno di questi contatori supera una soglia massima predefinita (80%), le richieste verranno rifiutate con una risposta 429 "Occupato" fino a quando i contatori non tornano a livelli normali.<br/><sup>\*</sup> Il valore predefinito in un piano a consumo è `true`. Il valore predefinito in un piano dedicato è `false`.|
|HSTS|non abilitato|Quando `isEnabled` è impostato su `true`, viene applicato il [comportamento di sicurezza del trasporto http Strict (HSTS) di .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) , come definito nella [classe`HstsOptions`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). Nell'esempio precedente viene inoltre impostata la proprietà [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) su 10 giorni. Le proprietà supportate di `hsts` sono: <table><tr><th>Proprietà</th><th>Description</th></tr><tr><td>excludedHosts</td><td>Matrice di stringhe di nomi host per cui non viene aggiunta l'intestazione HSTS.</td></tr><tr><td>includeSubDomains</td><td>Valore booleano che indica se il parametro includeSubDomain dell'intestazione Strict-Transport-Security è abilitato.</td></tr><tr><td>maxAge</td><td>Stringa che definisce il parametro max-age dell'intestazione Strict-Transport-Security.</td></tr><tr><td>preload</td><td>Valore booleano che indica se il parametro preload dell'intestazione Strict-Transport-Security è abilitato.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Numero massimo di funzioni http eseguite in parallelo. Ciò consente di controllare la concorrenza e pertanto di semplificare la gestione dell'uso delle risorse. Ad esempio, potrebbe essere presente una funzione HTTP che usa una quantità di risorse di sistema (memoria/CPU/socket) tale da creare problemi quando la concorrenza è troppo elevata. Oppure potrebbe essere presente una funzione che invia richieste a un servizio di terze parti e tali chiamate devono essere a frequenza limitata. In questi casi potrebbe risultare utile l'applicazione di una limitazione. <br/><sup>*</sup> Il valore predefinito per un piano a consumo è 100. Il valore predefinito per un piano dedicato è unbounded (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|Il numero massimo di richieste in sospeso che verrà mantenuto in un determinato intervallo. Questo limite include le richieste che vengono messe in coda ma non hanno avviato l'esecuzione, nonché le esecuzioni in corso. Le richieste in arrivo che superano questo limite vengono rifiutate con la risposta 429 "Occupato". Ciò consente ai chiamanti di usare strategie di ripetizione dei tentativi basate sul tempo e di controllare la latenza massima delle richieste. Questa impostazione controlla solo l'accodamento che si verifica all'interno del percorso di esecuzione dell'host dello script. Altre code, ad esempio la coda di richieste ASP.NET, saranno valide e non interessate da questa impostazione. <br/><sup>\*</sup>valore predefinito \il per un piano a consumo è 200. Il valore predefinito per un piano dedicato è unbounded (`-1`).|
|routePrefix|api|Il prefisso della route che si applica a tutte le route. Utilizzare una stringa vuota per rimuovere il prefisso predefinito. |


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
