<properties
	pageTitle="Associazioni HTTP e webhook in Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come usare trigger e associazioni HTTP e webhookin Funzioni di Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande"/>

# Associazioni HTTP e webhook in Funzioni di Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice per trigger e associazioni HTTP e webhook in Funzioni di Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## function.json per associazioni HTTP e webhook

Il file *function.json* specifica le proprietà relative alla richiesta e alla risposta.

Proprietà per la richiesta HTTP:

- `name`: nome della variabile usato nel codice della funzione per l'oggetto di richiesta o per il corpo della richiesta nel caso di funzioni Node.js.
- `type`: deve essere impostato su *httpTrigger*.
- `direction`: deve essere impostato su *in*.
- `webHookType`: per i trigger webhook, i valori validi sono *github*, *slack* e *genericJson*. Per un trigger HTTP diverso da un webhook, impostare questa proprietà su una stringa vuota. Per altre informazioni sui webhook, vedere la sezione [Trigger webhook](#webhook-triggers) seguente.
- `authLevel`: non si applica a trigger webhook. Impostare su "function" per richiedere la chiave API, su "anonymous" per rimuovere il requisito della chiave API o su "admin" per richiedere la chiave API master. Per altre informazioni, vedere la sezione [Chiavi API](#apikeys) seguente.

Proprietà per la risposta HTTP:

- `name`: nome della variabile usato nel codice della funzione per l'oggetto di risposta.
- `type`: deve essere impostato su *http*.
- `direction`: deve essere impostato su *out*.
 
Esempio di *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## Trigger webhook

Un trigger webhook è un trigger HTTP con le funzionalità seguenti progettate per i webhook:

* Per provider di webhook specifici (attualmente sono supportati GitHub e Slack), il runtime di Funzioni convalida la firma del provider.
* Per le funzioni Node.js, il runtime di Funzioni fornisce il corpo della richiesta invece dell'oggetto richiesta. Non esiste una gestione speciale per le funzioni C#, perché l'utente controlla ciò che viene fornito specificando il tipo di parametro. Se si specifica `HttpRequestMessage`, si ottiene l'oggetto di richiesta. Se si specifica un tipo POCO, il runtime di Funzioni cerca di analizzare un oggetto JSON nel corpo della richiesta per popolare le proprietà dell'oggetto.
* Per attivare una funzione webhook, la richiesta HTTP deve includere una chiave API. Per i trigger HTTP non webhook, questo requisito è facoltativo.

Per informazioni su come configurare un webhook GitHub, vedere [GitHub Developer - Creating WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) (GitHub Developer - Creazione di webhook).

## URL per attivare la funzione

Per attivare una funzione, si invia una richiesta HTTP a un URL, composto dall'URL dell'app per le funzioni e dal nome della funzione:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## Chiavi API

Per impostazione predefinita, una chiave API deve essere inclusa con una richiesta HTTP per attivare una funzione HTTP o webhook. La chiave può essere inclusa in una variabile della stringa di query denominata `code` oppure in un'intestazione HTTP `x-functions-key`. Per le funzioni non webhook, è possibile indicare che una chiave API non è obbligatoria impostando la proprietà `authLevel` su "anonymous" nel file *function.json*.

È possibile trovare i valori della chiave API nella cartella *D:\\home\\data\\Functions\\secrets* nel file system dell'app per le funzioni. La chiave master e la chiave della funzione vengono impostate nel file *host.json*, come illustrato in questo esempio.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

La chiave della funzione di *host.json* può essere usata per attivare qualsiasi funzione, ma non per attivare una funzione disabilitata. La chiave master può essere usata per attivare qualsiasi funzione, anche una che è stata disabilitata. È possibile configurare una funzione per richiedere la chiave master impostando la proprietà `authLevel` su "admin".

Se la cartella *secrets* contiene un file JSON con lo stesso nome della funzione, la proprietà `key` in tale file può essere usata anche per attivare la funzione e questa chiave funzionerà solo con la funzione a cui si riferisce. Ad esempio, la chiave API per una funzione denominata `HttpTrigger` viene specificata in *HttpTrigger.json* nella cartella *secrets*. Di seguito è fornito un esempio:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Quando si configura un trigger webhook, non condividere la chiave master con il provider webhook. Usare una chiave che funzioni solo con la funzione che elabora il webhook. La chiave master può essere usata per attivare qualsiasi funzione, anche quelle disabilitate.

## Codice C# di esempio per una funzione trigger HTTP 

Il codice di esempio cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

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

## Codice Node.js di esempio per una funzione trigger HTTP 

Questo codice di esempio cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

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

## Codice C# di esempio per una funzione webhook di GitHub 

Questo codice di esempio registra i commenti sui problemi di GitHub.

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

## Codice Node.js di esempio per una funzione webhook di GitHub 

Questo codice di esempio registra i commenti sui problemi di GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Passaggi successivi

[AZURE.INCLUDE [Passaggi successivi](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->