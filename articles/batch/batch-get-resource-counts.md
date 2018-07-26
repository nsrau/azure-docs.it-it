---
title: Conteggiare attività e nodi in base allo stato - Azure Batch | Microsoft Docs
description: Conteggiare le attività e i nodi di calcolo di Azure Batch in base allo stato per gestire e monitorare le soluzioni Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 06/29/2018
ms.author: danlep
ms.openlocfilehash: f4bad3d7058e82a246afce9502d275c7d485cb88
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011949"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorare le soluzioni Batch conteggiando attività e nodi in base allo stato

Per monitorare e gestire soluzioni Azure Batch su larga scala, è necessario un conteggio accurato delle risorse in vari stati. Azure Batch offre operazioni efficienti per ottenere i conteggi per le *attività* e i *nodi di calcolo* di Batch. Usare queste operazioni anziché chiamate all'API che potrebbero richiedere molto tempo per restituire informazioni dettagliate su raccolte di attività o nodi di grandi dimensioni.

* L'operazione di [recupero dei conteggi delle attività][rest_get_task_counts] consente di avere un conteggio aggregato delle attività attive, in corso e completate in un processo e delle attività che hanno avuto esito positivo o negativo. 

  Conteggiando le attività in ogni stato, è possibile visualizzare più facilmente lo stato di avanzamento di un processo a un utente o rilevare ritardi o errori imprevisti che possono influire sul processo. Il recupero dei conteggi è disponibile a partire dall'API del servizio Batch versione 2017-06-01.5.1 e negli SDK e strumenti correlati.

* L'operazione di [conteggio dei nodi del pool][rest_get_node_counts] riceve il numero di nodi di calcolo dedicati e con priorità bassa di ogni pool in vari stati: creazione, inattività, non in linea, annullamento, riavvio, ricreazione immagine, avvio e altri. 

  Conteggiando i nodi in ogni stato, è possibile determinare quando sono disponibili le risorse di calcolo adeguata per eseguire i processi e identificare potenziali problemi nei pool. L'elenco dei conteggi dei nodi del pool è disponibile a partire dall'API del servizio Batch versione 2018-03-01.6.1 e negli SDK e strumenti correlati.

Se si usa una versione del servizio che non supporta le operazioni di conteggio delle attività o dei nodi, usare una query di elenco per conteggiare queste risorse. Usare una query di elenco anche per ottenere informazioni su altre risorse di Batch, ad esempio processi, file e applicazioni. Per altre informazioni sull'applicazione dei filtri alle query di elenco, vedere [Creare query per elencare le risorse di Batch in modo efficiente](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Conteggi delle attività in base allo stato

L'operazione di recupero dei conteggi delle attività conta le attività in base agli stati seguenti:

- **Attiva**: l'attività è accodata e può essere eseguita, ma non è attualmente assegnata a un nodo di calcolo. Un'attività è `active` anche se [dipende da un'attività padre](batch-task-dependencies.md) non ancora completata. 
- **In esecuzione**: l'attività è stata assegnata a un nodo di calcolo, ma non è ancora completata. Un'attività viene conteggiata come `running` quando il suo stato è `preparing` o `running`, come indicato dall'operazione di [recupero delle informazioni su un'attività][rest_get_task].
- **Completata**: l'attività non è più idonea per l'esecuzione, perché è stata completata correttamente o è terminata con esito negativo e ha anche superato il limite di tentativi. 
- **Riuscita**: un'attività il cui risultato dell'esecuzione è `success`. Batch determina se un'attività ha avuto esito positivo o negativo controllando la proprietà `TaskExecutionResult` della proprietà [executionInfo][rest_get_exec_info].
- **Non riuscita**: un'attività il cui risultato dell'esecuzione è `failure`.

L'esempio di codice .NET seguente mostra come recuperare i conteggi delle attività in base allo stato: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

Per ottenere i conteggi delle attività per un processo, è possibile usare un criterio simile per REST e per altri linguaggi supportati. 
 

### <a name="consistency-checking-for-task-counts"></a>Verifica della coerenza per i conteggi delle attività

Batch offre un'ulteriore convalida per i conteggi delle attività in base allo stato usando verifiche di coerenza su più componenti del sistema. Nell'improbabile caso che la verifica di coerenza trovi errori, Batch corregge il risultato dell'operazione di recupero dei conteggi delle attività in base ai risultati della verifica di coerenza.

La proprietà `validationStatus` nella risposta indica se Batch ha eseguito la verifica di coerenza. Se Batch non ha verificato i conteggi in base agli stati effettivi presenti nel sistema, la proprietà `validationStatus` è impostata su `unvalidated`. Poiché per motivi di prestazioni Batch non esegue la verifica di coerenza se il processo include più di 200.000 attività, in questo caso la proprietà `validationStatus` è impostata su `unvalidated`. Il conteggio delle attività non è necessariamente errato in questo caso, perché è improbabile persino una perdita di dati limitata. 

Quando un'attività cambia stato, la pipeline di aggregazione elabora la modifica in pochi secondi. L'operazione di recupero dei conteggi delle attività riflette i conteggi delle attività aggiornati in questo periodo di tempo. Tuttavia, se la pipeline di aggregazione non rileva una modifica nello stato di un'attività, la modifica non viene registrata fino al successivo passaggio di convalida. Durante questo periodo di tempo i conteggi delle attività possono essere leggermente imprecisi a causa dell'evento ignorato, ma verranno corretti nel successivo passaggio di convalida.

## <a name="node-state-counts"></a>Conteggi dei nodi in base allo stato

L'operazione di conteggio dei nodi del pool conta i nodi di calcolo di ogni pool in base agli stati seguenti. In ogni pool sono disponibili conteggi aggregati separati per i nodi dedicati e i nodi con priorità bassa.

- **Creating**: macchina virtuale di Azure con allocazione che non è ancora iniziato per aggiungere un pool.
- **Idle**: nodo di calcolo disponibile che attualmente non esegue un'attività.
- **LeavingPool**: nodo che sta lasciando il pool, perché l'utente lo ha rimosso in modo esplicito o perché il pool si sta ridimensionando o riducendo automaticamente.
- **Offline**: nodo che Batch non può usare per pianificare nuove attività.
- **Preempted**: nodo con priorità bassa che è stato rimosso dal pool perché Azure ha recuperato la macchina virtuale. Un nodo `preempted` può essere reinizializzato quando è disponibile una capacità di macchine virtuali con priorità bassa di sostituzione.
- **Rebooting**: nodo in fase di riavvio.
- **Reimaging**: un nodo in cui viene reinstallato il sistema operativo.
- **Running**: nodo che esegue una o più attività (diversa dall'attività di avvio).
- **Starting**: nodo in cui viene avviato il servizio Batch. 
- **StartTaskFailed**: nodo in cui l'[attività di avvio][rest_start_task] non è riuscita e ha esaurito tutti i tentativi e in cui `waitForSuccess` è impostato sull'attività di avvio. Il nodo non può essere usato per l'esecuzione di attività.
- **Unknown**: nodo che ha perso il contatto con il servizio Batch e il cui stato non è noto.
- **Unusable**: nodo che non può essere usato per l'esecuzione di attività a causa di errori.
- **WaitingForStartTask**: nodo in cui l'attività ha iniziato l'esecuzione, ma è impostato `waitForSuccess` e l'attività di avvio non è stata completata.

Il frammento di codice C# seguente indica come elencare i conteggi dei nodi per tutti i pool dell'account corrente:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Il frammento di codice C# seguente indica come elencare i conteggi dei nodi per un pool specifico dell'account corrente.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Per ottenere i conteggi dei nodi per i pool, è possibile usare un criterio simile per REST e altri linguaggi supportati.
 
## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui concetti e sulle funzionalità del servizio Batch, vedere [Panoramica delle funzionalità di Batch](batch-api-basics.md). L'articolo descrive le risorse principali di Batch, tra cui pool, nodi di calcolo, processi e attività, e include una panoramica delle funzionalità del servizio.

* Per informazioni sull'applicazione dei filtri alle query di elenco per le risorse di Batch, vedere [Creare query per elencare le risorse di Batch in modo efficiente](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get#executionInfo
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

