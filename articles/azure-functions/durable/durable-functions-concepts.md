---
title: Modelli di funzioni durevoli e concetti tecnici in funzioni di Azure
description: Informazioni su come l'estensione funzioni permanenti di funzioni di Azure offre i vantaggi con stato di esecuzione del codice nel cloud.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: e5be81efcd655f1f0361d8c00d978a81c3e6caa5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443420"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Modelli di funzioni durevoli e concetti tecnici (funzioni di Azure)

Funzioni permanenti è un'estensione della [funzioni di Azure](../functions-overview.md) e [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). È possibile usare funzioni permanenti per scrivere funzioni con state in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii. 

Questo articolo offre informazioni dettagliate sui comportamenti dell'estensione funzioni permanenti per funzioni di Azure e i modelli comuni di implementazione. Le informazioni consentono di determinare come usare funzioni permanenti per aiutare a risolvere i problemi di sviluppo.

> [!NOTE]
> Funzioni permanenti è un'estensione avanzata per funzioni di Azure che non è appropriato per tutte le applicazioni. Questo articolo si presuppone una conoscenza avanzata con concetti [funzioni di Azure](../functions-overview.md) e le sfide coinvolte nello sviluppo di applicazioni senza server.

## <a name="patterns"></a>Modelli

Questa sezione descrive alcuni modelli comuni per applicazioni in cui le funzioni durevoli può essere utile.

### <a name="chaining"></a>Modello 1: Concatenamento di funzioni

Nella funzione di modello di concatenamento, una sequenza di funzioni viene eseguita in un ordine specifico. In questo modello, l'output di una funzione viene applicato all'input di un'altra funzione.

![Un diagramma della funzione di modello il concatenamento](./media/durable-functions-concepts/function-chaining.png)

È possibile usare funzioni permanenti per implementare la funzione di concatenamento di serie, in modo conciso come illustrato nell'esempio seguente:

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
> Esistono differenze minime tra la scrittura di una funzione permanente precompilata C# e la scrittura di una funzione permanente precompilata di C# script che viene visualizzato nell'esempio. In un C# funzione precompilati o meno parametri durevoli devono essere decorati con i rispettivi attributi. Un esempio è il `[OrchestrationTrigger]` dell'attributo per il `DurableOrchestrationContext` parametro. In un C# precompilato funzione permanente, se i parametri non sono decorati in modo corretto, il runtime non è possibile inserire le variabili nella funzione e si verifica un errore. Per altri esempi, vedere la [azure-functions-durable-extension esempi in GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

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

In questo esempio, i valori `F1`, `F2`, `F3`, e `F4` sono i nomi delle altre funzioni nell'app per le funzioni. È possibile implementare il flusso di controllo tramite codice imperativo per normali costrutti di codice. Il codice eseguito dall'alto verso il basso. Il codice può implicare esistente controllo flusso la semantica del linguaggio, ad esempio istruzioni condizionali e cicli. È possibile includere logica di gestione degli errori `try` / `catch` / `finally` blocchi.

È possibile usare la `context` parametri [DurableOrchestrationContext] \(.NET\) e il `context.df` object (JavaScript) per richiamare altre funzioni in base al nome, passare i parametri e restituire (funzione) output. Ogni volta che il codice chiama `await` (C#) o `yield` (JavaScript), i checkpoint di framework di funzioni permanenti lo stato dell'istanza della funzione corrente. Se il processo o della macchina virtuale viene riciclato durante l'esecuzione, l'istanza della funzione riprende dalla precedente `await` o `yield` chiamare. Per altre informazioni, vedere la sezione successiva, modello n. 2: Ventola out/fan-in.

> [!NOTE]
> Il `context` oggetti in JavaScript rappresentano l'intera [contesto funzione](../functions-reference-node.md#context-object), non solo il [DurableOrchestrationContext] parametro.

### <a name="fan-in-out"></a>Modello 2: Ventola out/fan-in

Nella ventola out/fan-in modello, eseguire più funzioni in parallelo e quindi attendere che tutte le funzioni alla fine. Spesso, alcune operazioni di aggregazione viene eseguita in base ai risultati restituiti dalle funzioni.

![Un diagramma di riparazione della ventola out/fan pattern](./media/durable-functions-concepts/fan-out-fan-in.png)

Alle funzioni normali, può effettuare il fan-facendo in modo che la funzione di inviare più messaggi a una coda. Fan-in è molto più complesso. Per effettuare il fan-in, in una normale funzione, si scrive codice per rilevare quando la fine delle funzioni attivate dalla coda e archiviarlo funzione gli output. 

L'estensione funzioni permanenti gestisce questo modello con codice relativamente semplice:

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

Il lavoro di tipo fan-out viene distribuito in più istanze del `F2` (funzione). Vengono monitorate tramite un elenco dinamico di attività. .NET `Task.WhenAll` API o JavaScript `context.df.Task.all` viene chiamata API, per attendere che tutte le funzioni chiamate alla fine. Successivamente, il `F2` funzione di output vengono aggregati dall'elenco di attività dinamiche e passati al `F3` (funzione).

Il checkpoint automatico che avviene al `await` oppure `yield` chiamare sul `Task.WhenAll` o `context.df.Task.all` assicura che un potenziale arresto anomalo a metà o un riavvio non richiede il riavvio di un'attività già completata.

### <a name="async-http"></a>Modello 3: API HTTP asincrone

Il modello APIs HTTP asincrona risolve il problema di coordinare lo stato di operazioni a esecuzione prolungata con client esterni. Un modo comune per implementare questo modello viene chiamata l'azione a esecuzione prolungata del trigger tramite HTTP. Quindi, reindirizza il client a un endpoint di stato che il client esegue il polling per informazioni su quando l'operazione viene completata.

![Un diagramma del modello API HTTP](./media/durable-functions-concepts/async-http-api.png)

Funzioni permanenti integra API incorporate che semplificano il codice scritto per interagire con esecuzioni delle funzioni con esecuzione prolungata. Gli esempi di avvio rapido di funzioni permanenti ([ C# ](durable-functions-create-first-csharp.md) e [JavaScript](quickstart-js-vscode.md)) Mostra un semplice comando REST che è possibile usare per avviare nuove istanze della funzione dell'agente di orchestrazione. Dopo l'avvio di un'istanza, l'estensione espone webhook APIs HTTP che query dello stato di funzione dell'agente di orchestrazione. 

Nell'esempio seguente mostra i comandi REST che avvia un agente di orchestrazione e query sul relativo stato. Per maggiore chiarezza, alcuni dettagli sono omessi dall'esempio.

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

Poiché il runtime di funzioni permanenti gestisce lo stato, non è necessario implementare il proprio meccanismo di rilevamento delle modifiche dello stato.

L'estensione funzioni permanenti ha webhook incorporati che gestire le orchestrazioni a esecuzione prolungata. È possibile implementare questo modello usando il proprio trigger di funzione (ad esempio, HTTP, una coda o hub eventi di Azure) e il `orchestrationClient` associazione. Ad esempio, è possibile utilizzare un messaggio in coda per attivare la terminazione. In alternativa, è possibile usare un trigger HTTP protetto da un criterio di autenticazione di Azure Active Directory anziché webhook incorporati che usano una chiave generata per l'autenticazione.

Di seguito sono riportati alcuni esempi di come usare il modello di API HTTP:

#### <a name="c"></a>C#

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

> [!WARNING]
> Quando si sviluppa in locale in JavaScript, per utilizzare i metodi `DurableOrchestrationClient`, è necessario impostare la variabile di ambiente `WEBSITE_HOSTNAME` al `localhost:<port>` (ad esempio, `localhost:7071`). Per altre informazioni su questo requisito, vedere [problema di GitHub 28](https://github.com/Azure/azure-functions-durable-js/issues/28).

In .NET, il parametro [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` è un valore proveniente dal binding di output `orchestrationClient`, incluso nell'estensione Durable Functions. In JavaScript, questo oggetto viene restituito chiamando `df.getClient(context)`. Questi oggetti forniscono metodi che consentono l'avvio, inviare eventi a, interruzione ed eseguire query per istanze della funzione dell'agente di orchestrazione nuove o esistenti.

Negli esempi precedenti, una funzione attivata tramite HTTP accetta una `functionName` valore dell'URL in ingresso e passa il valore per [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Il [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) associazione API quindi restituisce una risposta che contiene un `Location` intestazione e informazioni aggiuntive relative all'istanza. È possibile usare le informazioni in un secondo momento per cercare lo stato dell'istanza avviata o per terminare l'istanza.

### <a name="monitoring"></a>Modello 4: Monitorare

Il modello di monitoraggio fa riferimento a un processo ricorrente e flessibile in un flusso di lavoro. Un esempio esegue il polling fino a quando non vengono soddisfatte specifiche condizioni. È possibile usare una normale [trigger timer](../functions-bindings-timer.md) per soddisfare una semplice scenario, ad esempio un processo di pulizia periodico, ma il relativo intervallo è statico e la gestione delle durate delle istanze diventa complessa. È possibile usare funzioni permanenti per creare gli intervalli di ricorrenza flessibili, gestire la durata di attività e creare un monitoraggio più processi da una singola orchestrazione.

Un esempio di modello di monitoraggio deve invertire lo scenario di API HTTP asincrono precedente. Invece di esporre un endpoint per un client esterno monitorare un'operazione a esecuzione prolungata, il monitoraggio con esecuzione prolungata utilizza un endpoint esterno e quindi attende che una modifica dello stato.

![Un diagramma del modello di monitoraggio](./media/durable-functions-concepts/monitor.png)

In poche righe di codice, è possibile usare funzioni permanenti per creare più monitoraggi che osservano gli endpoint arbitrari. I monitoraggi possono terminare l'esecuzione quando viene soddisfatta una condizione, o la [DurableOrchestrationClient](durable-functions-instance-management.md) può terminare i monitoraggi. È possibile modificare un monitoraggio `wait` intervallo basato su una condizione specifica (ad esempio, backoff esponenziale). 

Il codice seguente implementa un monitoraggio di base:

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

Quando viene ricevuta una richiesta, viene creata una nuova istanza di orchestrazione per tale ID di processo. L'istanza esegue il polling di uno stato fino a quando non viene soddisfatta una condizione e terminato il ciclo. Un timer permanente consente di controllare l'intervallo di polling. Quindi, è possibile eseguire altre operazioni o l'orchestrazione può terminare. Quando la `context.CurrentUtcDateTime` (.NET) o `context.df.currentUtcDateTime` (JavaScript) supera il `expiryTime` valore, il monitoraggio termina.

### <a name="human"></a>Modello 5: Interazione umana

Molti processi automatizzati comportano qualche tipo di interazione umana. Che riguardano gli esseri umani in un processo automatizzato è difficile perché gli utenti non sono come a disponibilità elevata e capacità di risposta come servizi cloud. Un processo automatizzato potrebbe consentire per l'oggetto tramite i timeout e logica di compensazione.

Un processo di approvazione è un esempio di un processo di business con interazione umana. Approvazione di un responsabile potrebbe essere necessario per una nota spese che supera una certa quantità di dollari. Se il gestore non concede l'approvazione della nota spese entro 72 ore (forse il gestore di è andato in vacanza), un processo di escalation interviene per ottenere l'approvazione da un altro utente (ad esempio il responsabile del responsabile).

![Un diagramma del modello di interazione umana](./media/durable-functions-concepts/approval.png)

È possibile implementare il modello in questo esempio tramite una funzione di orchestrazione. L'agente di orchestrazione Usa una [timer permanenti](durable-functions-timers.md) per richiedere l'approvazione. L'agente di orchestrazione esegue l'escalation in caso di timeout. L'agente di orchestrazione in attesa di un [evento esterno](durable-functions-external-events.md), ad esempio una notifica che viene generata da un'interazione umana.

Questi esempi di creano un processo di approvazione per illustrare il modello di interazione umana con:

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

Per creare il timer durevole, chiamare `context.CreateTimer` (.NET) o `context.df.createTimer` (JavaScript). La notifica viene ricevuta da `context.WaitForExternalEvent` (.NET) o `context.df.waitForExternalEvent` (JavaScript). Quindi `Task.WhenAny` (.NET) o `context.df.Task.any` (JavaScript) viene chiamato per decidere se eseguire l'escalation (timeout si verifica prima di tutto) o il processo di approvazione (l'approvazione viene ricevuta prima del timeout).

Un client esterno può recapitare la notifica degli eventi a una funzione dell'agente di orchestrazione in attesa tramite il [APIs HTTP predefinite](durable-functions-http-api.md#raise-event) oppure usando la [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API da un'altra funzione:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="the-technology"></a>La tecnologia

Dietro le quinte, l'estensione funzioni permanenti è costruita basandosi sul [Durable Task Framework](https://github.com/Azure/durabletask), una libreria open source su GitHub che consente di creare orchestrazioni di attività permanenti. Funzioni di Azure è l'evoluzione senza server di processi Web di Azure, funzioni permanenti è l'evoluzione senza server di Durable Task Framework. Microsoft e altre organizzazioni che usano ampiamente Durable Task Framework per automatizzare i processi di importanza strategica. È una scelta ideale per l'ambiente senza server di Funzioni di Azure.

### <a name="event-sourcing-checkpointing-and-replay"></a>Origine eventi, impostazione di checkpoint e riesecuzione

Le funzioni di orchestrazione mantengono in modo affidabile lo stato di esecuzione usando il [origine evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) schema progettuale. Anziché archiviare direttamente lo stato corrente di un'orchestrazione, l'estensione funzioni permanenti Usa un archivio di solo accodamento per registrare la serie completa di azioni che accetta l'orchestrazione di funzioni. Un archivio di solo accodamento offre numerosi vantaggi rispetto a "il dump di" stato di runtime completo. I vantaggi includono la velocità di risposta, scalabilità e prestazioni migliori. È anche possibile ottenere la coerenza finale per i dati transazionali e audit trail completi e cronologia. Gli audit trail supporta azioni di compensazione affidabili.

Funzioni permanenti Usa in modo trasparente l'origine evento. Dietro le quinte, il `await` (C#) o `yield` operatore (JavaScript) in una funzione di orchestrazione restituisce il controllo del thread dell'agente di orchestrazione al dispatcher di Durable Task Framework. Il dispatcher quindi esegue il commit di eventuali nuove azioni pianificate dalla funzione di orchestrazione (ad esempio, la chiamata di una o più funzioni figlio o la pianificazione di un timer permanente) in un archivio. L'azione di commit trasparente viene aggiunto alla cronologia di esecuzione dell'istanza di orchestrazione. La cronologia viene archiviata in una tabella di archiviazione. L'azione di commit aggiunge quindi messaggi a una coda per pianificare le operazioni effettive. A questo punto, la funzione di orchestrazione può essere scaricata dalla memoria. 

La fatturazione per la funzione di orchestrazione viene interrotta se si usa il piano a consumo funzioni di Azure. Quando ci sono altre operazioni da eseguire, il riavvio di funzione, e il relativo stato viene ricostruito.

Quando una funzione di orchestrazione ha altre operazioni da eseguire (ad esempio, si riceve un messaggio di risposta o un timer permanente scade), l'agente di orchestrazione viene attivato ed esegue nuovamente l'intera funzione dall'inizio per ricostruire lo stato locale. 

Durante la riproduzione, se il codice prova a chiamare una funzione (o qualsiasi altro async lavoro), Durable Task Framework consulta la cronologia di esecuzione dell'orchestrazione corrente. Se rileva che il [funzione di attività](durable-functions-types-features-overview.md#activity-functions) già eseguita e ha restituito un risultato, riesegue il risultato della funzione e il codice dell'agente di orchestrazione continua a eseguire. Riproduzione continua fino al termine del codice della funzione o fino a quando non è pianificato nuovo lavoro asincrono.

### <a name="orchestrator-code-constraints"></a>Vincoli del codice dell'agente di orchestrazione

Il comportamento di riesecuzione del codice dell'agente di orchestrazione crea vincoli sul tipo di codice che è possibile scrivere in una funzione di orchestrazione. Ad esempio, il codice dell'agente di orchestrazione deve essere deterministico perché verrà rieseguito più volte e deve produrre lo stesso risultato ogni volta. Per l'elenco completo dei vincoli, vedere [vincoli di codice dell'agente di orchestrazione](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitoraggio e diagnostica

L'estensione funzioni permanenti invia automaticamente dati di rilevamento strutturati [Application Insights](../functions-monitoring.md) se si configura l'app per le funzioni con una chiave di strumentazione di Application Insights di Azure. È possibile usare i dati di rilevamento per monitorare le azioni e lo stato di avanzamento delle orchestrazioni.

Ecco un esempio dell'aspetto di funzioni permanenti gli eventi di rilevamento nel portale di Application Insights quando si usa [Application Insights Analitica](../../application-insights/app-insights-analytics.md):

![Risultati della query di Application Insights](./media/durable-functions-concepts/app-insights-1.png)

È possibile trovare i dati strutturati utili nel `customDimensions` campo in ogni voce di log. Di seguito è riportato un esempio di una voce che viene espanso completamente:

![Il campo customDimensions in una query di Application Insights](./media/durable-functions-concepts/app-insights-2.png)

A causa del comportamento di riproduzione del dispatcher di Durable Task Framework, probabilmente saranno presenti voci di log ridondanti per le azioni rieseguite. Le voci di log ridondanti possono aiutarti a comprendere il comportamento di riesecuzione del motore di base. Il [diagnostica](durable-functions-diagnostics.md) articolo illustrate query che filtrano i log di riesecuzione, in modo da visualizzare solo i log "in tempo reale".

## <a name="storage-and-scalability"></a>Archiviazione e scalabilità

L'estensione funzioni permanenti Usa code, tabelle e BLOB in archiviazione di Azure in modo permanente l'esecuzione della cronologia dello stato e attivare l'esecuzione della funzione. È possibile usare l'account di archiviazione predefinito per l'app per le funzioni oppure è possibile configurare un account di archiviazione separato. Potrebbe essere un account separato basato sui limiti di velocità effettiva di archiviazione. Il codice dell'agente di orchestrazione che scritto non interagisce con le entità in questi account di archiviazione. Durable Task Framework consente di gestire le entità direttamente come dettaglio di implementazione.

Le funzioni di orchestrazione pianificano le funzioni attività e ricevono le relative risposte tramite messaggi di coda interni. Quando un'app per le funzioni viene eseguita nel piano a consumo funzioni di Azure, il [controller di scalabilità di funzioni di Azure](../functions-scale.md#how-the-consumption-plan-works) consente di monitorare queste code. Aggiunte nuove istanze di calcolo in base alle esigenze. Quando viene scalata orizzontalmente a più macchine virtuali, una funzione di orchestrazione può essere eseguita su una macchina virtuale, mentre le funzioni di attività che le chiamate di funzione dell'agente di orchestrazione può essere eseguita su più macchine virtuali diverse. Per altre informazioni sul comportamento di scalabilità di funzioni permanenti, vedere [scalabilità e prestazioni](durable-functions-perf-and-scale.md).

La cronologia di esecuzione per gli account dell'agente di orchestrazione viene archiviata nell'archiviazione tabelle. Ogni volta che un'istanza viene riattivata in una determinata VM, l'agente di orchestrazione recupera la cronologia delle esecuzioni dall'archiviazione tabella in modo da poter ricompilare il suo stato locale. Un aspetto pratico della disponibilità della cronologia nell'archiviazione tabelle è che è possibile usare strumenti come [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) per visualizzare la cronologia delle orchestrazioni.

I BLOB di archiviazione vengono utilizzati principalmente come un meccanismo di leasing per coordinare l'aumento delle istanze di orchestrazione tra più macchine virtuali. I BLOB di archiviazione contengono i dati per i messaggi di grandi dimensioni che non possono essere archiviati direttamente in tabelle o code.

![Screenshot di Esplora archivi di Azure](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> Sebbene sia comodo e utile esaminare la cronologia di esecuzione nell'archiviazione tabelle, non apportare le eventuali dipendenze su questa tabella. La tabella potrebbe cambiare evolversi nell'estensione funzioni permanenti.

## <a name="known-issues"></a>Problemi noti

Tutti i problemi noti dovrebbero essere segnalati nell'elenco di [problemi GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Se si verifica un problema e non è possibile trovare il problema in GitHub, aprire un nuovo problema. Includere una descrizione dettagliata del problema.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzioni durevoli, vedere [funzioni durevoli tipi e le funzionalità](durable-functions-types-features-overview.md). 

Attività iniziali

> [!div class="nextstepaction"]
> [Creare la prima funzione permanente](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
