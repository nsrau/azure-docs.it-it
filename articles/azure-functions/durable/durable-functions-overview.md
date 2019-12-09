---
title: Panoramica di Funzioni permanenti - Azure
description: Introduzione all'estensione Funzioni permanenti per Funzioni di Azure.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 684c067f393b1f6037e67d3b49a861341f3353c8
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706131"
---
# <a name="what-are-durable-functions"></a>Informazioni su Durable Functions

*Durable Functions* è un'estensione di [Funzioni di Azure](../functions-overview.md) che consente di scrivere funzioni con stato in un ambiente di calcolo serverless. L'estensione permette di definire flussi di lavoro con stato, scrivendo [*funzioni dell'agente di orchestrazione*](durable-functions-orchestrations.md), ed entità con stato, scrivendo [*funzioni di entità*](durable-functions-entities.md) tramite il modello di programmazione di Funzioni di Azure. Dietro le quinte, l'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii, consentendo di concentrarsi sulla logica di business.

## <a name="language-support"></a>Lingue supportate

Durable Functions supporta attualmente i linguaggi seguenti:

* **C#** : sia le [librerie di classe precompilate](../functions-dotnet-class-library.md) che lo [script C#](../functions-reference-csharp.md).
* **JavaScript**: supportato per la versione 2.x del runtime di Funzioni di Azure. Richiede la versione 1.7.0 dell'estensione Durable Functions, o versione successiva. 
* **F#** : librerie di classe precompilate e script F#. Lo script F# è supportato solo per la versione 1.x del runtime di Funzioni di Azure.

Durable Functions ha l'obiettivo di supportare tutti i [linguaggi di Funzioni di Azure](../supported-languages.md). Vedere l'[elenco dei problemi di Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) per ottenere lo stato del lavoro più recente a supporto di linguaggi aggiuntivi.

Come per Funzioni di Azure, sono disponibili modelli che contribuiscono allo sviluppo di Durable Functions tramite [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) e il [portale di Azure](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Modelli di applicazione

Il caso d'uso principale per Durable Functions è la semplificazione dei requisiti complessi di coordinamento con stato nelle applicazioni senza server. Le sezioni seguenti descrivono i modelli di applicazione tipici che possono trarre vantaggio da Durable Functions:

* [Concatenamento di funzioni](#chaining)
* [Fan-out/fan-in](#fan-in-out)
* [API HTTP asincrone](#async-http)
* [Monitoraggio](#monitoring)
* [Interazione umana](#human)
* [Aggregatore (entità con stato)](#aggregator)

### <a name="chaining"></a>Modello 1: Concatenamento di funzioni

Nel modello di concatenamento di funzioni, una sequenza di funzioni viene eseguita in un ordine specifico. L'output di una funzione viene applicato all'input di un'altra.

![Diagramma del modello di concatenamento di funzioni](./media/durable-functions-concepts/function-chaining.png)

È possibile usare Durable Functions per implementare il modello di concatenamento di funzioni in modo conciso, come illustrato nell'esempio seguente.

In questo esempio i valori `F1`, `F2`, `F3` e `F4` corrispondono ai nomi di altre funzioni nell'app per le funzioni. È possibile implementare il flusso di controllo usando normali costrutti di codice imperativo. Il codice viene eseguito dall'alto verso il basso e può implicare semantica esistente del flusso di controllo del linguaggio, ad esempio istruzioni condizionali e cicli. È possibile includere la logica di gestione degli errori nei in blocchi `try`/`catch`/`finally`.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
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

È possibile usare il parametro `context` per richiamare altre funzioni tramite il nome, i parametri passati e l'output restituito dalla funzione. Ogni volta che il codice chiama `await`, il framework di Durable Functions imposta checkpoint sullo stato di avanzamento dell'istanza della funzione corrente. Se la VM o il processo viene riciclato durante l'esecuzione, l'istanza della funzione riprende dalla chiamata `await` precedente. Per altre informazioni, vedere la sezione seguente, Modello 2: Fan-out/fan-in.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

È possibile usare l'oggetto `context.df` per richiamare altre funzioni tramite il nome, i parametri passati e l'output restituito dalla funzione. Ogni volta che il codice chiama `yield`, il framework di Durable Functions imposta checkpoint sullo stato di avanzamento dell'istanza della funzione corrente. Se la VM o il processo viene riciclato durante l'esecuzione, l'istanza della funzione riprende dalla chiamata `yield` precedente. Per altre informazioni, vedere la sezione seguente, Modello 2: Fan-out/fan-in.

> [!NOTE]
> L'oggetto `context` in JavaScript rappresenta l'intero [contesto della funzione](../functions-reference-node.md#context-object). Accedere al contesto di Durable Functions usando la proprietà `df` nel contesto principale.

---

### <a name="fan-in-out"></a>Modello 2: Fan-out/fan-in

Nel modello di fan-out/fan-in vengono eseguite più funzioni in parallelo e poi si resta in attesa che vengano completate tutte. Spesso sui risultati restituiti dalle funzioni vengono eseguite alcune operazioni di aggregazione.

![Diagramma del modello di fan-out/fan-in](./media/durable-functions-concepts/fan-out-fan-in.png)

Con le normali funzioni il fan-out può essere effettuato facendo in modo che la funzione invii più messaggi a una coda. L'operazione di fan-in è molto più complessa. In una normale funzione si scrive codice per rilevare il completamento delle funzioni attivate dalla coda e quindi archiviarne l'output.

L'estensione Durable Functions gestisce questo modello con codice relativamente semplice:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
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

L'operazione di fan-out viene distribuita a più istanze della funzione `F2` e viene monitorata tramite un elenco dinamico di attività. Viene effettuata una chiamata a `Task.WhenAll` per attendere il completamento di tutte le funzioni chiamate. Quindi, gli output della funzione `F2` vengono aggregati dall'elenco dinamico di attività e passati alla funzione `F3`.

L'impostazione automatica di checkpoint che avviene alla chiamata di `await` su `Task.WhenAll` assicura che qualsiasi potenziale riavvio o arresto anomalo del sistema durante l'esecuzione non richieda il riavvio di un'attività già completata.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

L'operazione di fan-out viene distribuita a più istanze della funzione `F2` e viene monitorata tramite un elenco dinamico di attività. Viene chiamata l'API `context.df.Task.all` per attendere il completamento di tutte le funzioni chiamate. Quindi, gli output della funzione `F2` vengono aggregati dall'elenco dinamico di attività e passati alla funzione `F3`.

L'impostazione automatica di checkpoint che avviene alla chiamata di `yield` su `context.df.Task.all` assicura che qualsiasi potenziale riavvio o arresto anomalo del sistema durante l'esecuzione non richieda il riavvio di un'attività già completata.

---

> [!NOTE]
> In rari casi, è possibile che si verifichi un arresto anomalo nella finestra dopo che una funzione di attività è stata completata, ma prima che il completamento venga salvato nella cronologia dell'orchestrazione. In tal caso, la funzione di attività verrebbe rieseguita dall'inizio dopo il ripristino del processo.

### <a name="async-http"></a>Modello 3: API HTTP asincrone

Il modello di API HTTP asincrone riguarda il problema di coordinare lo stato delle operazioni a esecuzione prolungata con client esterni. Un modo comune per implementare questo modello consiste nell'impostare un endpoint HTTP che attiva l'azione a esecuzione prolungata. Quindi, il client viene reindirizzato a un endpoint di stato di cui esegue il polling per rilevare quando viene completata l'operazione.

![Diagramma del modello di API HTTP](./media/durable-functions-concepts/async-http-api.png)

Durable Functions offre **supporto incorporato** per questo modello, semplificando o anche rimuovendo il codice che è necessario scrivere per interagire con le funzioni a esecuzione prolungata. Gli esempi dell'avvio rapido di Durable Functions ([C#](durable-functions-create-first-csharp.md), [JavaScript](quickstart-js-vscode.md)) mostrano un semplice comando REST che può essere usato per avviare nuove istanze della funzione dell'agente di orchestrazione. Dopo l'avvio di un'istanza, l'estensione espone API HTTP webhook che eseguono query sullo stato della funzione di orchestrazione. 

L'esempio seguente mostra i comandi REST per avviare un agente di orchestrazione e per eseguire query sul relativo stato. Per maggiore chiarezza, alcuni dettagli sono stati omessi dall'esempio.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Poiché lo stato è gestito automaticamente dal runtime di Durable Functions, non è necessario implementare un meccanismo personalizzato di monitoraggio.

L'estensione Durable Functions espone le API HTTP predefinite che gestiscono le orchestrazioni a esecuzione prolungata. In alternativa, è possibile implementare questo modello usando trigger di funzione personalizzati (ad esempio HTTP, una coda o Hub eventi di Azure) e il [binding del client di orchestrazione](durable-functions-bindings.md#orchestration-client). Ad esempio, è possibile usare un messaggio di coda per attivare la terminazione. In alternativa, è possibile usare un trigger HTTP protetto da un criterio di autenticazione di Azure Active Directory invece delle API HTTP predefinite che per l'autenticazione usano una chiave generata.

Per altre informazioni, vedere l'articolo sulle [funzionalità HTTP](durable-functions-http-features.md), che spiega come esporre i processi asincroni a esecuzione prolungata su HTTP usando l'estensione Durable Functions.

### <a name="monitoring"></a>Modello 4: Monitorare

Il modello di monitoraggio si riferisce a un processo flessibile e ricorrente in un flusso di lavoro. Un esempio è il polling eseguito finché non vengono soddisfatte determinate condizioni. È possibile usare un normale [trigger timer](../functions-bindings-timer.md) per gestire un semplice scenario, ad esempio un processo di pulizia periodico, ma l'intervallo è statico e la gestione delle durate delle istanze diventa complessa. È possibile usare Durable Functions per creare intervalli di ricorrenza flessibili, gestire le durate delle attività e creare più processi di monitoraggio da una singola orchestrazione.

Un esempio di modello di monitoraggio consiste nell'invertire lo scenario precedente delle API HTTP asincrone. Invece di esporre un endpoint per un client esterno per monitorare un'operazione a esecuzione prolungata, il monitoraggio dell'esecuzione prolungata utilizza un endpoint esterno, in attesa di un cambiamento di stato.

![Diagramma del modello di monitoraggio](./media/durable-functions-concepts/monitor.png)

Con poche righe di codice, è possibile usare Durable Functions per creare più monitoraggi che osservano endpoint arbitrari. I monitoraggi possono terminare l'esecuzione quando viene soddisfatta una condizione oppure un'altra funzione può usare il cliente di orchestrazione durevole per terminarli. È possibile cambiare l'intervallo `wait` di un monitoraggio in base a una condizione specifica, ad esempio un backoff esponenziale. 

Il codice seguente implementa un monitoraggio di base:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

---

Quando viene ricevuta una richiesta, viene creata una nuova istanza di orchestrazione per tale ID di processo. L'istanza esegue il polling di uno stato fino a quando non viene soddisfatta una condizione e terminato il ciclo. Un timer durevole controlla l'intervallo di polling. Quindi, possono essere eseguite più operazioni oppure l'orchestrazione può terminare. Quando `nextCheck` supera `expiryTime`, il monitoraggio termina.

### <a name="human"></a>Modello 5: Interazione umana

Molti processi automatizzati comportano un'interazione umana di qualche tipo. Il problema in un processo automatizzato con interazione umana è che le persone non sono sempre disponibili e reattive quanto i servizi cloud. I processi automatizzati potrebbero consentire questa interazione tramite timeout e logica di compensazione.

Un processo di approvazione è un esempio di processo aziendale che implica interazione umana. Potrebbe essere richiesta l'approvazione di un manager per una nota spese che supera un determinato importo. Se il manager non approva la nota spese entro 72 ore (ad esempio perché è in ferie), viene avviato un processo di escalation per ottenere l'approvazione da parte di qualcun altro, magari il suo manager.

![Diagramma del modello di interazione umana](./media/durable-functions-concepts/approval.png)

È possibile implementare il modello in questo esempio usando una funzione dell'agente di orchestrazione. L'agente di orchestrazione usa un [timer durevole](durable-functions-timers.md) per richiedere l'approvazione. Se si verifica un timeout, l'agente di orchestrazione attiva un'escalation. L'agente di orchestrazione attende che si verifichi un [evento esterno](durable-functions-external-events.md), ad esempio una notifica generata tramite interazione umana.

Questi esempi creano un processo di approvazione per illustrare il modello di interazione umana:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
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
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Per creare il timer durevole, chiamare `context.CreateTimer`. La notifica viene ricevuta da `context.WaitForExternalEvent`. Viene quindi effettuata una chiamata a `Task.WhenAny` per stabilire se attivare l'escalation (si verifica prima il timeout) o elaborare l'approvazione (l'approvazione viene ricevuta prima del timeout).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Per creare il timer durevole, chiamare `context.df.createTimer`. La notifica viene ricevuta da `context.df.waitForExternalEvent`. Viene quindi effettuata una chiamata a `context.df.Task.any` per stabilire se attivare l'escalation (si verifica prima il timeout) o elaborare l'approvazione (l'approvazione viene ricevuta prima del timeout).

---

Un client esterno può recapitare la notifica degli eventi a una funzione dell'agente di orchestrazione in attesa usando le [API HTTP predefinite](durable-functions-http-api.md#raise-event):

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Un evento può essere generato anche usando il client di orchestrazione durevole di un'altra funzione:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

---

### <a name="aggregator"></a>Modello 6: Aggregatore (entità con stato)

Il sesto modello riguarda l'aggregazione in una singola *entità* indirizzabile dei dati degli eventi relativi a un periodo di tempo. In questo modello i dati aggregati possono provenire da più origini, possono essere recapitati in batch o possono essere distribuiti in periodi di tempo prolungati. L'aggregatore potrebbe dover intervenire sui dati degli eventi non appena arrivano e i client esterni potrebbero dover eseguire query sui dati aggregati.

![Diagramma dell'aggregatore](./media/durable-functions-concepts/aggregator.png)

Se si prova a implementare questo modello con normali funzioni senza stato, l'aspetto più difficile è che il controllo della concorrenza diventa un problema enorme. Non solo è necessario preoccuparsi di più thread che modificano contemporaneamente gli stessi dati, ma bisogna anche assicurarsi che l'aggregatore venga eseguito solo in una singola macchina virtuale alla volta.

È possibile usare [entità durevoli](durable-functions-entities.md) per implementare facilmente questo modello come una singola funzione.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Le entità durevoli possono anche essere modellate come classi in .NET. Questo modello può rivelarsi utile se l'elenco delle operazioni è fisso e diventa grande. L'esempio seguente è un'implementazione equivalente dell'entità `Counter` tramite classi e metodi .NET.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

I client possono accodare *operazioni* ("segnalazione") per una funzione di entità tramite il [binding del client di entità](durable-functions-bindings.md#entity-client).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> In .NET sono anche disponibili proxy generati dinamicamente per la segnalazione di entità in modo indipendente dai tipi. Oltre alla segnalazione, i client possono anche eseguire query per ottenere lo stato di una funzione di entità tramite [metodi indipendenti dai tipi](durable-functions-bindings.md#entity-client-usage) sul binding del client di orchestrazione.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

---

Le funzioni di entità sono disponibili in  [Durable Functions 2.0](durable-functions-versions.md) e versioni successive.

## <a name="the-technology"></a>La tecnologia

L'estensione Durable Functions è basata su [Durable Task Framework](https://github.com/Azure/durabletask), una libreria open source disponibile in GitHub usata per creare flussi di lavoro nel codice. Così come Funzioni di Azure è l'evoluzione serverless di Processi Web di Azure, Durable Functions è l'evoluzione serverless di Durable Task Framework. In Microsoft e in altre organizzazioni il sistema Durable Task Framework viene ampiamente usato per automatizzare i processi cruciali. È una scelta ideale per l'ambiente senza server di Funzioni di Azure.

## <a name="code-constraints"></a>Vincoli di codice

Per offrire garanzie di esecuzione affidabili e a esecuzione prolungata, le funzioni dell'agente di orchestrazione prevedono un set di regole di codifica che devono essere rispettate. Per altre informazioni, vedere l'articolo sui [vincoli di codice delle funzioni dell'agente di orchestrazione](durable-functions-code-constraints.md).

## <a name="billing"></a>Fatturazione

La fatturazione di Durable Functions è analoga a quella delle Funzioni di Azure. Per altre informazioni, vedere [Prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/). Quando si eseguono le funzioni dell'agente di orchestrazione nel [piano a consumo](../functions-scale.md#consumption-plan) di Funzioni di Azure, è necessario tenere presenti alcuni aspetti della fatturazione. Per altre informazioni, vedere l'articolo sulla [fatturazione di Durable Functions](durable-functions-billing.md).

## <a name="jump-right-in"></a>Per iniziare immediatamente

È possibile iniziare a usare Durable Functions in meno di 10 minuti completando una di queste esercitazioni introduttive specifiche del linguaggio:

* [C# con Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript con Visual Studio Code](quickstart-js-vscode.md)

In entrambe le guide introduttive, si crea e testa in locale una funzione durevole "hello world". Il codice della funzione verrà quindi pubblicato in Azure. La funzione creata orchestra e concatena le chiamate ad altre funzioni.

## <a name="learn-more"></a>Altre informazioni

Il video seguente illustra i vantaggi di Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Per una descrizione più approfondita di Durable Functions e della tecnologia sottostante, vedere il video seguente (è basato su .NET, ma i concetti si applicano anche ad altri linguaggi supportati):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Dal momento che Durable Functions è un'estensione avanzata per [Funzioni di Azure](../functions-overview.md) non è adatta a tutte le applicazioni. Per un confronto con altre tecnologie di orchestrazione di Azure, vedere [Confrontare Funzioni di Azure e App per la logica di Azure](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Tipi di funzioni e caratteristiche di Durable Functions](durable-functions-types-features-overview.md)
