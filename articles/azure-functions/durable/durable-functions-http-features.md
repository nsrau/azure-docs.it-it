---
title: HTTP features in Durable Functions - Azure Functions
description: Informazioni sulle funzionalità HTTP integrate nell'estensione Funzioni permanenti per Funzioni di Azure.Learn about the integrated HTTP features in the Durable Functions extension for Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 29d837446960b7535b26284efdfab7a1c59ea968
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132505"
---
# <a name="http-features"></a>Funzionalità HTTP

Funzioni durevoli dispone di diverse funzionalità che semplificano l'incorporazione di orchestrazioni ed entità durevoli nei flussi di lavoro HTTP. Questo articolo illustra in dettaglio alcune di queste funzionalità.

## <a name="exposing-http-apis"></a>Esposizione di API HTTP

Le orchestrazioni e le entità possono essere richiamate e gestite tramite richieste HTTP. L'estensione Funzioni permanenti espone le API HTTP predefinite. Fornisce inoltre API per l'interazione con orchestrazioni ed entità dall'interno di funzioni attivate da HTTP.

### <a name="built-in-http-apis"></a>API HTTP predefinite

L'estensione Funzioni permanenti aggiunge automaticamente un set di API HTTP all'host Funzioni di Azure.The Durable Functions extension automatically adds a set of HTTP APIs to the Azure Functions host. Con queste API, è possibile interagire e gestire le orchestrazioni e le entità senza scrivere codice.

Sono supportate le API HTTP predefinite seguenti.

* [Avviare una nuova orchestrazione](durable-functions-http-api.md#start-orchestration)
* [Istanza dell'orchestrazione di query](durable-functions-http-api.md#get-instance-status)
* [Terminare l'istanza dell'orchestrazione](durable-functions-http-api.md#terminate-instance)
* [Inviare un evento esterno a un'orchestrazioneSend an external event to an orchestration](durable-functions-http-api.md#raise-event)
* [Cronologia dell'orchestrazione di eliminazionePurge orchestration history](durable-functions-http-api.md#purge-single-instance-history)
* [Inviare un evento operazione a un'entitàSend an operation event to an entity](durable-functions-http-api.md#signal-entity)
* [Ottenere lo stato di un'entitàGet the state of an entity](durable-functions-http-api.md#get-entity)
* [Eseguire una query sull'elenco di entità](durable-functions-http-api.md#list-entities)

Vedere [l'articolo API HTTP](durable-functions-http-api.md) per una descrizione completa di tutte le API HTTP predefinite esposte dall'estensione Funzioni permanenti.

### <a name="http-api-url-discovery"></a>Rilevamento dell'URL di API HTTP

[L'associazione client](durable-functions-bindings.md#orchestration-client) di orchestrazione espone le API che possono generare utili payload di risposta HTTP. Ad esempio, può creare una risposta contenente collegamenti alle API di gestione per un'istanza di orchestrazione specifica. Gli esempi seguenti illustrano una funzione di trigger HTTP che illustra come usare questa API per una nuova istanza dell'orchestrazione:The following examples show an HTTP-trigger function that demonstrates how to use this API for a new orchestration instance:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json (funzione.json)**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

L'avvio di una funzione dell'agente di orchestrazione utilizzando le funzioni di trigger HTTP illustrate in precedenza può essere eseguito utilizzando qualsiasi client HTTP. Il comando cURL seguente avvia una `DoWork`funzione dell'agente di orchestrazione denominata :

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Successivamente è una risposta di esempio `abc123` per un'orchestrazione che ha come ID. Alcuni dettagli sono stati rimossi per chiarezza.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Nell'esempio precedente, ognuno dei `Uri` campi che terminano con corrisponde a un'API HTTP incorporata. È possibile usare queste API per gestire l'istanza dell'orchestrazione di destinazione.

> [!NOTE]
> Il formato degli URL webhook dipende dalla versione dell'host Funzioni di Azure in esecuzione. L'esempio precedente è per l'host di Azure Functions 2.0.The previous example is for the Azure Functions 2.0 host.

Per una descrizione di tutte le API HTTP predefinite, vedere le informazioni [di riferimento sull'API HTTP.](durable-functions-http-api.md)

### <a name="async-operation-tracking"></a>Verifica di operazioni asincrone

La risposta HTTP indicata in precedenza è stata concepita per semplificare l'implementazione di API asincrone a esecuzione prolungata con Durable Functions. Questo modello viene talvolta definito modello di consumer di *polling.* Il flusso client/server funziona come segue:

1. Il client invia una richiesta HTTP per avviare un processo a esecuzione prolungata, ad esempio una funzione dell'agente di orchestrazione.
1. Il trigger HTTP di destinazione restituisce una risposta HTTP 202 con un'intestazione Location con il valore "statusQueryGetUri".
1. Il client esegue il polling dell'URL nell'intestazione Location. Il client continua a visualizzare le risposte HTTP 202 con un'intestazione Location.
1. Quando l'istanza termina o ha esito negativo, l'endpoint nell'intestazione Location restituisce HTTP 200.

Questo protocollo consente il coordinamento dei processi a esecuzione prolungata con client o servizi esterni in grado di eseguire il polling di un endpoint HTTP e seguire l'intestazione Location. Entrambe le implementazioni client e server di questo modello sono incorporate nelle API HTTP di Funzioni durevoli.

> [!NOTE]
> Per impostazione predefinita, tutte le azioni basate su HTTP fornite dalle [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) supportano il modello di operazione asincrono standard. Questa funzionalità rende possibile incorporare una funzione permanente a esecuzione prolungata come parte di un flusso di lavoro di App per la logica. È possibile trovare ulteriori dettagli sul supporto di app per la logica per i modelli HTTP asincroni nella documentazione relativa alle azioni del flusso di lavoro di App per la logica di Azure e alla [documentazione relativa ai trigger.](../../logic-apps/logic-apps-workflow-actions-triggers.md)

> [!NOTE]
> Le interazioni con le orchestrazioni possono essere eseguite da qualsiasi tipo di funzione, non solo dalle funzioni attivate da HTTP.

Per altre informazioni su come gestire le orchestrazioni e le entità usando le API client, vedere [l'articolo Gestione delle istanze.](durable-functions-instance-management.md)

## <a name="consuming-http-apis"></a>Utilizzo di API HTTPConsuming HTTP APIs

Come descritto nei vincoli del codice della [funzione dell'agente](durable-functions-code-constraints.md)di orchestrazione , le funzioni dell'agente di orchestrazione non possono eseguire i/o direttamente. Al contrario, in genere chiamano funzioni di attività che eseguono operazioni di I/O.Instead, they typically call [activity functions](durable-functions-types-features-overview.md#activity-functions) that do I/O operations.

A partire da Funzioni durevoli 2.0, le orchestrazioni possono utilizzare in modo nativo le API HTTP utilizzando [l'associazione](durable-functions-bindings.md#orchestration-trigger)del trigger di orchestrazione .

Il codice di esempio seguente mostra una funzione dell'agente di orchestrazione che effettua una richiesta HTTP in uscita:The following example code shows an orchestrator function making an outbound HTTP request:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Utilizzando l'azione "chiama HTTP", è possibile eseguire le azioni seguenti nelle funzioni dell'agente di orchestrazione:

* Chiamare le API HTTP direttamente dalle funzioni di orchestrazione, con alcune limitazioni menzionate più avanti.
* Supporta automaticamente i modelli di polling dello stato HTTP 202 sul lato client.
* Usare [le identità gestite di Azure](../../active-directory/managed-identities-azure-resources/overview.md) per effettuare chiamate HTTP autorizzate ad altri endpoint di Azure.Use Azure Managed Identities to make authorized HTTP calls to other Azure endpoints.

La possibilità di utilizzare le API HTTP direttamente dalle funzioni dell'agente di orchestrazione è intesa come comodità per un determinato set di scenari comuni. È possibile implementare tutte queste funzionalità manualmente utilizzando le funzioni di attività. In molti casi, le funzioni di attività potrebbero offrire maggiore flessibilità.

### <a name="http-202-handling"></a>Gestione HTTP 202

L'API "call HTTP" può implementare automaticamente il lato client del modello consumer di polling. Se un'API chiamata restituisce una risposta HTTP 202 con un'intestazione Location, la funzione dell'agente di orchestrazione esegue automaticamente il polling della risorsa Location fino a quando non riceve una risposta diversa da 202.If a called API returns an HTTP 202 response with a Location header, the orchestrator function automatically polls the Location resource until receiving a response other than 202. Questa risposta sarà la risposta restituita al codice della funzione dell'agente di orchestrazione.

> [!NOTE]
> Le funzioni dell'agente di orchestrazione supportano anche in modo nativo il modello di consumer di polling sul lato server, come descritto in [Monitoraggio delle operazioni asincrone](#async-operation-tracking). Questo supporto significa che le orchestrazioni in un'app per le funzioni possono facilmente coordinare le funzioni dell'agente di orchestrazione in altre app per le funzioni. È simile al concetto di [sottoorchestrazione,](durable-functions-sub-orchestrations.md) ma con il supporto per la comunicazione tra app. Questo supporto è particolarmente utile per lo sviluppo di app in stile microservizio.

### <a name="managed-identities"></a>Identità gestite

Funzioni durevoli supporta in modo nativo le chiamate alle API che accettano token di Azure Active Directory (Azure AD) per l'autorizzazione. Questo supporto usa [le identità gestite di Azure](../../active-directory/managed-identities-azure-resources/overview.md) per acquisire questi token.

Il codice seguente è un esempio di funzione dell'agente di orchestrazione .NET. La funzione effettua chiamate autenticate per riavviare una macchina virtuale utilizzando [l'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachines)delle macchine virtuali di Azure Resource Manager.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

Nell'esempio precedente, `tokenSource` il parametro è configurato per acquisire token di Azure AD per [Azure Resource Manager.](../../azure-resource-manager/management/overview.md) I token sono identificati dall'URI `https://management.core.windows.net`della risorsa. L'esempio presuppone che l'app per le funzioni corrente sia in esecuzione in locale o sia stata distribuita come app per le funzioni con un'identità gestita. Si presuppone che l'identità locale o l'identità gestita disponga dell'autorizzazione per gestire le macchine virtuali nel gruppo `myRG`di risorse specificato.

In fase di esecuzione, l'origine del token configurata restituisce automaticamente un token di accesso OAuth 2.0.At runtime, the configured token source automatically returns an OAuth 2.0 access token. L'origine aggiunge quindi il token come token di connessione all'intestazione Authorization della richiesta in uscita. Questo modello è un miglioramento rispetto all'aggiunta manuale di intestazioni di autorizzazione alle richieste HTTP per i motivi seguenti:This model is an improvement over adding manually adding authorization headers to HTTP requests for the following reasons:

* L'aggiornamento del token viene gestito automaticamente. Non devi preoccuparti dei token scaduti.
* I token non vengono mai archiviati nello stato di orchestrazione durevole.
* Non è necessario scrivere codice per gestire l'acquisizione di token.

È possibile trovare un esempio più completo [nell'esempio precompilato](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)di Riavvio registrato in C.

Le identità gestite non sono limitate alla gestione delle risorse di Azure.Managed identities aren't limited to Azure resource management. È possibile usare le identità gestite per accedere a qualsiasi API che accetta token di connessione di Azure AD, inclusi i servizi di Azure di Microsoft e le app Web dei partner. L'app Web di un partner può anche essere un'altra app per le funzioni. Per un elenco dei servizi di Azure di Microsoft che supportano l'autenticazione con Azure AD, vedere [Servizi di Azure che supportano l'autenticazione](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)di Azure AD.

### <a name="limitations"></a>Limitazioni

Il supporto incorporato per chiamare le API HTTP è una funzionalità di convenienza. Non è appropriato per tutti gli scenari.

Le richieste HTTP inviate dalle funzioni dell'agente di orchestrazione e le relative risposte vengono serializzate e persistenti come messaggi in coda. Questo comportamento di accodamento garantisce che le chiamate HTTP siano affidabili e sicure per la [riproduzione dell'orchestrazione.](durable-functions-orchestrations.md#reliability) Tuttavia, il comportamento di accodamento presenta anche limitazioni:However, the queuing behavior also has limitations:

* Ogni richiesta HTTP comporta latenza aggiuntiva rispetto a un client HTTP nativo.
* I messaggi di richiesta o risposta di grandi dimensioni che non possono rientrare in un messaggio della coda possono ridurre in modo significativo le prestazioni dell'orchestrazione. L'overhead dell'offload dei payload dei messaggi nell'archiviazione BLOB può causare una potenziale riduzione delle prestazioni.
* I payload di streaming, chunked e binary non sono supportati.
* La possibilità di personalizzare il comportamento del client HTTP è limitata.

Se una di queste limitazioni potrebbe influire sul caso d'uso, prendere in considerazione l'utilizzo di funzioni di attività e librerie client HTTP specifiche del linguaggio per effettuare chiamate HTTP in uscita.

> [!NOTE]
> Se si è uno sviluppatore .NET, ci si potrebbe chiedere perché questa funzionalità utilizza i tipi **DurableHttpRequest** e **DurableHttpResponse** anziché i tipi **HttpRequestMessage** e **HttpResponseMessage** incorporati.
>
> Questa scelta di progettazione è intenzionale. Il motivo principale è che i tipi personalizzati garantiscono agli utenti di non formulare presupposti non corretti sui comportamenti supportati del client HTTP interno. I tipi specifici di Funzioni durevoli consentono inoltre di semplificare la progettazione delle API. Possono inoltre rendere più facilmente disponibili funzionalità speciali come l'integrazione delle [identità gestite](#managed-identities) e il modello di consumer di [polling.](#http-202-handling) 

### <a name="extensibility-net-only"></a>Estendibilità (solo .NET)

È possibile personalizzare il comportamento del client HTTP interno dell'orchestrazione utilizzando [Azure Functions .NET dependency injection](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection). Questa capacità può essere utile per apportare piccole modifiche comportamentali. Può anche essere utile per unit test del client HTTP inserendo oggetti fittizi.

Nell'esempio seguente viene illustrato l'utilizzo dell'inserimento delle dipendenze per disabilitare la convalida del certificato TLS/SSL per le funzioni dell'agente di orchestrazione che chiamano endpoint HTTP esterni.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle entità durevoli](durable-functions-entities.md)
