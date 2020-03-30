---
title: Vincoli del codice dell'agente di orchestrazione durevole - Funzioni di AzureDurable orchestrator code constraints - Azure Functions
description: Riproduzione delle funzioni di orchestrazione e vincoli di codice per Funzioni durevoli di Azure.Orchestration function replay and code constraints for Azure Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562140"
---
# <a name="orchestrator-function-code-constraints"></a>Vincoli del codice della funzione Orchestrator

Funzioni durevoli è un'estensione di Funzioni di [Azure](../functions-overview.md) che consente di creare app con stato. È possibile usare una [funzione dell'agente](durable-functions-orchestrations.md) di orchestrazione per orchestrare l'esecuzione di altre funzioni durevoli all'interno di un'app per le funzioni. Le funzioni dell'agente di orchestrazione sono con stato, affidabili e potenzialmente a esecuzione prolungata.

## <a name="orchestrator-code-constraints"></a>Vincoli del codice dell'agente di orchestrazione

Le funzioni dell'agente di [orchestrazione](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) usano l'origine eventi per garantire un'esecuzione affidabile e mantenere lo stato delle variabili locali. Il comportamento di [riproduzione](durable-functions-orchestrations.md#reliability) del codice dell'agente di orchestrazione crea vincoli sul tipo di codice che è possibile scrivere in una funzione dell'agente di orchestrazione. Ad esempio, le funzioni dell'agente di orchestrazione devono essere *deterministiche:* una funzione dell'agente di orchestrazione verrà riprodotta più volte e deve produrre lo stesso risultato ogni volta.

### <a name="using-deterministic-apis"></a>Utilizzo di API deterministiche

In questa sezione vengono fornite alcune semplici linee guida che consentono di garantire che il codice sia deterministico.

Le funzioni dell'agente di orchestrazione possono chiamare qualsiasi API nelle lingue di destinazione. Tuttavia, è importante che le funzioni dell'agente di orchestrazione chiamino solo API deterministiche. *Un'API deterministica* è un'API che restituisce sempre lo stesso valore dato lo stesso input, indipendentemente da quando o con quale frequenza viene chiamata.

Nella tabella seguente vengono illustrati esempi di API che è consigliabile evitare perché *non* sono deterministiche. Queste restrizioni si applicano solo alle funzioni dell'agente di orchestrazione. Altri tipi di funzione non hanno tali restrizioni.

| Categoria API | Motivo | Soluzione alternativa |
| ------------ | ------ | ---------- |
| Date e ore  | Le API che restituiscono la data o l'ora corrente non sono deterministiche perché il valore restituito è diverso per ogni riproduzione. | Usa`CurrentUtcDateTime` l'API in `currentUtcDateTime` .NET o l'API in JavaScript, che sono sicuri per la riproduzione. |
| GUID e UUID  | Le API che restituiscono un GUID casuale o UUID non sono deterministiche perché il valore generato è diverso per ogni riproduzione. | Utilizzare `NewGuid` in .NET o `newGuid` JavaScript per generare in modo sicuro GUID casuali. |
| Numeri casuali | Le API che restituiscono numeri casuali non sono deterministiche perché il valore generato è diverso per ogni riproduzione. | Usare una funzione di attività per restituire numeri casuali a un'orchestrazione. I valori restituiti delle funzioni di attività sono sempre sicuri per la riproduzione. |
| Associazioni | Le associazioni di input e output in genere eseguono i/o e non sono deterministiche. Una funzione dell'agente di orchestrazione non deve utilizzare direttamente nemmeno le associazioni [client dell'orchestrazione](durable-functions-bindings.md#orchestration-client) e [dell'entità.](durable-functions-bindings.md#entity-client) | Usare associazioni di input e output all'interno di funzioni client o attività. |
| Rete | Le chiamate di rete coinvolgono sistemi esterni e non sono deterministiche. | Utilizzare le funzioni di attività per effettuare chiamate di rete. Se è necessario effettuare una chiamata HTTP dalla funzione dell'agente di orchestrazione, è anche possibile utilizzare le [API HTTP durevoli.](durable-functions-http-features.md#consuming-http-apis) |
| Blocco delle API | Il blocco di `Thread.Sleep` API come in .NET e API simili può causare problemi di prestazioni e scalabilità per le funzioni dell'agente di orchestrazione e deve essere evitato. Nel piano Consumo funzioni di Azure possono anche comportare costi di runtime non necessari. | Usa alternative per bloccare le API quando sono disponibili. Ad esempio, `CreateTimer` utilizzare per introdurre ritardi nell'esecuzione dell'orchestrazione. I ritardi [del timer durevole](durable-functions-timers.md) non vengono conteggiati per il tempo di esecuzione di una funzione dell'agente di orchestrazione. |
| API asincrone | Il codice dell'agente di orchestrazione non `IDurableOrchestrationContext` deve `context.df` mai avviare alcuna operazione asincrona se non utilizzando l'API o l'API dell'oggetto. Ad esempio, non è `Task.Run` `Task.Delay`possibile `HttpClient.SendAsync` utilizzare , `setTimeout` , `setInterval` e in .NET o e in JavaScript. Il Framework attività durevole esegue il codice dell'agente di orchestrazione in un singolo thread. Non può interagire con altri thread che potrebbero essere chiamati da altre API asincrone. | Una funzione dell'agente di orchestrazione deve eseguire solo chiamate asincrone durevoli. Le funzioni di attività devono eseguire qualsiasi altra chiamata API asincrona. |
| Funzioni JavaScript asincroneAsync JavaScript functions | Non è possibile dichiarare funzioni dell'agente di orchestrazione JavaScript perché `async` il runtime node.js non garantisce che le funzioni asincrone siano deterministiche. | Dichiarare le funzioni dell'agente di orchestrazione JavaScript come funzioni di generazione sincrone. |
| API di threading | Il framework di attività durevole esegue il codice dell'agente di orchestrazione in un singolo thread e non può interagire con altri thread. L'introduzione di nuovi thread nell'esecuzione di un'orchestrazione può comportare un'esecuzione non deterministica o deadlock. | Le funzioni dell'agente di orchestrazione non devono quasi mai usare le API di threading. Ad esempio, in .NET, evitare di utilizzare `ConfigureAwait(continueOnCapturedContext: false)`; ciò garantisce che le continuazioni delle attività `SynchronizationContext`vengano eseguite nell'originale della funzione dell'agente di orchestrazione. Se tali API sono necessarie, limitarne l'utilizzo solo alle funzioni di attività. |
| Variabili statiche | Evitare di utilizzare variabili statiche non costanti nelle funzioni dell'agente di orchestrazione perché i relativi valori possono cambiare nel tempo, determinando un comportamento di runtime non deterministico. | Utilizzare costanti o limitare l'utilizzo di variabili statiche alle funzioni di attività. |
| Variabili di ambiente | Non usare variabili di ambiente nelle funzioni dell'agente di orchestrazione. I relativi valori possono cambiare nel tempo, determinando un comportamento di runtime non deterministico. | È necessario fare riferimento alle variabili di ambiente solo dall'interno di funzioni client o attività. |
| Cicli infiniti | Evitare i cicli infiniti nelle funzioni di orchestrazione. Poiché il Framework attività durevole salva la cronologia di esecuzione durante l'avanzamento della funzione di orchestrazione, un ciclo infinito può causare l'esaurimento della memoria di un'istanza dell'agente di orchestrazione. | Per scenari di ciclo infinito, usare API come `ContinueAsNew` in .NET o `continueAsNew` in JavaScript per riavviare l'esecuzione della funzione e ignorare la cronologia di esecuzione precedente. |

Anche se l'applicazione di questi vincoli potrebbe sembrare difficile all'inizio, in pratica sono facili da seguire.

Il framework delle attività permanenti tenta di rilevare le violazioni delle regole precedenti. Se viene rilevata una violazione, il framework genera un'eccezione **NonDeterministicOrchestrationException.If** it finds a violation, the framework throws a NonDeterministicOrchestrationException exception. Tuttavia, questo comportamento di rilevamento non rileva tutte le violazioni e non è necessario dipendere da esso.

## <a name="versioning"></a>Controllo delle versioni

Un'orchestrazione durevole potrebbe essere eseguita continuamente per giorni, mesi, anni o anche [eternamente](durable-functions-eternal-orchestrations.md). Qualsiasi aggiornamento del codice apportato alle app di Funzioni durevoli che influiscono sulle orchestrazioni non completate potrebbe interrompere il comportamento di riproduzione delle orchestrazioni. Ecco perché è importante pianificare con attenzione quando si apportano aggiornamenti al codice. Per una descrizione più dettagliata di come eseguire la versione del codice, vedere l'articolo sul [controllo delle versioni](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Attività permanenti

> [!NOTE]
> Questa sezione descrive i dettagli di implementazione interna del framework di attività permanenti. È possibile utilizzare funzioni durevoli senza conoscere queste informazioni. che vengono date al solo scopo di comprendere il comportamento di riesecuzione.

Le attività che possono attendere in modo sicuro nelle funzioni dell'agente di orchestrazione vengono in alcune volte definite *attività durevoli.* Il framework delle attività durevoli crea e gestisce queste attività. Esempi sono le attività restituite da **CallActivityAsync**, **WaitForExternalEvent**e **CreateTimer** nelle funzioni dell'agente di orchestrazione .NET.

Queste attività durevoli sono gestite `TaskCompletionSource` internamente da un elenco di oggetti in .NET. Durante la riproduzione, queste attività vengono create come parte dell'esecuzione del codice dell'agente di orchestrazione. Sono finiti quando il dispatcher enumera gli eventi della cronologia corrispondenti.

Le attività vengono eseguite in modo sincrono utilizzando un singolo thread fino a quando tutta la cronologia è stata riprodotta. Le attività durevoli che non vengono completate entro la fine della riproduzione della cronologia hanno le azioni appropriate eseguite. Ad esempio, un messaggio potrebbe essere accodato per chiamare una funzione di attività.

La descrizione di questa sezione del comportamento di runtime consente di `await` comprendere `yield` perché una funzione dell'agente di orchestrazione non può usare o in un'attività non durevole. Esistono due motivi: il thread del dispatcher non può attendere il completamento dell'attività e qualsiasi callback di tale attività potrebbe potenzialmente danneggiare lo stato di rilevamento della funzione dell'agente di orchestrazione. Alcuni controlli di runtime sono in atto per aiutare a rilevare queste violazioni.

Per ulteriori informazioni su come il Framework attività durevole esegue le funzioni dell'agente di orchestrazione, consultare il [codice sorgente dell'attività durevole in GitHub](https://github.com/Azure/durabletask). In particolare, vedere [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come richiamare sottoorchestrazioni](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni](durable-functions-versioning.md)
