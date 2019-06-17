---
title: Tipi di funzioni e funzionalità nell'estensione funzioni permanenti di funzioni di Azure
description: Informazioni sui tipi di funzioni e i ruoli che supportano la comunicazione a funzione in un'orchestrazione di funzioni permanenti di funzioni di Azure.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731117"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Tipi di funzioni durevoli e caratteristiche (funzioni di Azure)

Funzioni permanenti è un'estensione della [funzioni di Azure](../functions-overview.md). È possibile usare funzioni permanenti per l'orchestrazione con stato di esecuzione della funzione. Una funzione permanente è una soluzione che è costituita da diverse funzioni di Azure. Funzioni possono svolgono ruoli diversi in un'orchestrazione di funzioni permanenti. 

Questo articolo offre una panoramica dei tipi di funzioni che è possibile usare in un'orchestrazione di funzioni permanenti. L'articolo include alcuni modelli comuni che è possibile usare per la connessione funzioni. Scopri come funzioni durevoli può aiutarti a risolvere i problemi di sviluppo di app.

![Un'immagine che mostra i tipi di funzioni permanenti][1]  

## <a name="types-of-durable-functions"></a>Tipi di funzioni durevoli

È possibile usare tre tipi di funzione permanente in funzioni di Azure: client, orchestrator e attività.

### <a name="activity-functions"></a>Funzioni di attività

Funzioni di attività sono l'unità di lavoro in un'orchestrazione di funzioni durevoli di base. Funzioni di attività sono le funzioni e le attività che sono coordinate nel processo. Ad esempio, si potrebbe creare una funzione permanente per l'elaborazione di un ordine. Le attività implicano il controllo dell'inventario, ad addebitare il cliente e la creazione di una spedizione. Ogni attività sarebbe una funzione di attività. 

Le funzioni di attività non sono limitate in tipo di operazioni che è possibile eseguire in essi contenuti. È possibile scrivere una funzione di attività in una qualsiasi [linguaggio che le funzioni permanenti supportano](durable-functions-overview.md#language-support). Il framework di attività durevoli garantisce che ogni funzione di attività chiamata venga eseguita almeno una volta durante un'orchestrazione.

Usa un' [trigger di attività](durable-functions-bindings.md#activity-triggers) per attivare una funzione di attività. Funzioni .NET ricevono una [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) come parametro. È possibile anche associare il trigger a qualsiasi altro oggetto per passare gli input alla funzione. In JavaScript, è possibile accedere a un input tramite il `<activity trigger binding name>` proprietà il [ `context.bindings` oggetto](../functions-reference-node.md#bindings).

La funzione di attività possa anche restituire i valori per l'agente di orchestrazione. Se si inviano o restituisca un numero elevato di valori da una funzione di attività, è possibile usare [tuple o matrici](durable-functions-bindings.md#passing-multiple-parameters). È possibile attivare una funzione di attività solo da un'istanza di orchestrazione. Anche se una funzione di attività e un'altra funzione (ad esempio, una funzione attivata tramite HTTP) potrebbero condividere codice, ogni funzione può avere un solo trigger.

Per altre informazioni ed esempi, vedere [funzioni di attività](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funzioni dell'agente di orchestrazione

Le funzioni di orchestrazione viene descritto come vengono eseguite le azioni e l'ordine in cui vengono eseguite le azioni. Le funzioni di orchestrazione vengono descritte l'orchestrazione nel codice (C# o JavaScript) come illustrato nel [modelli di funzioni permanenti e concetti tecnici](durable-functions-concepts.md). Un'orchestrazione può avere molti tipi diversi di azioni, incluse [funzioni di attività](#activity-functions), [orchestrazioni secondarie](#sub-orchestrations), [in attesa di eventi esterni](#external-events)e [timer](#durable-timers). 

Una funzione di orchestrazione deve essere attivata da un [trigger di orchestrazione](durable-functions-bindings.md#orchestration-triggers).

Un agente di orchestrazione viene avviata da un [client orchestrator](#client-functions). È possibile attivare l'agente di orchestrazione da qualsiasi origine (HTTP, coda, flusso di eventi). Ogni istanza di un'orchestrazione ha un identificatore di istanza. L'identificatore dell'istanza può essere generati automaticamente (scelta consigliata) o generati dall'utente. È possibile usare l'identificatore dell'istanza di [gestire le istanze](durable-functions-instance-management.md) dell'orchestrazione.

Per altre informazioni ed esempi, vedere [trigger di orchestrazione](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Funzioni client

Le funzioni client sono le funzioni attivate che creano nuove istanze di un'orchestrazione. Le funzioni client sono il punto di ingresso per la creazione di un'istanza di un'orchestrazione di funzioni permanenti. È possibile attivare una funzione di client da qualsiasi origine (HTTP, coda, flusso di eventi). È possibile scrivere una funzione di client in qualsiasi lingua supportata dall'app. 

Le funzioni client hanno anche un [del client di orchestrazione](durable-functions-bindings.md#orchestration-client) associazione. Una funzione di client può usare il client di orchestrazione di associazione per creare e gestire le orchestrazioni durevole. 

L'esempio più semplice di una funzione di client è una funzione attivata tramite HTTP che si avvia una funzione di orchestrazione e quindi restituisce una risposta di stato di controllo. Per un esempio, vedere [rilevamento dell'URL di API HTTP](durable-functions-http-api.md#http-api-url-discovery).

Per altre informazioni ed esempi, vedere [del client di orchestrazione](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funzionalità e criteri

Le sezioni successive descrivono le funzionalità e i modelli di tipi di funzioni permanenti.

### <a name="sub-orchestrations"></a>Orchestrazioni secondarie

Le funzioni dell'agente di orchestrazione possono chiamare le funzioni di attività, ma possono anche chiamare altre funzioni dell'agente di orchestrazione. È possibile ad esempio compilare un'orchestrazione più grande da una raccolta di funzioni dell'agente di orchestrazione. In alternativa, è possibile eseguire più istanze di una funzione di orchestrazione in parallelo.

Per altre informazioni ed esempi, vedere [orchestrazioni secondarie](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Timer durevoli

[Funzioni permanenti](durable-functions-overview.md) fornisce *timer durevoli* che è possibile usare nelle funzioni di orchestrazione per implementare ritardi o per impostare i timeout nelle azioni asincrone. Usare i timer permanenti nelle funzioni di orchestrazione anziché `Thread.Sleep` e `Task.Delay` (C#) o `setTimeout()` e `setInterval()` (JavaScript).

Per altre informazioni ed esempi, vedere [timer permanenti](durable-functions-timers.md).

### <a name="external-events"></a>Eventi esterni

Le funzioni di orchestrazione possono attendere eventi esterni per aggiornare un'istanza di orchestrazione. Questa funzionalità di funzioni permanenti è spesso utile per la gestione di un'interazione umana o altri callback esterno.

Per altre informazioni ed esempi, vedere [eventi esterni](durable-functions-external-events.md).

### <a name="error-handling"></a>Gestione degli errori

Usare codice per implementare le orchestrazioni di funzioni permanenti. È possibile usare le funzionalità di gestione degli errori del linguaggio di programmazione. Modelli, ad esempio `try` / `catch` funzionano nell'orchestrazione. 

Includono anche funzioni permanenti con i criteri di ripetizione dei tentativi incorporato. Un'azione possibile ritardare e ripetere le attività automaticamente quando si verifica un'eccezione. È possibile usare i tentativi per gestire le eccezioni temporanee senza abbandonare l'orchestrazione.

Per altre informazioni ed esempi, vedere [gestione degli errori](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Comunicazione tra app per le funzioni

Anche se un'orchestrazione permanente viene eseguito nel contesto di un'app a singola funzione, è possibile utilizzare modelli per coordinare le orchestrazioni in molte App per le funzioni. Comunicazione tra app potrebbe avere luogo tramite HTTP, ma usa il framework permanenti per ogni attività, che è comunque possibile mantenere un processo durevole tra due app.

Gli esempi seguenti illustrano app cross-funzioni di orchestrazione in C# e JavaScript. In ogni esempio, un'attività avvia l'orchestrazione esterna. Un'altra attività recupera e restituisce lo stato. L'agente di orchestrazione attende che lo stato sia `Complete` prima di continuare.

Di seguito sono riportati alcuni esempi di orchestrazione di app cross-funzione:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, creare la prima funzione permanente nel [ C# ](durable-functions-create-first-csharp.md) oppure [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Per ulteriori informazioni su funzioni permanenti](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
