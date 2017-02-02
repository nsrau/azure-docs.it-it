---
title: Associazioni HTTP e webhook in Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare trigger e associazioni HTTP e webhookin Funzioni di Azure.
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: funzioni di Azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server, HTTP, API, REST
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 29df0e2198be05a64b6a90f1adf30a0c3b218d93
ms.openlocfilehash: b40fa2d511910668438ba33291d16202dec8c8a8


---
# <a name="azure-functions-http-and-webhook-bindings"></a>Associazioni HTTP e webhook in Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e usare trigger e associazioni HTTP in Funzioni di Azure.
In questo modo è possibile usare Funzioni di Azure per compilare API senza server e rispondere ai webhook.

Funzioni di Azure fornisce le associazioni seguenti:
- Un [trigger HTTP](#httptrigger) consente di richiamare una funzione con una richiesta HTTP e può essere personalizzato per rispondere ai [webhook](#hooktrigger).
- Un'[associazione di output HTTP](#output) consente di rispondere alla richiesta.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>Trigger HTTP
Il trigger HTTP eseguirà la funzione in risposta a una richiesta HTTP. È possibile personalizzarlo per rispondere a un particolare URL o set di metodi HTTP. Un trigger HTTP può essere configurato anche per rispondere ai webhook. 

Se si usa il portale di Funzioni, è anche possibile iniziare subito a usare un modello predefinito. Selezionare **Nuova funzione** e scegliere "API e webhook" dal menu a discesa **Scenario**. Selezionare uno dei modelli e fare clic su **Crea**.

Per impostazione predefinita, un trigger HTTP risponderà alla richiesta con un codice di stato HTTP 200 OK e un corpo vuoto. Per modificare la risposta, configurare un'[associazione di output HTTP](#output)

### <a name="configuring-an-http-trigger"></a>Configurazione di un trigger HTTP
Un trigger HTTP viene definito includendo un oggetto JSON simile al seguente nella matrice `bindings` di function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "GET" ],
    "route": "values/{id}"
},
```
L'associazione supporta le proprietà seguenti:

* **name**: obbligatoria. Nome della variabile usato nel codice della funzione per la richiesta o il corpo della richiesta. Vedere [Uso di un trigger HTTP dal codice](#httptriggerusage).
* **type**: obbligatoria. Deve essere impostata su "httpTrigger".
* **direction**: obbligatoria. Deve essere impostata su "in".
* _authLevel_: determina le eventuali chiavi che devono essere presenti nella richiesta per richiamare la funzione. Vedere [Uso delle chiavi](#keys) più avanti. Il valore può essere uno dei seguenti:
    * _anonymous_: nessuna chiave API obbligatoria.
    * _function_: è obbligatoria una chiave API specifica della funzione. Questo è il valore predefinito se non ne viene specificato nessuno.
    * _admin_: la chiave master è obbligatoria.
* **methods**: matrice dei metodi HTTP a cui la funzione risponderà. Se non viene specificata, la funzione risponderà a tutti i metodi HTTP. Vedere [Personalizzazione dell'endpoint HTTP](#url).
* **route**: definisce il modello di route, controllando a quali URL delle richieste la funzione risponderà. Il valore predefinito, se non ne viene specificato nessuno, è `<functionname>`. Vedere [Personalizzazione dell'endpoint HTTP](#url).
* **webHookType**: configura il trigger HTTP perché funga da ricevitore webhook per il provider specificato. Se viene scelto questo valore, la proprietà _methods_ non deve essere impostata. Vedere [Risposta ai webhook](#hooktrigger). Il valore può essere uno dei seguenti:
    * _genericJson_: endpoint di webhook per utilizzo generico senza logica per un provider specifico.
    * _github_: la funzione risponderà ai webhook GitHub. Se viene scelto questo valore, la proprietà _authLevel_ non deve essere impostata.
    * _slack_: la funzione risponderà ai webhook Slack. Se viene scelto questo valore, la proprietà _authLevel_ non deve essere impostata.

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Uso di un trigger HTTP dal codice
Per le funzioni C# e F#, è possibile dichiarare `HttpRequestMessage` o un tipo personalizzato come tipo dell'input del trigger. Se si sceglie `HttpRequestMessage`, si otterrà l'accesso completo all'oggetto richiesta. Per un tipo personalizzato (ad esempio, POCO), Funzioni cercherà di analizzare il corpo della richiesta come JSON per popolare le proprietà dell'oggetto.

Per le funzioni Node.js, il runtime di Funzioni fornisce il corpo della richiesta invece dell'oggetto richiesta.

Vedere [Esempi di trigger HTTP](#httptriggersample) per gli utilizzi di esempio.


<a name="output"></a>
## <a name="http-response-output-binding"></a>Associazione di output della risposta HTTP
Usare l'associazione di output HTTP per rispondere al mittente della richiesta HTTP. Questa associazione richiede un trigger HTTP e consente di personalizzare la risposta associata alla richiesta del trigger. Se non viene specificata un'associazione di output HTTP, un trigger HTTP restituirà HTTP 200 OK con un corpo vuoto. 

### <a name="configuring-an-http-output-binding"></a>Configurazione di un'associazione di output HTTP
L'associazione di output HTTP viene definita includendo un oggetto JSON simile al seguente nella matrice `bindings` di function.json:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
L'associazione contiene le proprietà seguenti:

* **name**: obbligatoria. Nome della variabile usato nel codice della funzione per la risposta. Vedere [Uso di un'associazione di output HTTP dal codice](#outputusage).
* **type**: obbligatoria. Deve essere impostata su "http".
* **direction**: obbligatoria. Deve essere impostata su "out".

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Uso di un'associazione di output HTTP dal codice
È possibile usare il parametro di output (ad esempio, "res") per rispondere al chiamante HTTP o webhook. In alternativa, è possibile usare il modello standard `Request.CreateResponse()` (C#) o `context.res` (Node.JS) per restituire la risposta. Per esempi di come usare il secondo metodo, vedere [Esempi di trigger HTTP](#httptriggersample) ed [Esempi di trigger webhook](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Risposta ai webhook
Un trigger HTTP con la proprietà _webHookType_ verrà configurato per rispondere ai [webhook](https://en.wikipedia.org/wiki/Webhook). La configurazione di base usa l'impostazione "genericJson", che limita le richieste solo a quelle che usano HTTP POST e con il tipo di contenuto `application/json`.

Il trigger può anche essere personalizzato per un provider di webhook specifico (ad esempio, [GitHub](https://developer.github.com/webhooks/) e [Slack](https://api.slack.com/outgoing-webhooks)). Se viene specificato un provider, il runtime di Funzioni può eseguire automaticamente la logica di convalida del provider.  

### <a name="configuring-github-as-a-webhook-provider"></a>Configurazione di Github come provider di webhook
Per rispondere ai webhook GitHub, creare prima di tutto la funzione con un trigger HTTP e impostare la proprietà _webHookType_ su "github". Copiare quindi l'[URL](#url) e la [chiave API](#keys) nella pagina **Add webhook** (Aggiungi webhook) del repository GitHub. Per altre informazioni, vedere la documentazione [Creating Webhooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) (Creazione di webhook) di GitHub.

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

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

In questo modo il codice della funzione può supportare due parametri nell'indirizzo: "category" e "id". I parametri sono compatibili con qualsiasi [vincolo di route dell'API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints). Il codice di funzione C# seguente usa entrambi i parametri.

```csharp
    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
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

Per informazioni su altre proprietà che è possibile configurare nel file *host.json*, vedere il [riferimento su host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).


<a name="keys"></a>
## <a name="working-with-keys"></a>Uso delle chiavi
Gli elementi HttpTrigger possono sfruttare le chiavi per una maggiore sicurezza. Un elemento HttpTrigger standard può usarle come chiave API, imponendo la presenza della chiave nella richiesta. I webhook possono usare le chiavi per autorizzare le richieste in svariati modi, a seconda di ciò che il provider supporta.

Le chiavi vengono archiviate come parte dell'app per le funzioni in Azure e crittografate inattive. Per visualizzare le chiavi, crearne di nuove o aggiornare le chiavi con nuovi valori, passare a una delle funzioni nel portale e selezionare "Gestisci". 

Esistono due tipi di chiavi:
- **Chiavi host**: queste chiavi vengono condivise da tutte le funzioni nell'app per le funzioni. Quando vengono usate come chiave API, consentono l'accesso a tutte le funzioni nell'app per le funzioni.
- **Chiavi di funzione**: queste chiavi si applicano solo alle funzioni specifiche sotto le quali vengono definite. Quando vengono usate come chiave API, consentono l'accesso solo a tale funzione.

Ogni chiave viene denominata per riferimento ed esiste una chiave predefinita (denominata "default") a livello di funzione e di host. La **chiave master** è una chiave host predefinita denominata "_master" che viene definita per ogni app per le funzioni e non può essere revocata. Fornisce l'accesso amministrativo alle API di runtime. Per usare `"authLevel": "admin"` nel file JSON di associazione, nella richiesta dovrà essere presentata questa chiave. Qualsiasi altra chiave restituirà un errore di autorizzazione.

> [!NOTE]
> Date le autorizzazioni elevate concesse dalla chiave master, è consigliabile non condividere questa chiave con terze parti o distribuirla in applicazioni client native. Prestare attenzione quando si sceglie il livello di autorizzazione di amministratore.
> 
> 

### <a name="api-key-authorization"></a>Autorizzazione della chiave API
Per impostazione predefinita, un elemento HttpTrigger richiede una chiave API nella richiesta HTTP. La richiesta HTTP in genere è quindi simile alla seguente:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

La chiave può essere inclusa in una variabile della stringa di query denominata `code`, come sopra, oppure in un'intestazione HTTP `x-functions-key`. Il valore della chiave può essere una chiave di funzione definita per la funzione o una chiave host.

È possibile scegliere di consentire le richieste senza chiavi o specificare che deve essere usata la chiave master modificando la proprietà `authLevel` nel file JSON dell'associazione. Vedere [Trigger HTTP](#httptrigger).

### <a name="keys-and-webhooks"></a>Chiavi e webhook
L'autorizzazione webhook viene gestita dal componente ricevitore dei webhook, che fa parte di HttpTrigger, e il meccanismo varia in base al tipo di webhook. Ogni meccanismo tuttavia si basa su una chiave. Per impostazione predefinita, verrà usata la chiave di funzione denominata "default". Per usare un'altra chiave, sarà necessario configurare il provider di webhook per inviare il nome della chiave con la richiesta in uno dei modi seguenti:

- **Stringa di query**: il provider passa il nome della chiave nel parametro della stringa di query `clientid` (ad esempio, `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`).
- **Intestazione della richiesta**: il provider passa il nome della chiave nell'intestazione `x-functions-clientid`.

> [!NOTE]
> Le chiavi di funzione hanno la precedenza sulle chiavi host. Se due chiavi sono definite con lo stesso nome, verrà usata la chiave di funzione.
> 
> 


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
* [Node.js](#httptriggernodejs)


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

È necessario un file `project.json` che usa NuGet per fare riferimento agli assembly `FSharp.Interop.Dynamic` e `Dynamitey` come segue:

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

Verrà usato NuGet per recuperare le dipendenze e verrà creato un riferimento alle dipendenze nello script.

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




<!--HONumber=Dec16_HO3-->


