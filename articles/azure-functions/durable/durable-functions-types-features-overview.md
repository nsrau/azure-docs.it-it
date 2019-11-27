---
title: Tipi di funzione nell'estensione Durable Functions di funzioni di Azure
description: Informazioni sui tipi di funzioni e ruoli che supportano la comunicazione da funzione a funzione in un'orchestrazione Durable Functions in funzioni di Azure.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 7a485f31ed7e112745cf3b45bbfe348e6a2e0fd3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232776"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Tipi e funzionalità di Durable Functions (funzioni di Azure)

Durable Functions è un'estensione di [Funzioni di Azure](../functions-overview.md). È possibile utilizzare Durable Functions per l'orchestrazione con stato dell'esecuzione della funzione. Un'app per le funzioni durevole è una soluzione costituita da funzioni di Azure diverse. Le funzioni possono svolgere ruoli diversi in un'orchestrazione di funzioni permanenti. 

In funzioni di Azure sono attualmente disponibili quattro tipi di funzioni permanenti: attività, agente di orchestrazione, entità e client. Nella parte restante di questa sezione vengono illustrati in modo più dettagliato i tipi di funzioni di un'orchestrazione.

## <a name="orchestrator-functions"></a>Funzioni dell'agente di orchestrazione

Le funzioni dell'agente di orchestrazione descrivono come vengono eseguite le azioni e l'ordine in cui vengono eseguite le azioni. Le funzioni dell'agente di orchestrazione descrivono l'orchestrazione nel codice (C# o JavaScript), come illustrato in [Durable Functions modelli di applicazione](durable-functions-overview.md#application-patterns). Un'orchestrazione può avere molti tipi diversi di azioni, incluse le [funzioni di attività](#activity-functions), le [sottoorchestrazioni](durable-functions-orchestrations.md#sub-orchestrations), l' [attesa di eventi esterni](durable-functions-orchestrations.md#external-events), [http](durable-functions-http-features.md)e [timer](durable-functions-orchestrations.md#durable-timers). Le funzioni dell'agente di orchestrazione possono interagire anche con le [funzioni dell'entità](#entity-functions).

> [!NOTE]
> Le funzioni dell'agente di orchestrazione vengono scritte utilizzando codice ordinario, ma esistono requisiti severi per la scrittura del codice. In particolare, il codice della funzione di orchestrazione deve essere *deterministico*. Il mancato completamento di questi requisiti di determinismo può causare un errore di esecuzione delle funzioni dell'agente di orchestrazione. Per informazioni dettagliate su questi requisiti e su come risolverli, vedere l'argomento [vincoli di codice](durable-functions-code-constraints.md) .

Per informazioni più dettagliate sulle funzioni dell'agente di orchestrazione e sulle relative funzionalità, vedere l'articolo relativo alle [orchestrazioni durevoli](durable-functions-orchestrations.md) .

## <a name="activity-functions"></a>Funzioni di attività

Le funzioni di attività sono l'unità di base di lavoro in un'orchestrazione di funzioni permanenti. Le funzioni di attività sono le funzioni e le attività orchestrate nel processo. Ad esempio, è possibile creare una funzione dell'agente di orchestrazione per elaborare un ordine. Le attività comportano il controllo dell'inventario, l'addebito del cliente e la creazione di una spedizione. Ogni attività sarebbe una funzione di attività distinta. Queste funzioni di attività possono essere eseguite in modo seriale, in parallelo o in una combinazione di entrambe.

Diversamente dalle funzioni dell'agente di orchestrazione, le funzioni di attività non sono limitate nel tipo di lavoro che è possibile eseguire. Le funzioni di attività vengono spesso usate per effettuare chiamate di rete o eseguire operazioni con utilizzo intensivo della CPU. Una funzione Activity può restituire i dati anche alla funzione dell'agente di orchestrazione. Il Framework di attività permanenti garantisce che ogni funzione di attività chiamata venga eseguita almeno *una volta* durante l'esecuzione di un'orchestrazione.

> [!NOTE]
> Poiché le funzioni di attività garantiscono solo l'esecuzione *almeno una volta* , è consigliabile rendere la logica della funzione di attività *idempotente* quando possibile.

Usare un [trigger di attività](durable-functions-bindings.md#activity-trigger) per definire una funzione di attività. Le funzioni .NET ricevono un `DurableActivityContext` come parametro. È anche possibile associare il trigger a qualsiasi altro oggetto serializzabile in JSON per passare gli input alla funzione. In JavaScript è possibile accedere a un input tramite la proprietà `<activity trigger binding name>` nell' [oggetto`context.bindings`](../functions-reference-node.md#bindings). Alle funzioni di attività può essere passato un solo valore. Per passare più valori, è necessario utilizzare tuple, matrici o tipi complessi.

> [!NOTE]
> È possibile attivare una funzione di attività solo da una funzione dell'agente di orchestrazione.

## <a name="entity-functions"></a>Funzioni di entità

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccole parti di stato. Spesso si fa riferimento a queste entità con stato come *entità durevoli*. Come le funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, il *trigger di entità*. Possono anche essere richiamate dalle funzioni client o dalle funzioni dell'agente di orchestrazione. Diversamente dalle funzioni dell'agente di orchestrazione, le funzioni di entità non hanno vincoli di codice specifici. Le funzioni di entità, inoltre, gestiscono lo stato in modo esplicito anziché in modo implicito, rappresentandolo tramite flusso di controllo.

> [!NOTE]
> Le funzioni di entità e le funzionalità correlate sono disponibili solo in Durable Functions 2.0 e versioni successive.

Per altre informazioni sulle funzioni di entità, vedere l'articolo sulle [entità durevoli](durable-functions-entities.md) .

## <a name="client-functions"></a>Funzioni client

Le funzioni dell'agente di orchestrazione vengono attivate da un' [associazione di trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger) e le funzioni dell'entità vengono attivate da un' [associazione di trigger](durable-functions-bindings.md#entity-trigger)di Entrambi questi trigger funzionano con la reazione ai messaggi accodati in un [Hub attività](durable-functions-task-hubs.md). Il modo principale per recapitare questi messaggi consiste nell'usare un'associazione client dell'agente di [orchestrazione](durable-functions-bindings.md#orchestration-client) o un' [associazione client di entità](durable-functions-bindings.md#entity-client) all'interno di una *funzione client*. Qualsiasi funzione non agente di orchestrazione può essere una *funzione client*. Ad esempio, è possibile attivare l'agente di orchestrazione da una funzione attivata tramite HTTP, una funzione attivata da Hub eventi di Azure e così via. Che cosa fa una funzione una *funzione client* è l'uso dell'associazione di output client durevole.

> [!NOTE]
> A differenza di altri tipi di funzione, l'agente di orchestrazione e le funzioni di entità non possono essere attivati direttamente usando i pulsanti nel portale di Azure. Se si vuole testare un agente di orchestrazione o una funzione di entità nel portale di Azure, è invece necessario eseguire una *funzione client* che avvia un agente di orchestrazione o una funzione di entità come parte dell'implementazione. Per un'esperienza di test più semplice, è consigliabile usare una funzione *trigger manuale* .

Oltre ad attivare le funzioni dell'agente di orchestrazione o dell'entità, l'associazione *client durevole* può essere utilizzata per interagire con l'esecuzione di orchestrazioni ed entità. Ad esempio, le orchestrazioni possono essere sottoposte a query, terminate e possono avere eventi generati. Per ulteriori informazioni sulla gestione delle orchestrazioni e delle entità, vedere l'articolo relativo alla [gestione delle istanze](durable-functions-instance-management.md) .

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, creare la prima funzione durevole in [C#](durable-functions-create-first-csharp.md) o [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Scopri di più sulle orchestrazioni Durable Functions](durable-functions-orchestrations.md)