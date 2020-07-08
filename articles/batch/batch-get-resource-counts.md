---
title: Conteggiare gli stati per attività e nodi
description: Conteggiare le attività e i nodi di calcolo di Azure Batch in base allo stato per gestire e monitorare le soluzioni Batch.
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960589"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorare le soluzioni Batch conteggiando attività e nodi in base allo stato

Per monitorare e gestire le soluzioni Azure Batch su larga scala, potrebbe essere necessario determinare i conteggi delle risorse in vari Stati. Azure Batch fornisce operazioni efficienti per ottenere i conteggi per le attività batch e i nodi di calcolo. È possibile usare queste operazioni invece di query di elenco potenzialmente dispendiose in termini di tempo che restituiscono informazioni dettagliate sulle raccolte di grandi dimensioni di attività o nodi.

- L'operazione di [recupero dei conteggi delle attività](/rest/api/batchservice/job/gettaskcounts) consente di avere un conteggio aggregato delle attività attive, in corso e completate in un processo e delle attività che hanno avuto esito positivo o negativo. 

  Conteggiando le attività in ogni stato, è possibile visualizzare più facilmente lo stato di avanzamento di un processo a un utente o rilevare ritardi o errori imprevisti che possono influire sul processo. Il recupero dei conteggi è disponibile a partire dall'API del servizio Batch versione 2017-06-01.5.1 e negli SDK e strumenti correlati.

- L'operazione di [conteggio dei nodi del pool](/rest/api/batchservice/account/listpoolnodecounts) riceve il numero di nodi di calcolo dedicati e con priorità bassa di ogni pool in vari stati: in fase di creazione, inattivo, offline, superato, riavvio, ricreazione immagine, avvio e altri.

  Conteggiando i nodi in ogni stato, è possibile determinare quando sono disponibili le risorse di calcolo adeguata per eseguire i processi e identificare potenziali problemi nei pool. L'elenco dei conteggi dei nodi del pool è disponibile a partire dall'API del servizio Batch versione 2018-03-01.6.1 e negli SDK e strumenti correlati.

Si noti che in alcuni casi i numeri restituiti da queste operazioni potrebbero non essere aggiornati. Se è necessario assicurarsi che il conteggio sia accurato, usare una query di elenco per contare queste risorse. Le query di elenco consentono inoltre di ottenere informazioni su altre risorse di batch, ad esempio le applicazioni. Per altre informazioni sull'applicazione dei filtri alle query di elenco, vedere [Creare query per elencare le risorse di Batch in modo efficiente](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Conteggi delle attività in base allo stato

L'operazione di recupero dei conteggi delle attività conta le attività in base agli stati seguenti:

- **Attiva**: l'attività è accodata e può essere eseguita, ma non è attualmente assegnata a un nodo di calcolo. Un'attività è `active` anche se [dipende da un'attività padre](batch-task-dependencies.md) non ancora completata. 
- **In esecuzione**: l'attività è stata assegnata a un nodo di calcolo, ma non è ancora completata. Un'attività viene conteggiata come `running` quando il suo stato è `preparing` o `running`, come indicato dall'operazione di [recupero delle informazioni su un'attività](/rest/api/batchservice/task/get).
- **Completata**: l'attività non è più idonea per l'esecuzione, perché è stata completata correttamente o è terminata con esito negativo e ha anche superato il limite di tentativi. 
- **Riuscita**: un'attività il cui risultato dell'esecuzione è `success`. Batch determina se un'attività ha avuto esito positivo o negativo controllando la proprietà `TaskExecutionResult` della proprietà [executionInfo](/rest/api/batchservice/task/get).
- **Non riuscita**: un'attività il cui risultato dell'esecuzione è `failure`.

L'esempio di codice .NET seguente mostra come recuperare i conteggi delle attività in base allo stato:

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Per ottenere i conteggi delle attività per un processo, è possibile usare un criterio simile per REST e per altri linguaggi supportati. 

> [!NOTE]
> Le versioni di API del servizio di batch precedenti al 01-08-2018.7.0 restituiscono anche una proprietà `validationStatus` nella risposta di recupero dei conteggi delle attività. Questa proprietà indica se Batch ha controllato i conteggi per la coerenza con gli stati segnalati nell'API di elenco delle attività. Un valore di `validated` indica solo che Batch ha controllato la coerenza almeno una volta per il processo. Il valore della `validationStatus` proprietà non indica se i conteggi restituiti dai conteggi delle attività sono attualmente aggiornati.

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
- **StartTaskFailed**: nodo in cui l'[attività di avvio](/rest/api/batchservice/pool/add#starttask) non è riuscita e sono stati esauriti tutti i tentativi e in cui `waitForSuccess` è impostato sull'attività di avvio. Il nodo non può essere usato per l'esecuzione di attività.
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

- Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
- Per informazioni sull'applicazione di filtri alle query che elencano le risorse batch, vedere [creare query per elencare in modo efficiente le risorse batch](batch-efficient-list-queries.md).
