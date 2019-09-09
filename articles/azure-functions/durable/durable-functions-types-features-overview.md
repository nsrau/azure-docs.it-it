---
title: Tipi di funzione e funzionalità nell'estensione Durable Functions di funzioni di Azure
description: Informazioni sui tipi di funzioni e ruoli che supportano la comunicazione da funzione a funzione in un'orchestrazione Durable Functions in funzioni di Azure.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097749"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Tipi e funzionalità di Durable Functions (funzioni di Azure)

Durable Functions è un'estensione di [funzioni di Azure](../functions-overview.md). È possibile utilizzare Durable Functions per l'orchestrazione con stato dell'esecuzione della funzione. Una funzione durevole è una soluzione costituita da funzioni di Azure diverse. Le funzioni possono svolgere ruoli diversi in un'orchestrazione di funzioni permanenti. 

Questo articolo offre una panoramica dei tipi di funzioni che è possibile usare in un'orchestrazione Durable Functions. L'articolo include alcuni modelli comuni che è possibile usare per la connessione di funzioni. Scopri in che modo Durable Functions ti aiuta a risolvere le tue esigenze di sviluppo di app.

![Immagine che mostra i tipi di funzioni permanenti][1]  

## <a name="types-of-durable-functions"></a>Tipi di funzioni durevoli

È possibile usare quattro tipi di funzioni permanenti in funzioni di Azure: attività, agente di orchestrazione, entità e client.

### <a name="activity-functions"></a>Funzioni di attività

Le funzioni di attività sono l'unità di base di lavoro in un'orchestrazione di funzioni permanenti. Le funzioni di attività sono le funzioni e le attività orchestrate nel processo. Ad esempio, è possibile creare una funzione durevole per elaborare un ordine. Le attività comportano il controllo dell'inventario, l'addebito del cliente e la creazione di una spedizione. Ogni attività sarebbe una funzione di attività. 

Le funzioni di attività non sono limitate nel tipo di lavoro che è possibile eseguire in essi. È possibile scrivere una funzione di attività in qualsiasi [linguaggio che Durable Functions supporto](durable-functions-overview.md#language-support). Il framework di attività durevoli garantisce che ogni funzione di attività chiamata venga eseguita almeno una volta durante un'orchestrazione.

Usare un [trigger di attività](durable-functions-bindings.md#activity-triggers) per attivare una funzione di attività. Le funzioni .NET ricevono un [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) come parametro. È possibile anche associare il trigger a qualsiasi altro oggetto per passare gli input alla funzione. In JavaScript, è possibile accedere a un input tramite `<activity trigger binding name>` la proprietà [ `context.bindings` nell'oggetto](../functions-reference-node.md#bindings).

La funzione Activity può inoltre restituire valori all'agente di orchestrazione. Se si invia o si restituisce un numero elevato di valori da una funzione di attività, è possibile utilizzare [Tuple o matrici](durable-functions-bindings.md#passing-multiple-parameters). È possibile attivare una funzione attività solo da un'istanza di orchestrazione. Sebbene una funzione di attività e un'altra funzione, ad esempio una funzione attivata tramite HTTP, possano condividere codice, ogni funzione può avere un solo trigger.

Per altre informazioni ed esempi, vedere [funzioni di attività](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funzioni dell'agente di orchestrazione

Le funzioni dell'agente di orchestrazione descrivono come vengono eseguite le azioni e l'ordine in cui vengono eseguite le azioni. Le funzioni dell'agente di orchestrazione descrivono l'orchestrazione nel codice (C# o JavaScript), come illustrato in [Durable Functions modelli e concetti tecnici](durable-functions-concepts.md). Un'orchestrazione può avere molti tipi diversi di azioni, incluse le [funzioni di attività](#activity-functions), le [sottoorchestrazioni](#sub-orchestrations), l' [attesa di eventi esterni e i](#external-events) [timer](#durable-timers). Le funzioni dell'agente di orchestrazione possono interagire anche con le [funzioni dell'entità](#entity-functions).

Una funzione di orchestrazione deve essere attivata da un [trigger di orchestrazione](durable-functions-bindings.md#orchestration-triggers).

Un agente di orchestrazione viene avviato da un client di agente di [orchestrazione](#client-functions). È possibile attivare l'agente di orchestrazione da qualsiasi origine (HTTP, coda, flusso di eventi). Ogni istanza di un'orchestrazione dispone di un identificatore di istanza. L'identificatore dell'istanza può essere generato automaticamente (scelta consigliata) o generata dall'utente. È possibile utilizzare l'identificatore di istanza per [gestire le istanze](durable-functions-instance-management.md) dell'orchestrazione.

Per ulteriori informazioni e per esempi, vedere [trigger di orchestrazione](durable-functions-bindings.md#orchestration-triggers).

###  <a name="entity-functions"></a>Funzioni di entità (anteprima)

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccoli elementi di stato, noti come *entità durevoli*. Come le funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, *trigger di entità*. Diversamente dalle funzioni dell'agente di orchestrazione, le funzioni di entità non hanno vincoli di codice specifici. Anche le funzioni di entità gestiscono lo stato in modo esplicito anziché rappresentare in modo implicito lo stato tramite flusso di controllo.

> [!NOTE]
> Le funzioni di entità e le funzionalità correlate sono disponibili solo in Durable Functions 2,0 e versioni successive.

Per ulteriori informazioni sulle funzioni di entità, vedere la documentazione della funzionalità di anteprima delle [funzioni di entità](durable-functions-preview.md#entity-functions) .

### <a name="client-functions"></a>Funzioni client

Le funzioni client sono funzioni attivate per la creazione e la gestione di istanze di orchestrazioni ed entità. Sono effettivamente il punto di ingresso per interagire con Durable Functions. È possibile attivare una funzione client da qualsiasi origine (HTTP, coda, flusso di eventi e così via). Una funzione client usa l' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client) per creare e gestire le orchestrazioni e le entità durevoli.

L'esempio più semplice di una funzione client è una funzione attivata da HTTP che avvia una funzione dell'agente di orchestrazione e quindi restituisce una risposta di stato del controllo. Per un esempio, vedere [individuazione dell'URL dell'API HTTP](durable-functions-http-api.md#http-api-url-discovery).

Per ulteriori informazioni e per esempi, vedere [Orchestration client](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funzionalità e criteri

Nelle sezioni successive vengono descritte le funzionalità e i modelli dei tipi di Durable Functions.

### <a name="sub-orchestrations"></a>Orchestrazioni secondarie

Le funzioni di orchestrazione possono chiamare le funzioni di attività, ma possono anche chiamare altre funzioni dell'agente di orchestrazione. È possibile ad esempio compilare un'orchestrazione più grande da una raccolta di funzioni dell'agente di orchestrazione. In alternativa, è possibile eseguire più istanze di una funzione dell'agente di orchestrazione in parallelo.

Per ulteriori informazioni e per esempi, vedere [orchestrazioni secondarie](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Timer durevoli

[Durable Functions](durable-functions-overview.md) fornisce *timer durevoli* che è possibile utilizzare nelle funzioni dell'agente di orchestrazione per implementare ritardi o impostare timeout per le azioni asincrone. Usare i timer durevoli nelle funzioni dell' `Thread.Sleep` agente diC#orchestrazione `setTimeout()` anziché `setInterval()` e `Task.Delay` () o e (JavaScript).

Per ulteriori informazioni ed esempi, vedere la pagina relativa ai [timer durevoli](durable-functions-timers.md).

### <a name="external-events"></a>Eventi esterni

Le funzioni di orchestrazione possono attendere eventi esterni per aggiornare un'istanza di orchestrazione. Questa funzionalità Durable Functions spesso è utile per la gestione di un'interazione umana o di altri callback esterni.

Per ulteriori informazioni ed esempi, vedere [eventi esterni](durable-functions-external-events.md).

### <a name="error-handling"></a>Gestione degli errori

Utilizzare il codice per implementare le orchestrazioni Durable Functions. È possibile utilizzare le funzionalità di gestione degli errori del linguaggio di programmazione. Modelli come `try` / illavoronell'orchestrazione`catch` . 

Durable Functions inoltre sono disponibili criteri di ripetizione dei tentativi predefiniti. Un'azione può ritardare e ripetere automaticamente le attività quando si verifica un'eccezione. È possibile utilizzare i tentativi per gestire le eccezioni temporanee senza abbandonare l'orchestrazione.

Per ulteriori informazioni e per esempi, vedere [gestione degli errori](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Comunicazione tra app per le funzioni

Anche se un'orchestrazione durevole viene eseguita nel contesto di una singola app per le funzioni, è possibile usare i modelli per coordinare le orchestrazioni in molte app per le funzioni. La comunicazione tra app potrebbe verificarsi su HTTP, ma l'uso del Framework durevole per ogni attività significa che è ancora possibile mantenere un processo durevole tra due app.

Negli esempi seguenti viene illustrata l'orchestrazione di C# app tra le funzioni in e JavaScript. In ogni esempio, un'attività avvia l'orchestrazione esterna. Un'altra attività recupera e restituisce lo stato. L'agente di orchestrazione attende che lo stato `Complete` sia prima di continuare.

Di seguito sono riportati alcuni esempi di orchestrazione di app tra funzioni:

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

Per iniziare, creare la prima funzione durevole in [C#](durable-functions-create-first-csharp.md) o [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Scopri di più su Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
