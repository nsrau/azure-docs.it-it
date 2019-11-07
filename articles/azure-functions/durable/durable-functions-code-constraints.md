---
title: Vincoli di codice del codificatore durevole-funzioni di Azure
description: Riesecuzione delle funzioni di orchestrazione e vincoli di codice per Durable Functions di Azure.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 5fc4a7e4256e405ff1a91b88b2b001048cc832fc
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614996"
---
# <a name="orchestrator-function-code-constraints"></a>Vincoli del codice della funzione di orchestrazione

Durable Functions è un'estensione di [funzioni di Azure](../functions-overview.md) che consente di creare app con stato. È possibile usare una [funzione di orchestrazione](durable-functions-orchestrations.md) per orchestrare l'esecuzione di altre funzioni permanenti all'interno di un'app per le funzioni. Le funzioni dell'agente di orchestrazione sono con stato, affidabile e potenzialmente con esecuzione prolungata.

## <a name="orchestrator-code-constraints"></a>Vincoli del codice dell'agente di orchestrazione

Le funzioni dell'agente di orchestrazione usano l'origine [eventi](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) per garantire un'esecuzione affidabile e mantenere lo stato delle variabili locali. Il [comportamento di riproduzione](durable-functions-orchestrations.md#reliability) del codice dell'agente di orchestrazione crea vincoli sul tipo di codice che è possibile scrivere in una funzione dell'agente di orchestrazione. Ad esempio, le funzioni dell'agente di orchestrazione devono essere *deterministiche*: una funzione dell'agente di orchestrazione verrà riprodotta più volte e deve produrre lo stesso risultato ogni volta.

### <a name="using-deterministic-apis"></a>Uso di API deterministiche

In questa sezione vengono fornite alcune semplici linee guida che consentono di garantire la deterministicità del codice.

Le funzioni dell'agente di orchestrazione possono chiamare qualsiasi API nelle lingue di destinazione. Tuttavia, è importante che le funzioni dell'agente di orchestrazione chiamino solo API deterministiche. Un' *API deterministica* è un'API che restituisce sempre lo stesso valore dato lo stesso input, indipendentemente da quando o con quale frequenza viene chiamato.

La tabella seguente illustra esempi di API da evitare perché *non* sono deterministiche. Queste restrizioni si applicano solo alle funzioni dell'agente di orchestrazione. Altri tipi di funzione non hanno restrizioni di questo tipo.

| Categoria API | Motivo | Soluzione alternativa |
| ------------ | ------ | ---------- |
| Date e ore  | Le API che restituiscono la data o l'ora corrente sono non deterministiche, perché il valore restituito è diverso per ogni riproduzione. | Usare l'API`CurrentUtcDateTime` in .NET o l'API `currentUtcDateTime` in JavaScript, che sono sicure per la riproduzione. |
| GUID e UUID  | Le API che restituiscono un GUID o un UUID casuale sono non deterministiche, perché il valore generato è diverso per ogni riproduzione. | Usare `NewGuid` in .NET o `newGuid` in JavaScript per generare in modo sicuro GUID casuali. |
| Numeri casuali | Le API che restituiscono numeri casuali sono non deterministiche, perché il valore generato è diverso per ogni riproduzione. | Utilizzare una funzione di attività per restituire numeri casuali a un'orchestrazione. I valori restituiti delle funzioni di attività sono sempre sicuri per la riproduzione. |
| Associazioni | Le associazioni di input e output eseguono in genere operazioni di I/O e non deterministiche. Una funzione dell'agente di orchestrazione non deve usare direttamente anche le associazioni client di [orchestrazione](durable-functions-bindings.md#orchestration-client) e [client di entità](durable-functions-bindings.md#entity-client) . | Usare le associazioni di input e output all'interno di funzioni client o di attività. |
| Rete | Le chiamate di rete coinvolgono sistemi esterni e sono non deterministiche. | Usare le funzioni di attività per effettuare chiamate di rete. Se è necessario effettuare una chiamata HTTP dalla funzione dell'agente di orchestrazione, è anche possibile usare le [API HTTP durevoli](durable-functions-http-features.md#consuming-http-apis). |
| API di blocco | Il blocco di API come `Thread.Sleep` in .NET e API simili può causare problemi di prestazioni e scalabilità per le funzioni dell'agente di orchestrazione e deve essere evitato. Nel piano a consumo di funzioni di Azure possono anche verificarsi addebiti di runtime superflui. | Usare le alternative alle API di blocco quando sono disponibili. Utilizzare, ad esempio, `CreateTimer` per introdurre ritardi nell'esecuzione dell'orchestrazione. I ritardi di [timer durevoli](durable-functions-timers.md) non vengono conteggiati per il tempo di esecuzione di una funzione di orchestrazione. |
| API asincrone | Il codice dell'agente di orchestrazione non deve mai avviare alcuna operazione asincrona tranne che tramite l'API `IDurableOrchestrationContext` o l'API dell'oggetto `context.df`. Ad esempio, non è possibile usare `Task.Run`, `Task.Delay`e `HttpClient.SendAsync` in .NET o `setTimeout` e `setInterval` in JavaScript. Il Framework di attività permanenti esegue il codice dell'agente di orchestrazione su un singolo thread. Non può interagire con altri thread che potrebbero essere chiamati da altre API asincrone. | Una funzione dell'agente di orchestrazione deve effettuare solo chiamate asincrone durevoli. Le funzioni di attività devono effettuare qualsiasi altra chiamata API asincrona. |
| Funzioni JavaScript asincrone | Non è possibile dichiarare le funzioni dell'agente di orchestrazione JavaScript come `async` perché il runtime di node. js non garantisce che le funzioni asincrone siano deterministiche. | Dichiarare funzioni dell'agente di orchestrazione JavaScript come funzioni generatore sincrone. |
| API di threading | Il Framework di attività permanenti esegue il codice dell'agente di orchestrazione su un singolo thread e non può interagire con altri thread. L'introduzione di nuovi thread nell'esecuzione di un'orchestrazione può comportare un deadlock o un'esecuzione non deterministica. | Le funzioni dell'agente di orchestrazione non dovrebbero mai usare API di Threading. Se tali API sono necessarie, limitarne l'uso solo alle funzioni di attività. |
| Variabili statiche | Evitare di usare variabili statiche non costanti nelle funzioni dell'agente di orchestrazione perché i relativi valori possono cambiare nel tempo, causando un comportamento di runtime non deterministico. | Utilizzare le costanti oppure limitare l'utilizzo di variabili statiche alle funzioni di attività. |
| Variabili di ambiente | Non usare variabili di ambiente nelle funzioni dell'agente di orchestrazione. I valori possono cambiare nel tempo, ottenendo un comportamento di runtime non deterministico. | È necessario fare riferimento alle variabili di ambiente solo nelle funzioni client o nelle funzioni di attività. |
| Cicli infiniti | Evitare i cicli infiniti nelle funzioni di orchestrazione. Poiché il Framework di attività permanenti salva la cronologia di esecuzione durante l'avanzamento della funzione di orchestrazione, un ciclo infinito può causare l'esaurimento della memoria di un'istanza dell'agente di orchestrazione. | Per gli scenari con ciclo infinito, usare API come `ContinueAsNew` in .NET o `continueAsNew` in JavaScript per riavviare l'esecuzione della funzione ed eliminare la cronologia di esecuzione precedente. |

Sebbene l'applicazione di questi vincoli potrebbe sembrare difficile in primo luogo, in pratica è facile da seguire.

Il Framework di attività permanenti tenta di rilevare le violazioni delle regole precedenti. Se viene trovata una violazione, il Framework genera un'eccezione **NonDeterministicOrchestrationException** . Tuttavia, questo comportamento di rilevamento non intercetta tutte le violazioni e non è necessario dipendono da esso.

## <a name="versioning"></a>Controllo delle versioni

È possibile che un'orchestrazione durevole venga eseguita continuamente per giorni, mesi, anni o addirittura [eternamente](durable-functions-eternal-orchestrations.md). Eventuali aggiornamenti del codice apportati alle app Durable Functions che interessano le orchestrazioni non completate potrebbero compromettere il comportamento di riproduzione delle orchestrazioni. Per questo motivo è importante pianificare con attenzione quando si effettuano aggiornamenti al codice. Per una descrizione più dettagliata di come eseguire la versione del codice, vedere l'articolo relativo al [controllo delle versioni](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Attività permanenti

> [!NOTE]
> Questa sezione descrive i dettagli di implementazione interna del framework di attività permanenti. È possibile utilizzare funzioni permanenti senza conoscere queste informazioni. che vengono date al solo scopo di comprendere il comportamento di riesecuzione.

Le attività che possono attendere in modo sicuro nelle funzioni dell'agente di orchestrazione vengono talvolta definite *attività durevoli*. Il Framework di attività permanenti crea e gestisce queste attività. Gli esempi sono le attività restituite da **CallActivityAsync**, **WaitForExternalEvent**e **CreateTimer** nelle funzioni dell'agente di orchestrazione .NET.

Queste attività durevoli sono gestite internamente da un elenco di oggetti `TaskCompletionSource` in .NET. Durante la riproduzione, queste attività vengono create come parte dell'esecuzione del codice dell'agente di orchestrazione. Sono finiti perché il dispatcher enumera gli eventi di cronologia corrispondenti.

Le attività vengono eseguite in modo sincrono utilizzando un thread singolo fino a quando non viene riprodotta tutta la cronologia. Le attività durevoli che non sono finite entro la fine della riproduzione della cronologia hanno le azioni appropriate eseguite. Ad esempio, un messaggio può essere accodato per chiamare una funzione di attività.

Questa sezione descrive il comportamento di runtime per comprendere il motivo per cui una funzione dell'agente di orchestrazione non può utilizzare `await` o `yield` in un'attività non durevole. Esistono due motivi: il thread del dispatcher non può attendere il completamento dell'attività e qualsiasi callback da tale attività potrebbe potenzialmente danneggiare lo stato di rilevamento della funzione dell'agente di orchestrazione. Alcuni controlli di runtime sono disponibili per consentire il rilevamento di tali violazioni.

Per ulteriori informazioni sul modo in cui il Framework di attività permanenti esegue le funzioni dell'agente di orchestrazione, consultare il [codice sorgente delle attività durevoli su GitHub](https://github.com/Azure/durabletask). In particolare, vedere [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come richiamare le orchestrazioni secondarie](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni](durable-functions-versioning.md)
