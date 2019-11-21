---
title: Controllo delle versioni in Funzioni permanenti - Azure
description: Informazioni su come implementare il controllo delle versioni nell'estensione Funzioni permanenti per Funzioni di Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232748"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Controllo delle versioni in Funzioni permanenti (Funzioni di Azure)

Nella durata di un'applicazione è comune che vengano aggiunte, rimosse e modificate funzioni. [Funzioni permanenti](durable-functions-overview.md) consente di concatenare diverse funzioni in modi non possibili in precedenza, influenzando in tal modo la gestione del controllo delle versioni.

## <a name="how-to-handle-breaking-changes"></a>Come gestire le modifiche di rilievo

Sono disponibili alcuni esempi di modifiche di rilievo che è necessario tenere presenti. Questo articolo illustra i più comuni, basati tutti sul fatto che tutte le funzioni di orchestrazione, sia nuove che esistenti, sono interessate dalle modifiche apportate al codice di funzione.

### <a name="changing-activity-or-entity-function-signatures"></a>Changing activity or entity function signatures

Una modifica della firma fa riferimento a una modifica di nome, input oppure output di una funzione. If this kind of change is made to an activity or entity function, it could break any orchestrator function that depends on it. Se si aggiorna la funzione dell'agente di orchestrazione per gestire questa modifica, si potrebbero interrompere le istanze esistenti in corso.

As an example, suppose we have the following orchestrator function.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Questa semplice funzione usa i risultati di **Foo** e li passa a **Bar**. Si supponga che sia necessario modificare il valore restituito di **Foo** da `bool` a `int` per supportare un'ampia gamma di valori di risultati. Il risultato sarà analogo a questo:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

Questa modifica funziona correttamente per tutte le nuove istanze della funzione dell'agente di orchestrazione, ma interrompe tutte le istanze in corso. For example, consider the case where an orchestration instance calls a function named `Foo`, gets back a boolean value, and then checkpoints. Se la modifica della firma viene distribuita a questo punto, l'istanza per cui è stato applicato il checkpoint ha immediatamente esito negativo quando riprende e riesegue la chiamata a `context.CallActivityAsync<int>("Foo")`. This failure happens because the result in the history table is `bool` but the new code tries to deserialize it into `int`.

This example is just one of many different ways that a signature change can break existing instances. In generale, se è necessario modificare il modo in cui un agente di orchestrazione chiama una funzione, la modifica può diventare un problema.

### <a name="changing-orchestrator-logic"></a>Modifica della logica dell'agente di orchestrazione

L'altra classe di problemi di controllo delle versioni deriva dalla modifica del codice di funzione dell'agente di orchestrazione in un modo che disorienta la logica di riesecuzione per le istanze in corso.

Si consideri la funzione dell'agente di orchestrazione seguente:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Si supponga ora che si desideri apportare una modifica apparentemente innocua per aggiungere un'altra chiamata di funzione.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

Questa modifica aggiunge una chiamata di funzione a **SendNotification** tra **Foo** e **Bar**. Non sono presenti modifiche della firma. Il problema si verifica quando un'istanza esistente riprende dopo la chiamata a **Bar**. During replay, if the original call to **Foo** returned `true`, then the orchestrator replay will call into **SendNotification**, which is not in its execution history. Di conseguenza, il framework di attività permanenti ha esito negativo e genera un'eccezione `NonDeterministicOrchestrationException` perché ha rilevato una chiamata a **SendNotification** quando era prevista la visualizzazione di una chiamata a **Bar**. The same type of problem can occur when adding any calls to "durable" APIs, including `CreateTimer`, `WaitForExternalEvent`, etc.

## <a name="mitigation-strategies"></a>Strategie di mitigazione

Di seguito vengono indicate alcune strategie per affrontare le problematiche di controllo delle versioni.

* Non eseguire alcuna operazione
* Arrestare tutte le istanze in corso
* Eseguire distribuzioni side-by-side

### <a name="do-nothing"></a>Non eseguire alcuna operazione

Il modo più semplice per gestire una modifica di rilievo è quello di consentire che le istanze di orchestrazione in corso abbiano esito negativo. Le nuove istanze eseguono correttamente il codice modificato.

Whether this kind of failure is a problem depends on the importance of your in-flight instances. In fase di sviluppo attivo e quando la presenza di istanze in corso non è importante, questa situazione potrebbe essere corretta. However, you'll need to deal with exceptions and errors in your diagnostics pipeline. Se si desidera evitare queste situazioni, prendere in considerazione le altre opzioni di controllo delle versioni.

### <a name="stop-all-in-flight-instances"></a>Arrestare tutte le istanze in corso

Un'altra opzione è quella di arrestare tutte le istanze in corso. Stopping all instances can be done by clearing the contents of the internal **control-queue** and **workitem-queue** queues. The instances will be forever stuck where they are, but they will not clutter your logs with failure messages. This approach is ideal in rapid prototype development.

> [!WARNING]
> I dettagli di queste code possono cambiare nel tempo, pertanto non è consigliabile basarsi su questa tecnica per i carichi di lavoro di produzione.

### <a name="side-by-side-deployments"></a>Eseguire distribuzioni side-by-side

Il modo migliore per garantire che le modifiche di rilievo vengano distribuite in modo sicuro è quello di eseguire una distribuzione side-by-side con le versioni precedenti. Questa operazione può essere eseguita tramite una delle tecniche seguenti:

* Deploy all the updates as entirely new functions, leaving existing functions as-is. This can be tricky because the callers of the new function versions must be updated as well following the same guidelines.
* Distribuire tutti gli aggiornamenti come una nuova app per le funzioni con un account di archiviazione diverso.
* Deploy a new copy of the function app with the same storage account but with an updated `taskHub` name. Side-by-side deployments is the recommended technique.

### <a name="how-to-change-task-hub-name"></a>Come modificare il nome dell'hub attività

L'hub attività può essere configurato nel file *host.json* come indicato di seguito:

#### <a name="functions-1x"></a>Funzioni 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

The default value for Durable Functions v1.x is `DurableFunctionsHub`. Starting in Durable Functions v2.0, the default task hub name is the same as the function app name in Azure, or `TestHubName` if running outside of Azure.

Tutte le entità di Archiviazione di Azure sono denominate in base al valore di configurazione `hubName`. Se si assegna un nuovo nome all'hub attività, verificare che per la nuova versione dell'applicazione vengano create code separate e la tabella di cronologia. The function app, however, will stop processing events for orchestrations or entities created under the previous task hub name.

È consigliabile distribuire la nuova versione dell'app per le funzioni a un nuovo [slot di distribuzione](../functions-deployment-slots.md). Gli slot di distribuzione consentono di eseguire side-by-side più copie dell'app per le funzioni con un solo slot come slot di *produzione* attivo. Quando è possibile esporre la nuova logica di orchestrazione all'infrastruttura esistente, l'operazione può essere semplice come la sostituzione della nuova versione nello slot di produzione.

> [!NOTE]
> Questa strategia funziona meglio quando si usano trigger HTTP e di webhook per le funzioni dell'agente di orchestrazione. For non-HTTP triggers, such as queues or Event Hubs, the trigger definition should [derive from an app setting](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) that gets updated as part of the swap operation.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire i problemi di prestazioni e di scalabilità](durable-functions-perf-and-scale.md)
