---
title: Vincoli di codice del codificatore durevole-funzioni di Azure
description: Riesecuzione delle funzioni di orchestrazione e vincoli di codice per Durable Functions di Azure.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935768"
---
# <a name="orchestrator-function-code-constraints"></a>Vincoli del codice della funzione di orchestrazione

Durable Functions è un'estensione di [funzioni di Azure](../functions-overview.md) che consente di compilare applicazioni con stato. È possibile usare una [funzione di orchestrazione](durable-functions-orchestrations.md) per orchestrare l'esecuzione di altre funzioni permanenti all'interno di un'app per le funzioni. Le funzioni dell'agente di orchestrazione sono con stato, affidabile e potenzialmente con esecuzione prolungata.

## <a name="orchestrator-code-constraints"></a>Vincoli del codice dell'agente di orchestrazione

Le funzioni dell'agente di orchestrazione usano l'origine [eventi](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) per garantire un'esecuzione affidabile e mantenere lo stato delle variabili locali. Il [comportamento di riproduzione](durable-functions-orchestrations.md#reliability) del codice dell'agente di orchestrazione crea vincoli sul tipo di codice che è possibile scrivere in una funzione dell'agente di orchestrazione. Ad esempio, il codice dell'agente di orchestrazione deve essere *deterministico*.  Le funzioni dell'agente di orchestrazione verranno riprodotte più volte e dovranno produrre lo stesso risultato ogni volta.

Le sezioni seguenti forniscono alcune semplici linee guida per garantire che il codice sia deterministico.

### <a name="using-deterministic-apis"></a>Uso di API deterministiche

Le funzioni dell'agente di orchestrazione sono gratuite per chiamare qualsiasi API desiderata nella lingua di destinazione. Tuttavia, è importante che le funzioni dell'agente di orchestrazione chiamino solo API *deterministiche* . Un' *API deterministica* è un'API che restituisce sempre esattamente lo stesso valore dato lo stesso input, indipendentemente dal momento in cui viene chiamato.

Di seguito sono riportati alcuni esempi di API da evitare perché *non* sono deterministiche. Queste restrizioni si applicano solo alle funzioni dell'agente di orchestrazione. Altri tipi di funzione non hanno restrizioni di questo tipo.

| Categoria API | `Reason` | Soluzione alternativa |
| ------------ | ------ | ---------- |
| Data/ora  | Le API che restituiscono la data o l'ora _corrente_ sono non deterministiche perché restituiscono un valore diverso per ogni riproduzione. | Usare l'API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) `currentUtcDateTime` o (JavaScript), che è sicura per la riproduzione. |
| GUID/UUID  | Le API che restituiscono un GUID/UUID _casuale_ sono non deterministiche, in quanto il valore generato sarà diverso per ogni riproduzione. | Usare [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.NET) o `newGuid` (JavaScript) per generare in modo sicuro GUID casuali. |
| Numeri casuali | Le API che restituiscono numeri casuali non sono deterministiche, perché il valore generato sarà diverso per ogni riproduzione. | Utilizzare una funzione di attività per restituire numeri casuali a un'orchestrazione. I valori restituiti delle funzioni di attività sono sempre sicuri per la riproduzione. |
| Associazioni | Le associazioni di input e output eseguono in genere operazioni di I/O e non deterministiche. Anche le associazioni client di [orchestrazione](durable-functions-bindings.md#orchestration-client) e [client di entità](durable-functions-bindings.md#entity-client) non devono essere utilizzate direttamente da una funzione dell'agente di orchestrazione. | Usare le associazioni di input e output all'interno di funzioni client o di attività. |
| Rete | Le chiamate di rete coinvolgono sistemi esterni e sono non deterministiche. | Usare le funzioni di attività per effettuare chiamate di rete. Se è necessario effettuare una chiamata HTTP dalla funzione dell'agente di orchestrazione, è anche possibile usare le [API HTTP durevoli](durable-functions-http-features.md#consuming-http-apis). |
| API di blocco | Le API di blocco `Thread.Sleep` quali (.NET) o altre API simili possono causare problemi di prestazioni e scalabilità per le funzioni dell'agente di orchestrazione e devono essere evitate. Nel piano a consumo di funzioni di Azure possono anche verificarsi addebiti di tempo di esecuzione superflui. | Usare alternative alle API di blocco quando disponibili, ad `CreateTimer` esempio per introdurre ritardi nell'esecuzione dell'orchestrazione. I ritardi del [timer durevoli](durable-functions-timers.md) non vengono conteggiati per il tempo di esecuzione di una funzione di orchestrazione. |
| API asincrone | Il codice dell'agente di orchestrazione **non deve mai avviare un'operazione asincrona**, a meno che non usi l'API [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) o l'API dell'oggetto `context.df`. Ad esempio, nessun `Task.Run`, `Task.Delay` o `HttpClient.SendAsync` in .NET o `setTimeout()` e `setInterval()` in JavaScript. Il framework di attività permanenti esegue il codice dell'agente di orchestrazione in un singolo thread e non può interagire con alcun altro thread che possa essere pianificato da altre API asincrone. | Solo le chiamate asincrone *durevoli* devono essere eseguite da una funzione dell'agente di orchestrazione. Tutte le altre chiamate API asincrone devono essere eseguite dalle funzioni di attività. |
| Funzioni JavaScript asincrone | Le funzioni dell'agente di `async` orchestrazione JavaScript non possono essere perché il runtime di node. js non garantisce che le funzioni asincrone siano deterministiche. | Le funzioni dell'agente di orchestrazione JavaScript devono essere dichiarate come funzioni generatore sincrone |
| API di threading | Il Framework di attività permanenti esegue il codice dell'agente di orchestrazione su un singolo thread e non è in grado di interagire con altri thread. L'introduzione di nuovi thread nell'esecuzione di un'orchestrazione può comportare un deadlock o un'esecuzione non deterministica. | Le API di threading non dovrebbero mai essere usate nelle funzioni dell'agente di orchestrazione. Se necessario, devono essere limitate alle funzioni di attività. |
| Variabili statiche | Le variabili statiche non costanti devono essere evitate nelle funzioni dell'agente di orchestrazione perché i relativi valori possono cambiare nel tempo, causando un comportamento di esecuzione non deterministico. | Utilizzare le costanti o limitare l'utilizzo di variabili statiche alle funzioni di attività. |
| Variabili di ambiente | Non utilizzare variabili di ambiente nelle funzioni dell'agente di orchestrazione. I valori possono cambiare nel tempo, ottenendo un comportamento di esecuzione non deterministico. | È necessario fare riferimento alle variabili di ambiente solo dalle funzioni client o dalle funzioni di attività. |
| Cicli infiniti | Nelle funzioni dell'agente di orchestrazione è consigliabile evitare cicli infiniti. Il Framework di attività permanenti salva la cronologia di esecuzione durante l'avanzamento della funzione di orchestrazione, quindi un ciclo infinito potrebbe causare l'esaurimento della memoria di un'istanza dell'agente di orchestrazione. | Per scenari che prevedono la presenza di cicli infiniti, usare API come [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.NET) o `continueAsNew` (JavaScript) per riavviare l'esecuzione della funzione e rimuovere la cronologia di esecuzione precedente. |

Sebbene questi vincoli possano sembrare stringenti, non sono difficili da usare. Il framework di attività permanenti tenta di rilevare le violazioni delle regole indicate in precedenza e genera un'eccezione `NonDeterministicOrchestrationException`. Questa operazione di rilevamento è tuttavia onerosa ed è consigliabile non usarla spesso.

## <a name="versioning"></a>Controllo delle versioni

Un'orchestrazione durevole può essere eseguita in modo continuo per giorni, mesi, anni o addirittura [eternamente](durable-functions-eternal-orchestrations.md). Eventuali aggiornamenti del codice effettuati per Durable Functions applicazioni che interessano le orchestrazioni non ancora completate potrebbero compromettere il comportamento di riproduzione. È pertanto importante pianificare con attenzione quando si effettuano aggiornamenti al codice. Per una descrizione più dettagliata di come eseguire la versione del codice, vedere l'articolo relativo al [controllo delle versioni](durable-functions-versioning.md) .

## <a name="durable-tasks"></a>Attività permanenti

> [!NOTE]
> Questa sezione descrive i dettagli di implementazione interna del framework di attività permanenti. È possibile usare funzioni permanenti senza conoscere queste informazioni che vengono date al solo scopo di comprendere il comportamento di riesecuzione.

Le attività che possono essere messe in attesa in modo sicuro nelle funzioni dell'agente di orchestrazione vengono talvolta definite come *attività permanenti*. Queste attività vengono create e gestite dal framework di attività permanenti. Gli esempi sono le attività restituite `WaitForExternalEvent`da `CallActivityAsync`, `CreateTimer` e nelle funzioni dell'agente di orchestrazione .NET.

Queste *attività durevoli* vengono gestite internamente tramite un elenco `TaskCompletionSource` di oggetti in .NET. Durante la riesecuzione queste attività vengono create come parte dell'esecuzione di codice dell'agente di orchestrazione e vengono completate quando il dispatcher enumera gli eventi di cronologia corrispondenti. L'esecuzione viene eseguita in modo sincrono utilizzando un thread singolo fino a quando non viene riprodotta tutta la cronologia. Tutte le attività durevoli che non sono state completate alla fine della riproduzione della cronologia hanno azioni appropriate eseguite. Un messaggio ad esempio può essere accodata per chiamare una funzione di attività.

Il comportamento di esecuzione descritto di seguito dovrebbe aiutare a comprendere il motivo per cui `await` il `yield` codice della funzione dell'agente di orchestrazione non deve mai o un'attività non durevole: il thread Dispatcher non può attendere il completamento e qualsiasi callback da tale attività potrebbe potenzialmente danneggia lo stato di rilevamento della funzione dell'agente di orchestrazione. Alcuni controlli di runtime sono disponibili per tentare di rilevare tali violazioni.

Se si desiderano altre informazioni sul modo in cui il framework di attività permanenti esegue le funzioni dell'agente di orchestrazione, vedere il [codice sorgente delle attività permanenti in GitHub](https://github.com/Azure/durabletask), in particolare [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come richiamare le orchestrazioni secondarie](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni](durable-functions-versioning.md)
