---
title: Modelli di Durable Functions e concetti tecnici in funzioni di Azure
description: Informazioni sul modo in cui l'estensione Durable Functions in funzioni di Azure offre i vantaggi dell'esecuzione di codice con stato nel cloud.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: fe3000181ed02e3640e7af48fa492f4a7db55191
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734579"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Modelli di Durable Functions e concetti tecnici (funzioni di Azure)

Durable Functions è un'estensione di [funzioni di Azure](../functions-overview.md) e processi Web di [Azure](../../app-service/web-sites-create-web-jobs.md). È possibile usare Durable Functions per scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii. 

Questo articolo fornisce informazioni dettagliate sui comportamenti dell'estensione Durable Functions per funzioni di Azure e modelli di implementazione comuni. Le informazioni possono essere utili per determinare come utilizzare Durable Functions per contribuire alla risoluzione delle proprie esigenze di sviluppo.

> [!NOTE]
> Durable Functions è un'estensione avanzata per funzioni di Azure che non è appropriata per tutte le applicazioni. Questo articolo presuppone che l'utente abbia una certa familiarità con i concetti in [funzioni di Azure](../functions-overview.md) e i problemi correlati allo sviluppo di applicazioni senza server.

## <a name="patterns"></a>Criterio di ricerca

In questa sezione vengono descritti alcuni modelli di applicazione comuni in cui Durable Functions possono essere utili.

### <a name="chaining"></a>Modello 1: Concatenamento di funzioni

Nel modello di concatenamento di funzioni, una sequenza di funzioni viene eseguita in un ordine specifico. In questo modello, l'output di una funzione viene applicato all'input di un'altra funzione.

![Diagramma del modello di concatenamento di funzioni](./media/durable-functions-concepts/function-chaining.png)

È possibile usare Durable Functions per implementare il modello di concatenamento di funzioni in modo conciso, come illustrato nell'esempio seguente:

#### <a name="precompiled-c"></a>C# precompilato

```csharp
public static async Task<object> Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

#### <a name="c-script"></a>Script C#

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

> [!NOTE]
> Esistono differenze minime tra la scrittura di una funzione durevole precompilata in C# e la scrittura di una funzione durevole C# precompilata nello script. In una C# funzione precompilata, i parametri durevoli devono essere decorati con i rispettivi attributi. Un esempio è l' `[OrchestrationTrigger]` attributo per il `DurableOrchestrationContext` parametro. In una C# funzione durevole precompilata, se i parametri non sono decorati correttamente, il runtime non può inserire le variabili nella funzione e si verifica un errore. Per altri esempi, vedere gli [esempi di Azure-Functions-Durable-Extension su GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

In questo esempio, i valori `F1` `F3`, `F2`, e `F4` sono i nomi di altre funzioni nell'app per le funzioni. È possibile implementare il flusso di controllo utilizzando costrutti di codifica imperativi normali. Il codice viene eseguito dall'alto verso il basso. Il codice può coinvolgere la semantica del flusso di controllo del linguaggio esistente, ad esempio condizionali e cicli. È possibile includere la logica di gestione `try` degli errori nei / / `catch` `finally` blocchi.

È possibile usare il `context` parametro [DurableOrchestrationContext] \(.NET\) e l' `context.df` oggetto (JavaScript) per richiamare altre funzioni in base al nome, passare i parametri e restituire l'output della funzione. Ogni volta che il codice `await` chiamaC#() `yield` o (JavaScript), il Durable Functions Framework esegue il checkpoint dello stato di avanzamento dell'istanza della funzione corrente. Se il processo o la macchina virtuale ricicla a metà dell'esecuzione, l'istanza della funzione riprende dalla chiamata precedente `await` o `yield` . Per ulteriori informazioni, vedere la sezione successiva, pattern #2: Ventola/ventola in uscita.

> [!NOTE]
> L' `context` oggetto in JavaScript rappresenta l'intero [contesto della funzione](../functions-reference-node.md#context-object), non solo il parametro [DurableOrchestrationContext] .

### <a name="fan-in-out"></a>Modello 2: Fan-out/ventola

Nel modello fan-out/fan in è possibile eseguire più funzioni in parallelo e quindi attendere il completamento di tutte le funzioni. Spesso, alcune operazioni di aggregazione vengono eseguite sui risultati restituiti dalle funzioni.

![Diagramma dello schema fan-out/fan](./media/durable-functions-concepts/fan-out-fan-in.png)

Con le funzioni normali, è possibile fare un fan in modo che la funzione invii più messaggi a una coda. Il ritorno a ventaglio è molto più complesso. Per eseguire la ventola, in una funzione normale è necessario scrivere il codice per tenere traccia del momento in cui terminano le funzioni attivate dalla coda e quindi archiviare gli output della funzione. 

L'estensione Durable Functions gestisce questo modello con codice relativamente semplice:

#### <a name="precompiled-c"></a>C# precompilato

```csharp
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Il lavoro di fan-out viene distribuito a più istanze della `F2` funzione. Il lavoro viene rilevato usando un elenco dinamico di attività. Viene chiamata `Task.WhenAll` l'API .NET `context.df.Task.all` o l'API JavaScript per attendere il completamento di tutte le funzioni chiamate. Gli output della `F2` funzione vengono quindi aggregati dall'elenco attività dinamica e passati `F3` alla funzione.

Il checkpoint automatico che si verifica `await` in o `yield` la chiamata di `Task.WhenAll` su `context.df.Task.all` o garantisce che un potenziale arresto anomalo o un riavvio a metà non richieda il riavvio di un'attività già completata.

### <a name="async-http"></a>Modello 3: API HTTP asincrone

Il modello di API HTTP asincrone risolve il problema di coordinare lo stato delle operazioni a esecuzione prolungata con client esterni. Un modo comune per implementare questo modello consiste nel fatto che una chiamata HTTP attiva l'azione a esecuzione prolungata. Quindi, reindirizzare il client a un endpoint di stato che il client esegue il polling per apprendere al termine dell'operazione.

![Diagramma del modello di API HTTP](./media/durable-functions-concepts/async-http-api.png)

Durable Functions fornisce API incorporate che semplificano il codice scritto per interagire con le esecuzioni di funzioni a esecuzione prolungata. Gli esempi di avvio rapido[C#](durable-functions-create-first-csharp.md) Durable Functions (e [JavaScript](quickstart-js-vscode.md)) mostrano un semplice comando REST che è possibile usare per avviare nuove istanze della funzione dell'agente di orchestrazione. Dopo l'avvio di un'istanza, l'estensione espone le API HTTP webhook che eseguono query sullo stato della funzione dell'agente di orchestrazione. 

Nell'esempio seguente vengono illustrati i comandi REST che avviano un agente di orchestrazione e ne eseguono una query. Per maggiore chiarezza, alcuni dettagli sono omessi dall'esempio.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Poiché il runtime di Durable Functions gestisce lo stato, non è necessario implementare un meccanismo di rilevamento dello stato personalizzato.

L'estensione Durable Functions dispone di Webhook predefiniti che gestiscono le orchestrazioni a esecuzione prolungata. È possibile implementare questo modello usando trigger di funzione personalizzati (ad esempio http, una coda o hub eventi di Azure) e l' `orchestrationClient` associazione. Ad esempio, è possibile usare un messaggio della coda per attivare la terminazione. In alternativa, è possibile usare un trigger HTTP protetto da un Azure Active Directory Criteri di autenticazione invece dei webhook predefiniti che usano una chiave generata per l'autenticazione.

Di seguito sono riportati alcuni esempi di come usare il modello di API HTTP:

#### <a name="precompiled-c"></a>C# precompilato

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
[FunctionName("StartNewOrchestration")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="c-script"></a>Script C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

In .NET, il parametro [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` è un valore proveniente dal binding di output `orchestrationClient`, incluso nell'estensione Durable Functions. In JavaScript, questo oggetto viene restituito chiamando `df.getClient(context)`. Questi oggetti forniscono metodi che è possibile utilizzare per avviare, inviare eventi a, terminare ed eseguire query per le istanze della funzione dell'agente di orchestrazione nuove o esistenti.

Negli esempi precedenti, una funzione attivata tramite http accetta un `functionName` valore dall'URL in ingresso e passa il valore a [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). L'API di associazione [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) restituisce quindi una risposta che contiene `Location` un'intestazione e informazioni aggiuntive sull'istanza. È possibile utilizzare le informazioni in un secondo momento per cercare lo stato dell'istanza avviata o terminare l'istanza.

### <a name="monitoring"></a>Modello 4: Monitoraggio

Il modello di monitoraggio si riferisce a un processo flessibile e ricorrente in un flusso di lavoro. Un esempio è il polling fino a quando non vengono soddisfatte determinate condizioni. È possibile utilizzare un normale [trigger timer](../functions-bindings-timer.md) per risolvere uno scenario di base, ad esempio un processo di pulizia periodico, ma l'intervallo è statico e la gestione delle durate delle istanze diventa complessa. È possibile utilizzare Durable Functions per creare intervalli di ricorrenza flessibili, gestire la durata delle attività e creare più processi di monitoraggio da una singola orchestrazione.

Un esempio di modello di monitoraggio consiste nell'annullare lo scenario precedente dell'API HTTP Async. Anziché esporre un endpoint affinché un client esterno esegua il monitoraggio di un'operazione di lunga durata, il monitoraggio con esecuzione prolungata utilizza un endpoint esterno e quindi attende una modifica dello stato.

![Diagramma del pattern di monitoraggio](./media/durable-functions-concepts/monitor.png)

In poche righe di codice è possibile usare Durable Functions per creare più monitoraggi che osservino gli endpoint arbitrari. I monitoraggi possono terminare l'esecuzione quando viene soddisfatta una condizione oppure il [DurableOrchestrationClient](durable-functions-instance-management.md) può terminare i monitoraggi. È possibile modificare l'intervallo di `wait` un monitoraggio in base a una condizione specifica, ad esempio backoff esponenziale. 

Il codice seguente implementa un monitoraggio di base:

#### <a name="precompiled-c"></a>C# precompilato

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

Quando viene ricevuta una richiesta, viene creata una nuova istanza di orchestrazione per tale ID di processo. L'istanza esegue il polling di uno stato fino a quando non viene soddisfatta una condizione e terminato il ciclo. Un timer durevole controlla l'intervallo di polling. Quindi, è possibile eseguire più operazioni oppure l'orchestrazione può terminare. Quando (.NET) o `context.df.currentUtcDateTime` (JavaScript) supera il `expiryTime` valore, il monitoraggio termina. `context.CurrentUtcDateTime`

### <a name="human"></a>Modello 5: Interazione umana

Molti processi automatizzati coinvolgono un tipo di interazione umana. Coinvolgere gli utenti in un processo automatizzato è difficile perché gli utenti non sono a disponibilità elevata e rispondono ai servizi cloud. Un processo automatizzato può consentire questo problema utilizzando i timeout e la logica di compensazione.

Un processo di approvazione è un esempio di processo di business che prevede l'interazione umana. L'approvazione da parte di un responsabile potrebbe essere obbligatoria per una nota spese che supera un determinato importo in dollari. Se il responsabile non approva la nota spese entro 72 ore (forse il Manager è andato in vacanza), viene avviata una procedura di escalation per ottenere l'approvazione da un altro utente (forse il responsabile del Manager).

![Diagramma del modello di interazione umana](./media/durable-functions-concepts/approval.png)

È possibile implementare il modello in questo esempio utilizzando una funzione dell'agente di orchestrazione. L'agente di orchestrazione usa un [timer durevole](durable-functions-timers.md) per richiedere l'approvazione. L'agente di orchestrazione viene inoltrato se si verifica il timeout. L'agente di orchestrazione attende un [evento esterno](durable-functions-external-events.md), ad esempio una notifica generata da un'interazione umana.

Questi esempi creano un processo di approvazione per illustrare il modello di interazione umana:

#### <a name="precompiled-c"></a>C# precompilato

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Per creare il timer durevole, chiamare `context.CreateTimer` (.NET) o `context.df.createTimer` (JavaScript). La notifica viene ricevuta da `context.WaitForExternalEvent` (.NET) o `context.df.waitForExternalEvent` (JavaScript). Viene quindi chiamato (.NET) `context.df.Task.any` o (JavaScript) per decidere se eseguire l'escalation (il timeout si verifica per primo) o elaborare l'approvazione (l'approvazione viene ricevuta prima del timeout). `Task.WhenAny`

Un client esterno può recapitare la notifica degli eventi a una funzione dell'agente di orchestrazione in attesa usando le [API HTTP predefinite](durable-functions-http-api.md#raise-event) o usando l'API [DurableOrchestrationClient. RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) di un'altra funzione:

#### <a name="precompiled-c"></a>C# precompilato

```csharp
public static async Task Run(
  [HttpTrigger] string instanceId,
  [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="javascript"></a>JavaScript

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

### <a name="aggregator"></a>#6 modello: Aggregator (anteprima)

Il sesto modello riguarda l'aggregazione dei dati degli eventi in un periodo di tempo in un'unica *entità*indirizzabile. In questo modello, i dati aggregati possono provenire da più origini, possono essere recapitati in batch o possono essere distribuiti in periodi di tempo prolungati. L'aggregatore potrebbe avere l'esigenza di intervenire sui dati degli eventi quando arriva ed è possibile che i client esterni debbano eseguire query sui dati aggregati.

![Diagramma aggregator](./media/durable-functions-concepts/aggregator.png)

L'aspetto più difficile del tentativo di implementare questo modello con funzioni normali senza stato è che il controllo della concorrenza diventa un problema enorme. Non solo è necessario preoccuparsi di più thread che modificano contemporaneamente gli stessi dati, ma è anche necessario assicurarsi che l'aggregatore venga eseguito solo su una singola macchina virtuale alla volta.

Utilizzando una [funzione di entità durevole](durable-functions-preview.md#entity-functions), è possibile implementare facilmente questo modello come una singola funzione.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Le entità durevoli possono anche essere modellate come classi .NET. Questo può essere utile se l'elenco delle operazioni diventa grande e se è principalmente statico. L'esempio seguente è un'implementazione equivalente dell'entità `Counter` con le classi e i metodi .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

I client possono accodare *operazioni* per (anche note come "segnalazione") a una funzione di entità `orchestrationClient` che usa l'associazione.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

I proxy generati dinamicamente sono disponibili anche per segnalare le entità in modo indipendente dai tipi. Oltre alla segnalazione, i client possono anche eseguire una query per lo stato di una funzione di entità usando i `orchestrationClient` metodi sull'associazione.

> [!NOTE]
> Le funzioni di entità sono attualmente disponibili solo nell' [anteprima Durable Functions 2,0](durable-functions-preview.md).

## <a name="the-technology"></a>La tecnologia

In background, l'estensione Durable Functions si basa sul [Framework di attività durevoli](https://github.com/Azure/durabletask), una libreria open source su GitHub usata per creare orchestrazioni di attività durevoli. Analogamente a funzioni di Azure è l'evoluzione senza server di processi Web di Azure, Durable Functions è l'evoluzione senza server di Durable Task Framework. Microsoft e altre organizzazioni utilizzano ampiamente il framework delle attività durevoli per automatizzare i processi mission-critical. È una scelta ideale per l'ambiente senza server di Funzioni di Azure.

### <a name="event-sourcing-checkpointing-and-replay"></a>Origine eventi, impostazione di checkpoint e riesecuzione

Le funzioni dell'agente di orchestrazione gestiscono in modo affidabile lo stato di esecuzione [usando il modello](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) di progettazione dell'origine Anziché archiviare direttamente lo stato corrente di un'orchestrazione, l'estensione Durable Functions utilizza un archivio di solo accodamento per registrare la serie completa di azioni accettate dall'orchestrazione di funzioni. Un archivio di solo accodamento presenta molti vantaggi rispetto al "dump" dello stato di runtime completo. I vantaggi includono un miglioramento delle prestazioni, della scalabilità e della velocità di risposta. Si ottiene anche la coerenza finale per i dati transazionali e la cronologia e i percorsi di controllo completi. I Audit Trail supportano azioni di compensazione affidabili.

Durable Functions utilizza in modo trasparente il sourcing degli eventi. Dietro le quinte, `await` l'C#operatore ( `yield` ) o (JavaScript) in una funzione dell'agente di orchestrazione restituisce il controllo del thread dell'agente di orchestrazione al dispatcher di Durable Task Framework. Il dispatcher quindi esegue il commit di eventuali nuove azioni pianificate dalla funzione di orchestrazione (ad esempio, la chiamata di una o più funzioni figlio o la pianificazione di un timer permanente) in un archivio. L'azione di commit trasparente viene accodata alla cronologia di esecuzione dell'istanza dell'orchestrazione. La cronologia viene archiviata in una tabella di archiviazione. L'azione di commit aggiunge quindi messaggi a una coda per pianificare le operazioni effettive. A questo punto, la funzione di orchestrazione può essere scaricata dalla memoria. 

La fatturazione per la funzione dell'agente di orchestrazione si interrompe se si usa il piano a consumo di funzioni di Azure. Quando sono presenti più operazioni da eseguire, la funzione viene riavviata e il relativo stato viene ricostruito.

Quando una funzione di orchestrazione dispone di più operazioni da eseguire (ad esempio, viene ricevuto un messaggio di risposta o scade un timer durevole), l'agente di orchestrazione viene riattivato ed esegue nuovamente l'intera funzione dall'inizio per ricompilare lo stato locale. 

Durante la riproduzione, se il codice tenta di chiamare una funzione (o di eseguire altre operazioni asincrone), il Framework di attività permanenti consulta la cronologia di esecuzione dell'orchestrazione corrente. Se rileva che la [funzione di attività](durable-functions-types-features-overview.md#activity-functions) è già stata eseguita e ha restituito un risultato, riprodurrà il risultato della funzione e il codice dell'agente di orchestrazione continuerà a essere eseguito. La riproduzione continua fino al termine del codice della funzione o fino a quando non viene pianificato un nuovo lavoro asincrono.

### <a name="orchestrator-code-constraints"></a>Vincoli del codice dell'agente di orchestrazione

Il comportamento di riproduzione del codice dell'agente di orchestrazione crea vincoli sul tipo di codice che è possibile scrivere in una funzione dell'agente di orchestrazione. Ad esempio, il codice dell'agente di orchestrazione deve essere deterministico perché verrà rieseguito più volte e deve produrre lo stesso risultato ogni volta. Per l'elenco completo dei vincoli, vedere vincoli del codice dell'agente di [orchestrazione](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitoraggio e diagnostica

L'estensione Durable Functions emette automaticamente dati di rilevamento strutturati per [Application Insights](../functions-monitoring.md) se si configura l'app per le funzioni con una chiave di strumentazione di applicazione Azure Insights. È possibile utilizzare i dati di rilevamento per monitorare le azioni e lo stato di avanzamento delle orchestrazioni.

Di seguito è riportato un esempio dell'aspetto degli eventi di rilevamento Durable Functions nel portale di Application Insights quando si usa [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Risultati della query Application Insights](./media/durable-functions-concepts/app-insights-1.png)

È possibile trovare dati strutturati utili nel `customDimensions` campo di ogni voce di log. Di seguito è riportato un esempio di una voce completamente espansa:

![Campo customDimensions in una query Application Insights](./media/durable-functions-concepts/app-insights-2.png)

A causa del comportamento di riproduzione del dispatcher di Durable Task Framework, probabilmente saranno presenti voci di log ridondanti per le azioni rieseguite. Le voci di log ridondanti consentono di comprendere il comportamento di riproduzione del motore di base. L'articolo di [diagnostica](durable-functions-diagnostics.md) Mostra le query di esempio che filtrano i log di riproduzione, in modo da visualizzare solo i log "in tempo reale".

## <a name="storage-and-scalability"></a>Archiviazione e scalabilità

L'estensione Durable Functions usa le code, le tabelle e i BLOB in archiviazione di Azure per salvare in modo permanente lo stato della cronologia di esecuzione e attivare l'esecuzione della funzione. È possibile usare l'account di archiviazione predefinito per l'app per le funzioni oppure è possibile configurare un account di archiviazione separato. Potrebbe essere necessario un account separato basato sui limiti di velocità effettiva di archiviazione. Il codice dell'agente di orchestrazione scritto non interagisce con le entità in questi account di archiviazione. Il Framework di attività permanenti gestisce le entità direttamente come dettaglio di implementazione.

Le funzioni di orchestrazione pianificano le funzioni attività e ricevono le relative risposte tramite messaggi di coda interni. Quando un'app per le funzioni viene eseguita nel piano a consumo di funzioni di Azure, il [controller di scalabilità di funzioni di Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) monitora queste code. Le nuove istanze di calcolo vengono aggiunte in base alle esigenze. Quando si aumentano le macchine virtuali, una funzione dell'agente di orchestrazione può essere eseguita in una macchina virtuale, mentre le funzioni di attività chiamate dalla funzione dell'agente di orchestrazione possono essere eseguite su diverse VM diverse. Per ulteriori informazioni sul comportamento della scalabilità di Durable Functions, vedere [prestazioni e scalabilità](durable-functions-perf-and-scale.md).

La cronologia di esecuzione per gli account dell'agente di orchestrazione viene archiviata nell'archivio tabelle. Ogni volta che un'istanza viene riattivata in una determinata macchina virtuale, l'agente di orchestrazione recupera la cronologia di esecuzione dall'archivio tabelle per poter ricompilare il proprio stato locale. Un aspetto pratico della disponibilità della cronologia nell'archivio tabelle è che è possibile utilizzare strumenti come [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) per visualizzare la cronologia delle orchestrazioni.

I BLOB di archiviazione vengono usati principalmente come meccanismo di leasing per coordinare la scalabilità orizzontale delle istanze di orchestrazione tra più macchine virtuali. I BLOB di archiviazione contengono dati per messaggi di grandi dimensioni che non possono essere archiviati direttamente in tabelle o code.

![Screenshot di Azure Storage Explorer](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Sebbene sia facile e comodo visualizzare la cronologia di esecuzione nell'archiviazione tabelle, non creare dipendenze da questa tabella. La tabella potrebbe cambiare con l'evoluzione dell'estensione Durable Functions.

## <a name="known-issues"></a>Problemi noti

Tutti i problemi noti dovrebbero essere segnalati nell'elenco di [problemi GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Se si riscontra un problema e non si riesce a trovare il problema in GitHub, aprire un nuovo problema. Includere una descrizione dettagliata del problema.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Durable Functions, vedere [Durable Functions tipi di funzione e funzionalità](durable-functions-types-features-overview.md). 

Attività iniziali

> [!div class="nextstepaction"]
> [Creare la prima funzione permanente](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html