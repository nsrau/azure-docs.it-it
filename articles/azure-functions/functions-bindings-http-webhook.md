---
title: Trigger e associazioni HTTP di Funzioni di Azure
description: Informazioni su come usare i trigger e le associazioni HTTP in Funzioni di Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: funzioni di Azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server, HTTP, API, REST
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 8d2bd74609447463b7ff857aa1037eaf5b6e3abb
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727004"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Trigger e associazioni HTTP di Funzioni di Azure

Questo articolo illustra come usare trigger e associazioni di output HTTP in Funzioni di Azure.

Un trigger HTTP può essere personalizzato per rispondere ai [webhook](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Le associazioni HTTP sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) versione 1.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

Le associazioni HTTP sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/).

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Un trigger HTTP consente di richiamare una funzione con una richiesta HTTP e può essere usato per compilare API senza server e rispondere ai webhook.

Per impostazione predefinita, un trigger HTTP restituisce HTTP 200 OK con un corpo vuoto nelle funzioni 1.x o HTTP 204 Nessun contenuto con un corpo vuoto nelle funzioni 2.x. Per modificare la risposta, configurare un'[associazione di output HTTP](#output).

## <a name="trigger---example"></a>Trigger - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#trigger---c-example)
* [Script C# (file con estensione csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Trigger - esempio in C#

L'esempio seguente mostra una [funzione in C#](functions-dotnet-class-library.md) che cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP. Si noti che il valore restituito viene usato per l'associazione di output, ma non è necessario un attributo di valore restituito.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

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

Ecco il codice script C# associato a un oggetto `HttpRequestMessage`:

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
{
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

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
using Microsoft.Extensions.Logging;

public static string Run(CustomObject req, ILogger log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public string name {get; set;}
}
```

### <a name="trigger---f-example"></a>Trigger - Esempio in F#

L'esempio seguente mostra un'associazione di trigger in un file *function.json* e una [funzione F#](functions-reference-fsharp.md) che usa l'associazione. La funzione cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
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

### <a name="trigger---python-example"></a>Trigger - Esempio di Python

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

### <a name="trigger---java-example"></a>Trigger - Esempio Java

L'esempio seguente mostra un'associazione di trigger in un file *function.json* e una [funzione Java](functions-reference-java.md) che usa l'associazione. La funzione restituisce una risposta con codice di stato HTTP 200 con corpo della richiesta che prefissa il corpo della richiesta di attivazione del trigger con un messaggio di saluto "Ciao,".


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

Ecco il codice Java:

```java
@FunctionName("hello")
public HttpResponseMessage<String> hello(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS), Optional<String> request,
                        final ExecutionContext context)
    {
        // default HTTP 200 response code
        return String.format("Hello, %s!", request);
    }
}
```

## <a name="trigger---attributes"></a>Trigger - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs).

È possibile impostare il livello di autorizzazione e i metodi HTTP consentiti nei parametri di costruttore dell'attributo. Sono disponibili proprietà per il modello di route e il tipo di webhook. Per altre informazioni su queste impostazioni, vedere [Trigger - configurazione](#trigger---configuration). Di seguito è mostrato un attributo `HttpTrigger` in una firma del metodo:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequestMessage req)
{
    ...
}
 ```

Per un esempio completo, vedere [Trigger - esempio in C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `HttpTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
| **type** | n/d| Obbligatoria. Deve essere impostata su `httpTrigger`. |
| **direction** | n/d| Obbligatoria. Deve essere impostata su `in`. |
| **nome** | n/d| Obbligatoria. Nome della variabile usato nel codice della funzione per la richiesta o il corpo della richiesta. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina le eventuali chiavi che devono essere presenti nella richiesta per richiamare la funzione. Il livello di autorizzazione può corrispondere a uno dei valori seguenti: <ul><li><code>anonymous</code>&mdash;Non è richiesta nessuna chiave API.</li><li><code>function</code>&mdash;È richiesta una chiave API specifica della funzione. Questo è il valore predefinito se non ne viene specificato nessuno.</li><li><code>admin</code>&mdash;È richiesta la chiave master.</li></ul> Per altre informazioni, consultare la sezione sulle [chiavi di autorizzazione](#authorization-keys). |
| **methods** |**Metodi** | Matrice di metodi HTTP a cui la funzione risponde. Se non viene specificata, la funzione risponde a tutti i metodi HTTP. Vedere [Personalizzare l'endpoint HTTP](#customize-the-http-endpoint). |
| **route** | **Route** | Definisce il modello di route, controllando a quali URL di richiesta risponde la funzione. Il valore predefinito, se non ne viene specificato nessuno, è `<functionname>`. Per altre informazioni, vedere [Personalizza l'endpoint HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Supportato solo per la versione di runtime 1.x._<br/><br/>Configura il trigger HTTP perché funga da ricevitore [webhook](https://en.wikipedia.org/wiki/Webhook) per il provider specificato. Non impostare la proprietà `methods` se si imposta questa proprietà. Il tipo di webhook può essere uno dei seguenti:<ul><li><code>genericJson</code>&mdash;Endpoint di webhook per uso generico senza logica per un provider specifico. Questa impostazione limita le richieste solo a quelle che usano HTTP POST e con il tipo di contenuto `application/json`.</li><li><code>github</code>&mdash;La funzione risponde ai [webhook GitHub](https://developer.github.com/webhooks/). Non usare la proprietà _authLevel_ con webhook GitHub. Per altre informazioni, vedere la sezione con relativa ai webhook GitHub più avanti in questo articolo.</li><li><code>slack</code>&mdash;La funzione risponde ai [webhook Slack](https://api.slack.com/outgoing-webhooks). Non usare la proprietà _authLevel_ con webhook Slack. Per altre informazioni, vedere la sezione con relativa ai webhook Slack più avanti in questo articolo.</li></ul>|

## <a name="trigger---usage"></a>Trigger - uso

Per le funzioni C# e F#, è possibile dichiarare `HttpRequestMessage` o un tipo personalizzato come tipo dell'input del trigger. Se si sceglie `HttpRequestMessage`, si ottiene accesso completo all'oggetto richiesta. Per un tipo personalizzato, il runtime cerca di analizzare il corpo della richiesta JSON per impostare le proprietà dell'oggetto.

Per le funzioni JavaScript, il runtime di Funzioni fornisce il corpo della richiesta invece dell'oggetto richiesta. Per altre informazioni, vedere l'[esempio di trigger JavaScript](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Personalizzare l'endpoint HTTP

Per impostazione predefinita, quando si crea una funzione per un trigger HTTP la funzione può essere indirizzata con una route nel formato seguente:

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
                                                ILogger log)
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
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
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

### <a name="working-with-client-identities"></a>Utilizzo delle identità client

Se l'app per le funzioni usa l'[autenticazione/autorizzazione di Servizio app ](../app-service/overview-authentication-authorization.md), è possibile visualizzare informazioni sui client autenticati dal codice. Queste informazioni sono disponibili come [intestazioni delle richieste inserite dalla piattaforma](../app-service/app-service-authentication-how-to.md#access-user-claims). 

È anche possibile leggere queste informazioni dai dati di binding. Questa funzionalità è disponibile solo per il runtime di Funzioni 2.x. È anche al momento disponibile solo per i linguaggi .NET.

Nei linguaggi .NET queste informazioni sono disponibili come [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal?view=netstandard-2.0). ClaimsPrincipal è disponibile come parte del contesto della richiesta, come illustrato nell'esempio seguente:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkResult();
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

### <a name="authorization-keys"></a>Chiavi di autorizzazione

Funzioni consente di usare in fase di sviluppo le chiavi che rendono più difficile accedere agli endpoint di funzione HTTP.  Un trigger HTTP standard può richiedere nella richiesta sia presente una chiave API di questo tipo. 

> [!IMPORTANT]
> Mentre le chiavi possono risultare per offuscare gli endpoint HTTP durante lo sviluppo, non sono progettate come un modo per proteggere un trigger HTTP nell'ambiente di produzione. Per altre informazioni, vedere [Proteggere un endpoint HTTP nell'ambiente di produzione](#secure-an-http-endpoint-in-production).

> [!NOTE]
> Nel runtime 1.x di Funzioni i provider di webhook possono usare le chiavi per autorizzare le richieste in svariati modi, a seconda di ciò che il provider supporta. Questa procedura è illustrata in [Webhook e chiavi](#webhooks-and-keys). La versione 2.x del runtime non include supporto incorporato per i provider di webhook.

Esistono due tipi di chiavi:

* **Chiavi host**: queste chiavi vengono condivise da tutte le funzioni nell'app per le funzioni. Quando vengono usate come chiave API, consentono l'accesso a tutte le funzioni nell'app per le funzioni.
* **Chiavi di funzione**: queste chiavi si applicano solo alle funzioni specifiche per le quali vengono definite. Quando vengono usate come chiave API, consentono l'accesso solo a tale funzione.

Ogni chiave viene denominata per riferimento ed esiste una chiave predefinita (denominata "default") a livello di funzione e di host. Le chiavi di funzione hanno la precedenza sulle chiavi host. Se sono definite due chiavi con lo stesso nome, viene usata sempre la chiave di funzione.

Ogni app per le funzioni ha anche una speciale **chiave master**. Questa chiave è una chiave host denominata `_master`, che fornisce l'accesso amministrativo alle API di runtime. Questa chiave non può essere revocata. Quando si imposta un livello di autorizzazione `admin`, le richieste devono usare la chiave master. Qualsiasi altra chiave provoca un errore di autorizzazione.

> [!CAUTION]  
> Date le autorizzazioni elevate concesse dalla chiave master nell'app per le funzioni, è consigliabile non condividere questa chiave con terze parti o distribuirla in applicazioni client native. Fare attenzione quando si sceglie il livello di autorizzazione di amministratore.

### <a name="obtaining-keys"></a>Ottenere le chiavi

Le chiavi vengono archiviate come parte dell'app per le funzioni in Azure e crittografate inattive. Per visualizzare le chiavi, crearne di nuove o aggiornare le chiavi con nuovi valori, passare a una delle funzioni attivate da HTTP nel [portale di Azure](https://portal.azure.com) e selezionare **Gestisci**.

![Gestire le chiavi di funzione nel portale.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Non è disponibile alcuna API supportata per l'acquisizione a livello di codice di tasti funzione.

### <a name="api-key-authorization"></a>Autorizzazione della chiave API

La maggior parte dei modelli di trigger HTTP richiedono una chiave API nella richiesta. La richiesta HTTP è quindi in genere simile al seguente URL:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

La chiave può essere inclusa in una variabile della stringa di query denominata `code`, come sopra. Può anche essere inclusa in un'intestazione HTTP `x-functions-key`. Il valore della chiave può essere una chiave di funzione definita per la funzione o una chiave host.

È possibile consentire le richieste anonime, che non richiedono chiavi. È anche possibile richiedere l'uso della chiave master. Per modificare il livello di autorizzazione predefinito, usare la proprietà `authLevel` nel file JSON di binding. Per altre informazioni, vedere [Trigger - configurazione](#trigger---configuration)

> [!NOTE]
> Quando si eseguono funzioni localmente, l'autorizzazione viene disabilitata indipendentemente dall'impostazione del livello di autenticazione specificata. Dopo la pubblicazione in Azure, viene applicata l'impostazione `authLevel` del trigger.



### <a name="secure-an-http-endpoint-in-production"></a>Proteggere un endpoint HTTP nell'ambiente di produzione

Per proteggere completamente gli endpoint di funzione nell'ambiente di produzione, è consigliabile implementare una delle opzioni di sicurezza a livello di app per le funzioni seguenti:

* Attivare l'autenticazione/autorizzazione del servizio app per l'app per le funzioni. La piattaforma del servizio app consente di usare Azure Active Directory (AAD) e diversi provider di identità di terze parti per autenticare i client. È possibile usarla per implementare regole di autorizzazione personalizzate per le funzioni ed è possibile lavorare con le informazioni utente dal codice di funzione. Per altre informazioni, vedere [Autenticazione e autorizzazione in Servizio app di Azure](../app-service/overview-authentication-authorization.md) e [Utilizzo delle identità client](#working-with-client-identities).

* Usare Gestione API di Azure (APIM) per autenticare le richieste. APIM offre un'ampia gamma di opzioni di sicurezza di API per le richieste in ingresso. Per altre informazioni, vedere [Criteri di autenticazione di Gestione API di Azure](../api-management/api-management-authentication-policies.md). Con APIM, è possibile configurare l'app per le funzioni in modo che accetti le richieste solo dall'indirizzo IP dell'istanza APIM. Per altre informazioni, vedere [Restrizioni degli indirizzi IP](ip-addresses.md#ip-address-restrictions).

* Distribuire l'app per le funzioni in un ambiente di servizio app di Azure (ASE). L'ASE fornisce un ambiente di hosting dedicato in cui eseguire le funzioni. L'ASE consente di configurare un singolo gateway front-end che è possibile usare per autenticare tutte le richieste in ingresso. Per altre informazioni, vedere [Configurazione di un web application firewall (WAF) per l'ambiente del servizio app](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Quando si usa uno di questi metodi di sicurezza a livello di app per le funzioni, è necessario impostare il livello di autenticazione delle funzioni attivate da HTTP su `anonymous`.

### <a name="webhooks"></a>Webhook

> [!NOTE]
> La modalità webhook è disponibile solo per il runtime di funzioni versione 1.x. Questa modifica è stata apportata per migliorare le prestazioni dei trigger HTTP nella versione 2.x.

Nella versione 1.x, i modelli webhook forniscono una convalida aggiuntiva per i payload di webhook. Nella versione 2.x il trigger HTTP di base è ancora funzionante ed è l'approccio consigliato per i webhook. 

#### <a name="github-webhooks"></a>Webhook GitHub

Per rispondere ai webhook GitHub, creare prima di tutto la funzione con un trigger HTTP e impostare la proprietà **webHookType** su `github`. Copiare quindi l'URL e la chiave API nella pagina **Aggiungi webhook** del repository GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Webhook Slack

Il webhook Slack genera automaticamente un token invece di consentire di specificarlo, quindi è necessario configurare una chiave specifica della funzione con il token da Slack. Vedere [Chiavi di autorizzazione](#authorization-keys).

### <a name="webhooks-and-keys"></a>Chiavi e webhook

L'autorizzazione webhook viene gestita dal componente ricevitore dei webhook, che fa parte del trigger HTTP, e il meccanismo varia in base al tipo di webhook. Ogni meccanismo si basa su una chiave. Per impostazione predefinita, viene usata la chiave di funzione denominata "default". Per usare una chiave diversa, configurare il provider di webhook per inviare il nome della chiave con la richiesta in uno dei modi seguenti:

* **Stringa di query**: il provider passa il nome della chiave nel parametro della stringa di query `clientid`, ad esempio `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
* **Intestazione della richiesta**: il provider passa il nome della chiave nell'intestazione `x-functions-clientid`.

## <a name="trigger---limits"></a>Trigger - Limiti

La lunghezza della richiesta HTTP è limitata a 100 MB (104.857.600 byte) e la lunghezza dell'URL è limitata a 4 KB (4096 byte). Questi limiti vengono specificati dall'elemento `httpRuntime` del [file web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config) del runtime.

Se una funzione che usa il trigger HTTP non viene completata entro 2,5 minuti circa, il gateway raggiungerà il timeout e restituirà un errore HTTP 502. La funzione rimarrà in esecuzione, ma non riuscirà a restituire una risposta HTTP. Per le funzioni con esecuzione prolungata, è consigliabile seguire modelli asincroni e restituire una posizione in cui è possibile effettuare il ping dello stato della richiesta. Per informazioni su quanto tempo può durare l'esecuzione di una funzione, vedere [Scalabilità e hosting - Piano a consumo](functions-scale.md#consumption-plan).

## <a name="trigger---hostjson-properties"></a>Trigger - proprietà di host.json

Il file [host.json](functions-host-json.md) contiene le impostazioni che controllano il comportamento del trigger HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Output

Usare l'associazione di output HTTP per rispondere al mittente della richiesta HTTP. Questa associazione richiede un trigger HTTP e consente di personalizzare la risposta associata alla richiesta del trigger. Se non viene fornita un'associazione di output HTTP, un trigger HTTP restituisce HTTP 200 OK con un corpo vuoto nelle funzioni 1.x o HTTP 204 Nessun contenuto con un corpo vuoto nelle funzioni 2.x.

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json*. Per le librerie di classe C# non vi sono proprietà di attributo che corrispondano a queste proprietà *function.json*.

|Proprietà  |DESCRIZIONE  |
|---------|---------|
| **type** |Il valore deve essere impostato su `http`. |
| **direction** | Il valore deve essere impostato su `out`. |
|**nome** | Nome della variabile usato nel codice della funzione per la risposta, o `$return`per usare il valore restituito. |

## <a name="output---usage"></a>Output - uso

Per inviare una risposta HTTP, usare modelli di risposta standard del linguaggio. In C# o nello script C#, eseguire il tipo restituito della funzione `HttpResponseMessage` o `Task<HttpResponseMessage>`. In C#, non è necessario un attributo del valore restituito.

Per le risposte di esempio, vedere l'[esempio di trigger](#trigger---example).

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
