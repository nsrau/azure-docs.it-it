---
title: Tipi di funzione in Funzioni durevoli di AzureFunction types in Azure Durable Functions
description: Informazioni sui tipi di funzioni e ruoli che supportano la comunicazione da funzione a funzione in un'orchestrazione di funzioni durevoli in Funzioni di Azure.Learn about the types of functions and roles that support function-to-function communication in a Durable Functions orchestration in Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277882"
---
# <a name="durable-functions-types-and-features"></a>Tipi e funzioni durevoli

Durable Functions è un'estensione di [Funzioni di Azure](../functions-overview.md). È possibile usare funzioni durevoli per l'orchestrazione con stato dell'esecuzione della funzione. Un'app per funzioni durevoli è una soluzione costituita da diverse funzioni di Azure.A durable function app is a solution that's made up of different Azure functions. Le funzioni possono svolgere ruoli diversi in un'orchestrazione di funzioni durevoli. 

Attualmente sono disponibili quattro tipi di funzioni durevoli in Funzioni di Azure: attività, orchestratore, entità e client. Il resto di questa sezione viene fornito in ulteriori dettagli sui tipi di funzioni coinvolte in un'orchestrazione.

## <a name="orchestrator-functions"></a>Funzioni dell'agente di orchestrazione

Le funzioni dell'agente di orchestrazione descrivono come vengono eseguite le azioni e l'ordine in cui vengono eseguite le azioni. Le funzioni dell'agente di orchestrazione descrivono l'orchestrazione nel codice (C o JavaScript) come illustrato nei modelli di [applicazione di Funzioni durevoli.](durable-functions-overview.md#application-patterns) Un'orchestrazione può avere molti tipi diversi di azioni, tra cui funzioni di [attività](#activity-functions), [sottoorchestrazioni](durable-functions-orchestrations.md#sub-orchestrations), in attesa di [eventi esterni](durable-functions-orchestrations.md#external-events), [HTTP](durable-functions-http-features.md)e timer [.](durable-functions-orchestrations.md#durable-timers) Le funzioni dell'agente di orchestrazione possono anche interagire con le [funzioni di entità](#entity-functions).

> [!NOTE]
> Le funzioni dell'agente di orchestrazione vengono scritte usando codice ordinario, ma esistono requisiti rigorosi su come scrivere il codice. In particolare, il codice della funzione dell'agente di orchestrazione deve essere *deterministico.* Se non si rispettano questi requisiti di determinismo, le funzioni dell'agente di orchestrazione non vengono eseguite correttamente. Informazioni dettagliate su questi requisiti e su come risolverli sono disponibili nell'argomento [relativo ai vincoli](durable-functions-code-constraints.md) di codice.

Per informazioni più dettagliate sulle funzioni dell'agente di orchestrazione e sulle relative funzionalità, vedere l'articolo [Orchestrazioni durevoli.](durable-functions-orchestrations.md)

## <a name="activity-functions"></a>Funzioni di attività

Le funzioni di attività sono l'unità di base del lavoro in un'orchestrazione di funzioni durevoli. Le funzioni di attività sono le funzioni e le attività orchestrate nel processo. Ad esempio, è possibile creare una funzione dell'agente di orchestrazione per elaborare un ordine. Le attività riguardano il controllo dell'inventario, l'addebito al cliente e la creazione di una spedizione. Ogni attività sarebbe una funzione di attività separata. Queste funzioni di attività possono essere eseguite in modo seriale, in parallelo o una combinazione di entrambi.

A differenza delle funzioni dell'agente di orchestrazione, le funzioni di attività non sono limitate nel tipo di lavoro che è possibile eseguire in esse. Le funzioni di attività vengono spesso utilizzate per effettuare chiamate di rete o eseguire operazioni che richiedono un utilizzo intensivo della CPU. Una funzione di attività può anche restituire dati alla funzione dell'agente di orchestrazione. Il framework delle attività durevoli garantisce che ogni funzione di attività chiamata verrà eseguita *almeno una volta* durante l'esecuzione di un'orchestrazione.

> [!NOTE]
> Poiché le funzioni di attività garantiscono *almeno una volta* l'esecuzione, è consigliabile rendere *idemotent* la funzione di attività quando possibile.

Usare un trigger di [attività](durable-functions-bindings.md#activity-trigger) per definire una funzione di attività. Le funzioni .NET ricevono un `DurableActivityContext` parametro. È anche possibile associare il trigger a qualsiasi altro oggetto serializzabile JSON per passare gli input alla funzione. In JavaScript, è possibile accedere `<activity trigger binding name>` a un input tramite la proprietà sull'oggetto [ `context.bindings` ](../functions-reference-node.md#bindings). Le funzioni di attività possono avere un solo valore passato. Per passare più valori, è necessario usare tuple, matrici o tipi complessi.

> [!NOTE]
> È possibile attivare una funzione di attività solo da una funzione dell'agente di orchestrazione.

## <a name="entity-functions"></a>Funzioni di entità

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccole parti di stato. Spesso ci riferiamo a queste entità con stato come *entità durevoli.* Come le funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, il *trigger di entità*. Possono anche essere richiamati da funzioni client o da funzioni dell'agente di orchestrazione. A differenza delle funzioni del ritor, le funzioni di entità non hanno vincoli di codice specifici. Le funzioni di entità, inoltre, gestiscono lo stato in modo esplicito anziché in modo implicito, rappresentandolo tramite flusso di controllo.

> [!NOTE]
> Le funzioni di entità e le funzionalità correlate sono disponibili solo in Durable Functions 2.0 e versioni successive.

Per altre informazioni sulle funzioni di entità, vedere l'articolo [Entità durevoli.](durable-functions-entities.md)

## <a name="client-functions"></a>Funzioni client

Le funzioni dell'agente di orchestrazione vengono attivate da [un'associazione](durable-functions-bindings.md#orchestration-trigger) di trigger di orchestrazione e le funzioni di entità vengono attivate da [un'associazione](durable-functions-bindings.md#entity-trigger)di trigger di entità. Entrambi questi trigger funzionano reagendo ai messaggi accodati in un [hub attività.](durable-functions-task-hubs.md) Il modo principale per recapitare questi messaggi consiste nell'utilizzare [un'associazione client dell'agente](durable-functions-bindings.md#orchestration-client) di orchestrazione o un'associazione client di [entità](durable-functions-bindings.md#entity-client) dall'interno di una *funzione client.* Qualsiasi funzione non di orchestrazione può essere una *funzione client*. Ad esempio, è possibile attivare l'agente di orchestrazione da una funzione attivata da HTTP, una funzione attivata da Hub eventi di Azure e così via. Ciò che rende una funzione una *funzione client* è l'uso dell'associazione di output client durevole.

> [!NOTE]
> A differenza di altri tipi di funzione, le funzioni dell'agente di orchestrazione e dell'entità non possono essere attivate direttamente usando i pulsanti nel portale di Azure.Unlike other function types, orchestrator and entity functions cannot be triggered directly using the buttons in the Azure Portal. Se si vuole testare un agente di orchestrazione o una funzione di entità nel portale di Azure, è invece necessario eseguire una *funzione client* che avvia un agente di orchestrazione o una funzione di entità come parte dell'implementazione. Per un'esperienza di test più semplice, è consigliata una funzione *di trigger manuale.*

Oltre all'attivazione di funzioni dell'agente di orchestrazione o dell'entità, l'associazione *client durevole* può essere utilizzata per interagire con le orchestrazioni e le entità in esecuzione. Ad esempio, le orchestrazioni possono essere sottoposte a query, terminate e possono avere eventi generati. Per altre informazioni sulla gestione di orchestrazioni ed entità, vedere l'articolo [Gestione delle istanze.](durable-functions-instance-management.md)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, creare la prima funzione durevole in [C](durable-functions-create-first-csharp.md) , o [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Altre informazioni sulle orchestrazioni di funzioni permanentiRead more about Durable Functions orchestrations](durable-functions-orchestrations.md)