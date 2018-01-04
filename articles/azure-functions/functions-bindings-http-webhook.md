---
title: Associazioni HTTP e webhook in Funzioni di Azure
description: Informazioni su come usare trigger e associazioni HTTP e webhookin Funzioni di Azure.
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: funzioni di Azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server, HTTP, API, REST
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: mahender
ms.openlocfilehash: 080712e0a6c05348e7163f3c8e2055e6ff2806b2
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Associazioni HTTP e webhook in Funzioni di Azure

Questo articolo illustra come usare le associazioni HTTP in Funzioni di Azure. Funzioni di Azure supporta il trigger HTTP e le associazioni di output.

Un trigger HTTP può essere personalizzato per rispondere ai [webhook](https://en.wikipedia.org/wiki/Webhook). Un trigger webhook accetta solo un payload JSON e convalida l'oggetto JSON. Esistono versioni speciali di trigger webhook che semplificano la gestione dei webhook da alcuni provider, ad esempio GitHub e Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="trigger"></a>Trigger

Un trigger HTTP consente di richiamare una funzione con una richiesta HTTP e può essere usato per compilare API senza server e rispondere ai webhook. 

Per impostazione predefinita, un trigger HTTP risponde alla richiesta con un codice di stato HTTP 200 OK e un corpo vuoto. Per modificare la risposta, configurare un'[associazione di output HTTP](#http-output-binding).

## <a name="trigger---example"></a>Trigger - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#trigger---c-example)
* [Script c# (con estensione csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Trigger - esempio in C#

Nell'esempio seguente un [funzione c#](functions-dotnet-class-library.md) che cerca un `name` parametro nella stringa di query o il corpo della richiesta HTTP.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>Trigger - esempio di script C#

L'esempio seguente mostra un'associazione di trigger in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice script C# associato a un oggetto `HttpRequestMessage`:

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

È possibile associare a un oggetto personalizzato invece di `HttpRequestMessage`. Questo oggetto viene creato dal corpo della richiesta e analizzato come JSON. Analogamente, un tipo può essere passato al binding di output della risposta HTTP e restituito come corpo della risposta, con un codice di stato 200.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

### <a name="trigger---f-example"></a>Trigger - Esempio in F#

L'esempio seguente mostra un'associazione di trigger in un file *function.json* e una [funzione F#](functions-reference-fsharp.md) che usa l'associazione. La funzione cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice F#:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

È necessario un file `project.json` che usa NuGet per fare riferimento agli assembly `FSharp.Interop.Dynamic` e `Dynamitey`, come illustrato nell'esempio seguente:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>Trigger - esempio JavaScript

L'esempio seguente illustra un'associazione di trigger in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
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
     
## <a name="trigger---webhook-example"></a>Trigger - esempio di webhook

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#webhook---c-example)
* [Script c# (con estensione csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhook - esempio C#

Nell'esempio seguente un [funzione c#](functions-dotnet-class-library.md) che invia un HTTP 200 in risposta a una richiesta JSON generica.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhook - esempio di script C#

L'esempio seguente mostra un'associazione di trigger webhook in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. Questa funzione registra i commenti sui problemi di GitHub.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice script C#:

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>Webhook - esempio F#

L'esempio seguente mostra un'associazione di trigger webhook in un file *function.json* e una [funzione F#](functions-reference-fsharp.md) che usa l'associazione. Questa funzione registra i commenti sui problemi di GitHub.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice F#:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>Webhook - esempio JavaScript

L'esempio seguente mostra un'associazione di trigger webhook in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. Questa funzione registra i commenti sui problemi di GitHub.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice JavaScript:

```javascript
```

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Trigger - attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attributo, definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http).

È possibile impostare il livello di autorizzazione e i metodi HTTP consentiti nei parametri di costruttore dell'attributo. Sono disponibili proprietà per il modello di route e il tipo di webhook. Per altre informazioni su queste impostazioni, vedere [Trigger - configurazione](#trigger---configuration). Di seguito è mostrato un attributo `HttpTrigger` in una firma del metodo:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Per un esempio completo, vedere [Trigger - esempio c#](#trigger---c-example).

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `HttpTrigger`.


|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
| **type** | n/d| Obbligatoria. Deve essere impostata su `httpTrigger`. |
| **direction** | n/d| Obbligatoria. Deve essere impostata su `in`. |
| **nome** | n/d| Obbligatoria. Nome della variabile usato nel codice della funzione per la richiesta o il corpo della richiesta. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina le eventuali chiavi che devono essere presenti nella richiesta per richiamare la funzione. Il livello di autorizzazione può corrispondere a uno dei valori seguenti: <ul><li><code>anonymous</code>&mdash;Non è richiesta nessuna chiave API.</li><li><code>function</code>&mdash;È richiesta una chiave API specifica della funzione. Questo è il valore predefinito se non ne viene specificato nessuno.</li><li><code>admin</code>&mdash;È richiesta la chiave master.</li></ul> Per altre informazioni, consultare la sezione sulle [chiavi di autorizzazione](#authorization-keys). |
| **methods** |**Metodi** | Matrice di metodi HTTP a cui la funzione risponde. Se non viene specificata, la funzione risponde a tutti i metodi HTTP. Vedere [Personalizzare l'endpoint HTTP](#trigger---customize-the-http-endpoint). |
| **route** | **Route** | Definisce il modello di route, controllando a quali URL di richiesta risponde la funzione. Il valore predefinito, se non ne viene specificato nessuno, è `<functionname>`. Per altre informazioni, vedere [Personalizza l'endpoint HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Configura il trigger HTTP perché funga da ricevitore [webhook](https://en.wikipedia.org/wiki/Webhook) per il provider specificato. Non impostare la proprietà `methods` se si imposta questa proprietà. Il tipo di webhook può essere uno dei seguenti:<ul><li><code>genericJson</code>&mdash;Endpoint di webhook per uso generico senza logica per un provider specifico. Questa impostazione limita le richieste solo a quelle che usano HTTP POST e con il tipo di contenuto `application/json`.</li><li><code>github</code>&mdash;La funzione risponde ai [webhook GitHub](https://developer.github.com/webhooks/). Non usare la proprietà _authLevel_ con webhook GitHub. Per altre informazioni, vedere la sezione con relativa ai webhook GitHub più avanti in questo articolo.</li><li><code>slack</code>&mdash;La funzione risponde ai [webhook Slack](https://api.slack.com/outgoing-webhooks). Non usare la proprietà _authLevel_ con webhook Slack. Per altre informazioni, vedere la sezione con relativa ai webhook Slack più avanti in questo articolo.</li></ul>|

## <a name="trigger---usage"></a>Trigger - uso

Per le funzioni C# e F#, è possibile dichiarare `HttpRequestMessage` o un tipo personalizzato come tipo dell'input del trigger. Se si sceglie `HttpRequestMessage`, si ottiene accesso completo all'oggetto richiesta. Per un tipo personalizzato, Funzioni cerca di analizzare il corpo della richiesta JSON per impostare le proprietà dell'oggetto. 

Per le funzioni JavaScript, il runtime di Funzioni fornisce il corpo della richiesta invece dell'oggetto richiesta. Per altre informazioni, vedere l'[esempio di trigger JavaScript](#trigger---javascript-example).

### <a name="github-webhooks"></a>Webhook GitHub

Per rispondere ai webhook GitHub, creare prima di tutto la funzione con un trigger HTTP e impostare la proprietà **webHookType** su `github`. Copiare quindi l'URL e la chiave API nella pagina **Aggiungi webhook** del repository GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Per un esempio, vedere [Creare una funzione attivata da un webhook GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Webhook Slack

Il webhook Slack genera automaticamente un token invece di consentire di specificarlo, quindi è necessario configurare una chiave specifica della funzione con il token da Slack. Vedere [Chiavi di autorizzazione](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Personalizzare l'endpoint HTTP

Per impostazione predefinita, quando si crea una funzione per un trigger HTTP o un webhook, la funzione può essere indirizzata con una route nel formato seguente:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

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
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

In questo modo il codice della funzione può supportare due parametri nell'indirizzo, _category_ e _id_. I parametri sono compatibili con qualsiasi [vincolo di route dell'API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints). Il codice di funzione C# seguente usa entrambi i parametri.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Di seguito è mostrato il codice di funzione Node.js che usa gli stessi parametri di route.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

Per impostazione predefinita, tutte le route di funzione sono precedute da *api*. È inoltre possibile personalizzare o rimuovere il prefisso con la proprietà `http.routePrefix` nel file [host.json](functions-host-json.md). Nell'esempio seguente viene rimosso il prefisso della route *api* usando una stringa vuota per il prefisso nel file *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Chiavi di autorizzazione

I trigger HTTP consentono di usare le chiavi per una maggiore sicurezza. Un trigger HTTP standard può usare queste chiavi come chiave API, richiedendone la presenza nella richiesta. I webhook possono usare le chiavi per autorizzare le richieste in svariati modi, a seconda di ciò che il provider supporta.

Le chiavi vengono archiviate come parte dell'app per le funzioni in Azure e crittografate inattive. Per visualizzare le chiavi, crearne di nuove o aggiornare le chiavi con nuovi valori, passare a una delle funzioni nel portale e selezionare "Gestisci". 

Esistono due tipi di chiavi:

- **Chiavi host**: queste chiavi vengono condivise da tutte le funzioni nell'app per le funzioni. Quando vengono usate come chiave API, consentono l'accesso a tutte le funzioni nell'app per le funzioni.
- **Chiavi di funzione**: queste chiavi si applicano solo alle funzioni specifiche sotto le quali vengono definite. Quando vengono usate come chiave API, consentono l'accesso solo a tale funzione.

Ogni chiave viene denominata per riferimento ed esiste una chiave predefinita (denominata "default") a livello di funzione e di host. Le chiavi di funzione hanno la precedenza sulle chiavi host. Se sono definite due chiavi con lo stesso nome, viene usata sempre la chiave di funzione.

La **chiave master** è una chiave host predefinita denominata "_master", definita per ogni app per le funzioni. Questa chiave non può essere revocata. Fornisce l'accesso amministrativo alle API di runtime. Per usare `"authLevel": "admin"` nel file JSON di binding è necessario presentare questa chiave nella richiesta. Qualsiasi altra chiave provoca un errore di autorizzazione.

> [!IMPORTANT]  
> Date le autorizzazioni elevate concesse dalla chiave master, è consigliabile non condividere questa chiave con terze parti o distribuirla in applicazioni client native. Fare attenzione quando si sceglie il livello di autorizzazione di amministratore.

### <a name="api-key-authorization"></a>Autorizzazione della chiave API

Per impostazione predefinita, un trigger HTTP richiede una chiave API nella richiesta HTTP. La richiesta HTTP è quindi in genere simile alla seguente:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

La chiave può essere inclusa in una variabile della stringa di query denominata `code`, come sopra, oppure in un'intestazione HTTP `x-functions-key`. Il valore della chiave può essere una chiave di funzione definita per la funzione o una chiave host.

È possibile consentire le richieste anonime, che non richiedono chiavi. È anche possibile richiedere l'uso della chiave master. Per modificare il livello di autorizzazione predefinito, usare la proprietà `authLevel` nel file JSON di binding. Per altre informazioni, vedere [Trigger - configurazione](#trigger---configuration)

### <a name="keys-and-webhooks"></a>Chiavi e webhook

L'autorizzazione webhook viene gestita dal componente ricevitore dei webhook, che fa parte del trigger HTTP, e il meccanismo varia in base al tipo di webhook. Ogni meccanismo tuttavia si basa su una chiave. Per impostazione predefinita, viene usata la chiave di funzione denominata "default". Per usare una chiave diversa, configurare il provider di webhook per inviare il nome della chiave con la richiesta in uno dei modi seguenti:

- **Stringa di query**: il provider passa il nome della chiave nel parametro della stringa di query `clientid`, ad esempio `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Intestazione della richiesta**: il provider passa il nome della chiave nell'intestazione `x-functions-clientid`.

## <a name="trigger---hostjson-properties"></a>Trigger - proprietà di host.json

Il file [host.json](functions-host-json.md) contiene le impostazioni che controllano il comportamento del trigger HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Output

Usare l'associazione di output HTTP per rispondere al mittente della richiesta HTTP. Questa associazione richiede un trigger HTTP e consente di personalizzare la risposta associata alla richiesta del trigger. Se non viene specificato un binding di output HTTP, un trigger HTTP restituisce HTTP 200 OK con un corpo vuoto. 

## <a name="output---configuration"></a>Output - configurazione

C# delle librerie di classe, non sono disponibili proprietà di configurazione di associazione output specifici. Per inviare una risposta HTTP, impostare il tipo restituito della funzione su `HttpResponseMessage` o `Task<HttpResponseMessage>`.

Per gli altri linguaggi un binding di output HTTP è definito come un oggetto JSON nella matrice `bindings` di function.json, come illustrato nell'esempio seguente:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json*.

|Proprietà  |DESCRIZIONE  |
|---------|---------|
| **type** |Il valore deve essere impostato su `http`. |
| **direction** | Il valore deve essere impostato su `out`. |
|**nome** | Nome della variabile usato nel codice della funzione per la risposta. |

## <a name="output---usage"></a>Output - uso

È possibile usare il parametro di output per rispondere al chiamante HTTP o webhook. È anche possibile usare modelli di risposta standard del linguaggio. Per altre risposte di esempio, vedere l'[esempio di trigger](#trigger---example) e l'[esempio di webhook](#trigger---webhook-example).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
