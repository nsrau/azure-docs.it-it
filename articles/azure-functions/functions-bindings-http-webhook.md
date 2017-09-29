---
title: Associazioni HTTP e webhook in Funzioni di Azure | Microsoft Docs
description: Informazioni su come usare trigger e associazioni HTTP e webhookin Funzioni di Azure.
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: funzioni di Azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server, HTTP, API, REST
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: mahender
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 3c3247592cbe2bc382d220264b0c646ee566b8a7
ms.contentlocale: it-it
ms.lasthandoff: 09/29/2017

---
# <a name="azure-functions-http-and-webhook-bindings"></a>Associazioni HTTP e webhook in Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e usare trigger e associazioni HTTP in Funzioni di Azure.
In questo modo è possibile usare Funzioni di Azure per compilare API senza server e rispondere ai webhook.

Funzioni di Azure fornisce le associazioni seguenti:
- Un [trigger HTTP](#httptrigger) consente di richiamare una funzione con una richiesta HTTP e può essere personalizzato per rispondere ai [webhook](#hooktrigger).
- Un'[associazione di output HTTP](#output) consente di rispondere alla richiesta.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>Trigger HTTP
Il trigger HTTP esegue la funzione in risposta a una richiesta HTTP. È possibile personalizzarlo per rispondere a un particolare URL o set di metodi HTTP. Un trigger HTTP può essere configurato anche per rispondere ai webhook. 

Se si usa il portale di Funzioni, è anche possibile iniziare subito a usare un modello predefinito. Selezionare **Nuova funzione** e scegliere "API e webhook" dal menu a discesa **Scenario**. Selezionare uno dei modelli e fare clic su **Crea**.

Per impostazione predefinita, un trigger HTTP risponde alla richiesta con un codice di stato HTTP 200 OK e un corpo vuoto. Per modificare la risposta, configurare un'[associazione di output HTTP](#output)

### <a name="configuring-an-http-trigger"></a>Configurazione di un trigger HTTP
Un trigger HTTP è definito da un oggetto JSON nella matrice `bindings` di function.json, come illustrato nell'esempio seguente:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
L'associazione supporta le proprietà seguenti:

|Proprietà  |Descrizione  |
|---------|---------|
| **nome** | Obbligatoria. Nome della variabile usato nel codice della funzione per la richiesta o il corpo della richiesta. Vedere [Uso di un trigger HTTP dal codice](#httptriggerusage). |
| **type** | Obbligatoria. Deve essere impostata su `httpTrigger`. |
| **direction** | Obbligatoria. Deve essere impostata su `in`. |
| **authLevel** | Determina le eventuali chiavi che devono essere presenti nella richiesta per richiamare la funzione. Il valore può essere uno dei seguenti: <ul><li><code>anonymous</code>&mdash;Non è richiesta nessuna chiave API.</li><li><code>function</code>&mdash;È richiesta una chiave API specifica della funzione. Questo è il valore predefinito se non ne viene specificato nessuno.</li><li><code>admin</code>&mdash;È richiesta la chiave master.</li></ul> Per altre informazioni, vedere [Uso delle chiavi](#keys). |
| **methods** | Matrice di metodi HTTP a cui la funzione risponde. Se non viene specificata, la funzione risponde a tutti i metodi HTTP. Vedere [Personalizzazione dell'endpoint HTTP](#url). |
| **route** | Definisce il modello di route, controllando a quali URL di richiesta risponde la funzione. Il valore predefinito, se non ne viene specificato nessuno, è `<functionname>`. Per altre informazioni, vedere [Personalizzazione dell'endpoint HTTP](#url). |
| **webHookType** | Configura il trigger HTTP perché funga da ricevitore webhook per il provider specificato. Non usare la proprietà _methods_ quando si usa questa impostazione. Il valore può essere uno dei seguenti:<ul><li><code>genericJson</code>&mdash;Endpoint di webhook per uso generico senza logica per un provider specifico.</li><li><code>github</code>&mdash;La funzione risponde ai webhook GitHub. Non usare la proprietà _authLevel_ quando si usa questo valore.</li><li><code>slack</code>&mdash;La funzione risponde ai webhook Slack. Non usare la proprietà _authLevel_ quando si usa questo valore.</li></ul> Per altre informazioni, vedere [Risposta ai webhook](#hooktrigger). |

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Uso di un trigger HTTP dal codice
Per le funzioni C# e F#, è possibile dichiarare il tipo di input del trigger come `HttpRequestMessage` o un tipo .NET personalizzato. Se si sceglie `HttpRequestMessage`, si ottiene accesso completo all'oggetto richiesta. Per un tipo .NET personalizzato, Funzioni cerca di analizzare il corpo della richiesta JSON per impostare le proprietà dell'oggetto. 

Per le funzioni Node.js, il runtime di Funzioni fornisce il corpo della richiesta invece dell'oggetto richiesta. Per altre informazioni, vedere [Esempi di trigger HTTP](#httptriggersample).


<a name="output"></a>
## <a name="http-response-output-binding"></a>Associazione di output della risposta HTTP
Usare l'associazione di output HTTP per rispondere al mittente della richiesta HTTP. Questa associazione richiede un trigger HTTP e consente di personalizzare la risposta associata alla richiesta del trigger. Se non viene specificato un binding di output HTTP, un trigger HTTP restituisce HTTP 200 OK con un corpo vuoto. 

### <a name="configuring-an-http-output-binding"></a>Configurazione di un'associazione di output HTTP
Un binding di output HTTP è definito da un oggetto JSON nella matrice `bindings` di function.json, come illustrato nell'esempio seguente:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
Il binding supporta le proprietà obbligatorie seguenti:

|Proprietà  |Descrizione  |
|---------|---------|
|**nome** | Nome della variabile usato nel codice della funzione per la risposta. Vedere [Uso di un'associazione di output HTTP dal codice](#outputusage). |
| **type** |Il valore deve essere impostato su `http`. |
| **direction** | Il valore deve essere impostato su `out`. |

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Uso di un'associazione di output HTTP dal codice
È possibile usare il parametro di output per rispondere al chiamante HTTP o webhook. È anche possibile usare modelli di risposta standard del linguaggio. Per esempi di risposte, vedere [Esempi di trigger HTTP](#httptriggersample) ed [Esempi di webhook](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Risposta ai webhook
Un trigger HTTP con la proprietà _webHookType_ è configurato per rispondere ai [webhook](https://en.wikipedia.org/wiki/Webhook). La configurazione di base usa l'impostazione "genericJson", che limita le richieste solo a quelle che usano HTTP POST e con il tipo di contenuto `application/json`.

Il trigger può anche essere personalizzato per un provider di webhook specifico, ad esempio, [GitHub](https://developer.github.com/webhooks/) o [Slack](https://api.slack.com/outgoing-webhooks). Se viene specificato un provider, il runtime di Funzioni può gestire automaticamente la logica di convalida del provider.  

### <a name="configuring-github-as-a-webhook-provider"></a>Configurazione di GitHub come provider di webhook
Per rispondere ai webhook GitHub, creare prima di tutto la funzione con un trigger HTTP e impostare la proprietà **webHookType** su `github`. Copiare quindi l'[URL](#url) e la [chiave API](#keys) nella pagina **Aggiungi webhook** del repository GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Per un esempio, vedere [Creare una funzione attivata da un webhook GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="configuring-slack-as-a-webhook-provider"></a>Configurazione di Slack come provider di webhook
Il webhook Slack genera automaticamente un token invece di consentire di specificarlo, quindi è necessario configurare una chiave specifica della funzione con il token da Slack. Vedere [Uso delle chiavi](#keys).

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>Personalizzazione dell'endpoint HTTP
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

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

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

Di seguito è mostrato il codice di funzione Node.js per usare gli stessi parametri di route.

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

Per impostazione predefinita, tutte le route di funzione sono precedute da *api*. È inoltre possibile personalizzare o rimuovere il prefisso con la proprietà `http.routePrefix` nel file *host.json*. Nell'esempio seguente viene rimosso il prefisso della route *api* usando una stringa vuota per il prefisso nel file *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

Per informazioni dettagliate su come aggiornare il file *host.json* per la funzione, vedere [Come aggiornare i file nelle app per le funzioni](functions-reference.md#fileupdate). 

Per informazioni su altre proprietà che è possibile configurare nel file *host.json*, vedere il [riferimento su host.json](functions-host-json.md).


<a name="keys"></a>
## <a name="working-with-keys"></a>Uso delle chiavi
I trigger HTTP consentono di usare le chiavi per una maggiore sicurezza. Un trigger HTTP standard può usare queste chiavi come chiave API, richiedendone la presenza nella richiesta. I webhook possono usare le chiavi per autorizzare le richieste in svariati modi, a seconda di ciò che il provider supporta.

Le chiavi vengono archiviate come parte dell'app per le funzioni in Azure e crittografate inattive. Per visualizzare le chiavi, crearne di nuove o aggiornare le chiavi con nuovi valori, passare a una delle funzioni nel portale e selezionare "Gestisci". 

Esistono due tipi di chiavi:
- **Chiavi host**: queste chiavi vengono condivise da tutte le funzioni nell'app per le funzioni. Quando vengono usate come chiave API, consentono l'accesso a tutte le funzioni nell'app per le funzioni.
- **Chiavi di funzione**: queste chiavi si applicano solo alle funzioni specifiche sotto le quali vengono definite. Quando vengono usate come chiave API, consentono l'accesso solo a tale funzione.

Ogni chiave viene denominata per riferimento ed esiste una chiave predefinita (denominata "default") a livello di funzione e di host. La **chiave master** è una chiave host predefinita denominata "_master", definita per ogni app per le funzioni. Questa chiave non può essere revocata. Fornisce l'accesso amministrativo alle API di runtime. Per usare `"authLevel": "admin"` nel file JSON di binding è necessario presentare questa chiave nella richiesta. Qualsiasi altra chiave provoca un errore di autorizzazione.

> [!IMPORTANT]  
> Date le autorizzazioni elevate concesse dalla chiave master, è consigliabile non condividere questa chiave con terze parti o distribuirla in applicazioni client native. Fare attenzione quando si sceglie il livello di autorizzazione di amministratore.

### <a name="api-key-authorization"></a>Autorizzazione della chiave API
Per impostazione predefinita, un trigger HTTP richiede una chiave API nella richiesta HTTP. La richiesta HTTP è quindi in genere simile alla seguente:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

La chiave può essere inclusa in una variabile della stringa di query denominata `code`, come sopra, oppure in un'intestazione HTTP `x-functions-key`. Il valore della chiave può essere una chiave di funzione definita per la funzione o una chiave host.

È possibile consentire le richieste anonime, che non richiedono chiavi. È anche possibile richiedere l'uso della chiave master. Per modificare il livello di autorizzazione predefinito, usare la proprietà `authLevel` nel file JSON di binding. Per altre informazioni, vedere [Trigger HTTP](#httptrigger).

### <a name="keys-and-webhooks"></a>Chiavi e webhook
L'autorizzazione webhook viene gestita dal componente ricevitore dei webhook, che fa parte del trigger HTTP, e il meccanismo varia in base al tipo di webhook. Ogni meccanismo tuttavia si basa su una chiave. Per impostazione predefinita, viene usata la chiave di funzione denominata "default". Per usare una chiave diversa, configurare il provider di webhook per inviare il nome della chiave con la richiesta in uno dei modi seguenti:

- **Stringa di query**: il provider passa il nome della chiave nel parametro della stringa di query `clientid`, ad esempio `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Intestazione della richiesta**: il provider passa il nome della chiave nell'intestazione `x-functions-clientid`.

> [!NOTE]
> Le chiavi di funzione hanno la precedenza sulle chiavi host. Se sono definite due chiavi con lo stesso nome, viene usata sempre la chiave di funzione.


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>Esempi di trigger HTTP
Si supponga che il trigger HTTP seguente sia presente nella matrice `bindings` di function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Vedere l'esempio specifico del linguaggio, che cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.JS](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>Esempio di trigger HTTP in C# #
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

È anche possibile eseguire il binding a un oggetto .NET al posto di **HttpRequestMessage**. Questo oggetto viene creato dal corpo della richiesta e analizzato come JSON. Analogamente, un tipo può essere passato al binding di output della risposta HTTP e restituito come corpo della risposta, con un codice di stato 200.

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

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>Esempio di trigger HTTP in F# #
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

È necessario un file `project.json` che usa NuGet per fare riferimento agli assembly `FSharp.Interop.Dynamic` e `Dynamitey`, come illustrato di seguito:

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

Viene usato NuGet per recuperare le dipendenze e farvi riferimento nello script.

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Esempio di trigger HTTP in Node.JS
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
     
<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Esempi di webhook
Si supponga che il trigger webhook seguente sia presente nella matrice `bindings` di function.json:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Vedere l'esempio specifico del linguaggio che registra i commenti del problema GitHub.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.js](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>Esempio di webhook in C# #
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

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>Esempio di webhook in F# #
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

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Esempio di webhook in Node.JS
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


