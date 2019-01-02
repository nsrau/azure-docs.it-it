---
title: Panoramica dei tipi di funzioni e funzionalità per Durable Functions - Azure
description: Informazioni sui tipi di funzioni e i ruoli che consentono la comunicazione da funzione a funzione come parte di un'orchestrazione di funzioni durevoli.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 359594ab91b903033ecc303eccd270988be19810
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336528"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Panoramica dei tipi di funzioni e funzionalità per Durable Functions (Funzioni di Azure)

Durable Functions fornisce l'orchestrazione con stato dell'esecuzione di funzioni. Una funzione durevole è una soluzione costituita da diverse Funzioni di Azure. Ognuna di queste funzioni può svolgere ruoli diversi come parte di un'orchestrazione. Il documento seguente fornisce una panoramica dei tipi di funzioni usate in un'orchestrazione di funzioni durevoli. Include anche alcuni criteri comuni nella connessione tra le funzioni.  Per iniziare subito, creare la prima funzione durevole in [C#](durable-functions-create-first-csharp.md) o in [JavaScript](quickstart-js-vscode.md).

![Tipi di funzioni durevoli][1]  

## <a name="types-of-functions"></a>Tipi di funzioni

### <a name="activity-functions"></a>Funzioni di attività

Le funzioni di attività sono l'unità di base del lavoro in un'orchestrazione durevole.  Le funzioni di attività sono le funzioni e le attività che vengono orchestrate nel processo.  Ad esempio, è possibile creare una funzione durevole per elaborare un ordine: verificare l'inventario, applicare un addebito al cliente e creare una spedizione.  Ognuna di queste attività è una funzione di attività.  Le funzioni di attività non presentano restrizioni nel tipo di operazioni che è possibile eseguire in esse.  Possono essere scritte in qualsiasi [linguaggio supportato da Durable Functions](durable-functions-overview.md#language-support). Durable Task Framework garantisce che ogni funzione di attività chiamata venga eseguita almeno una volta durante un'orchestrazione.

Una funzione di attività deve essere attivata da un [trigger di attività](durable-functions-bindings.md#activity-triggers).  Le funzioni riceveranno un [DurableActivityContext ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) come parametro. È possibile anche associare il trigger a qualsiasi altro oggetto per passare gli input alla funzione. In JavaScript, input sono accessibili tramite la proprietà `<activity trigger binding name>` dell'[oggetto `context.bindings`](../functions-reference-node.md#bindings).

La funzione di attività può anche restituire i valori all'agente di orchestrazione.  Se si inviano o restituiscono numerosi valori da una funzione di attività, è possibile [sfruttare le tuple o le matrici](durable-functions-bindings.md#passing-multiple-parameters).  Le funzioni di attività possono essere attivate solo da un'istanza di orchestrazione.  Sebbene una parte del codice possa essere condivisa tra una funzione di attività e un'altra funzione, ad esempio una funzione attivata tramite HTTP, ogni funzione può avere un solo trigger.

Altre informazioni ed esempi sono disponibili nell'[articolo sul binding di Durable Functions](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funzioni dell'agente di orchestrazione

Le funzioni dell'agente di orchestrazione sono il fulcro di una funzione durevole  e descrivono il modo e l'ordine con cui vengono eseguite le azioni.  Le funzioni dell'agente di orchestrazione descrivono l'orchestrazione nel codice (C# o JavaScript) come illustrato nella [panoramica delle funzioni durevoli](durable-functions-overview.md).  Un'orchestrazione può avere molti tipi diversi di azioni, ad esempio [funzioni di attività](#activity-functions), [orchestrazioni secondarie](#sub-orchestrations), [attesa di eventi esterni](#external-events) e [timer](#durable-timers).  

Una funzione di orchestrazione deve essere attivata da un [trigger di orchestrazione](durable-functions-bindings.md#orchestration-triggers).

Un agente di orchestrazione viene avviato da un [client di orchestrazione](#client-functions) che può a sua volta essere attivato da qualsiasi origine (HTTP, code, flussi di eventi).  Ogni istanza di un'orchestrazione dispone di un identificatore di istanza, che può essere generato automaticamente (scelta consigliata) o dall'utente.  Questo identificatore può essere usato per [gestire le istanze](durable-functions-instance-management.md) dell'orchestrazione.

Altre informazioni ed esempi sono disponibili nell'[articolo sul binding di Durable Functions](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Funzioni client

Le funzioni client sono le funzioni attivate che creeranno nuove istanze di un'orchestrazione.  Sono il punto di ingresso per creare un'istanza di un'orchestrazione durevole.  Le funzioni client possono essere attivate da qualsiasi trigger (HTTP, code, flussi di eventi e così via) e scritte in qualsiasi linguaggio supportato dall'app.  Oltre al trigger, le funzioni client dispongono di un binding del [client di orchestrazione ](durable-functions-bindings.md#orchestration-client) che consente di creare e gestire orchestrazioni durevoli.  L'esempio più semplice di una funzione client è una funzione attivata tramite HTTP che avvia una funzione di orchestrazione e restituisce una risposta di controllo dello stato come [illustrato nell'esempio seguente](durable-functions-http-api.md#http-api-url-discovery).

Altre informazioni ed esempi sono disponibili nell'[articolo sul binding di Durable Functions](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funzionalità e criteri

### <a name="sub-orchestrations"></a>Orchestrazioni secondarie

Oltre a chiamare le funzioni di attività, le funzioni dell'agente di orchestrazione possono chiamare altre funzioni dell'agente di orchestrazione. È possibile ad esempio compilare un'orchestrazione più grande da una raccolta di funzioni dell'agente di orchestrazione. Oppure è possibile eseguire più istanze di una funzione dell'agente di orchestrazione in parallelo.

Altre informazioni ed esempi sono disponibili nell'[articolo sulle orchestrazioni secondarie](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Timer durevoli

[Funzioni permanenti](durable-functions-overview.md) fornisce *timer permanenti* da usare nelle funzioni di orchestrazione per implementare ritardi o per impostare timeout nelle azioni asincrone. I timer permanenti devono essere usati nelle funzioni dell'agente di orchestrazione al posto di `Thread.Sleep` e `Task.Delay` (C#) oppure di `setTimeout()` e `setInterval()` (JavaScript).

Altre informazioni ed esempi di timer durevoli sono disponibili nell'[articolo sui timer durevoli](durable-functions-timers.md).

### <a name="external-events"></a>Eventi esterni

Le funzioni di orchestrazione possono attendere eventi esterni per aggiornare un'istanza di orchestrazione. Questa funzionalità di Durable Functions è spesso utile per gestire l'interazione umana o altri callback esterni.

Altre informazioni ed esempi sono disponibili nell'[articolo sugli eventi esterni](durable-functions-external-events.md).

### <a name="error-handling"></a>Gestione degli errori

Le orchestrazioni di Funzioni durevoli vengono implementate nel codice e possono usare le funzionalità di gestione degli errori del linguaggio di programmazione.  I criteri, come "try/catch", funzioneranno quindi nell'orchestrazione.  Durable Functions include anche alcuni criteri di ripetizione di tentativi predefiniti.  Un'azione può ritardare e riprovare automaticamente le attività in caso di eccezioni.  I tentativi consentono di gestire le eccezioni temporanee senza dover abbandonare l'orchestrazione.

Altre informazioni ed esempi sono disponibili nell'[articolo sulla gestione degli errori](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Comunicazione tra app per le funzioni

Sebbene un'orchestrazione durevole si trovi in genere all'interno del contesto di una singola app per le funzioni, esistono criteri che consentono di coordinare le orchestrazioni in più app per le funzioni.  Anche se la comunicazione tra app può avvenire tramite HTTP, usando il framework durevole per ogni attività significa che è possibile mantenere un processo durevole tra due app.

Di seguito sono disponibili esempi di orchestrazione tra app per le funzioni in C# e JavaScript.  Un'attività avvierà l'orchestrazione esterna. Un'altra attività recupererà e restituirà lo stato.  L'agente di orchestrazione attenderà che lo stato sia completo prima di continuare.

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
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

    // Call a remote orchestration
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now go do more work...
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

> [!div class="nextstepaction"]
> [Continuare a leggere la documentazione su Funzioni permanenti](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
