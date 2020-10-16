---
title: Eseguire attività contemporaneamente per ottimizzare l'uso dei nodi di calcolo Batch
description: Aumentare l'efficienza e ridurre i costi usando un minor numero di nodi di calcolo ed eseguendo attività in parallelo in ogni nodo di un pool di Azure Batch
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8bc9f03f05d52df6e400be5c57033ab2a38fa8eb
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102966"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Eseguire attività contemporaneamente per ottimizzare l'uso dei nodi di calcolo Batch

È possibile ottimizzare l'utilizzo delle risorse in un numero inferiore di nodi di calcolo nel pool eseguendo più di un'attività contemporaneamente in ogni nodo.

Sebbene alcuni scenari funzionino meglio con tutte le risorse di un nodo dedicate a una singola attività, alcuni carichi di lavoro possono vedere tempi di lavoro più brevi e costi ridotti quando più attività condividono le risorse seguenti:

- **Ridurre al minimo il trasferimento dei dati** per le attività in grado di condividere i dati. È possibile ridurre notevolmente gli addebiti per il trasferimento dei dati copiando i dati condivisi in un numero inferiore di nodi, quindi eseguendo le attività in parallelo in ogni nodo. Questa opzione è praticabile soprattutto se i dati da copiare in ogni nodo devono essere trasferiti tra aree geografiche.
- **Massimizzare l'utilizzo della memoria** per le attività che richiedono una grande quantità di memoria, ma solo per brevi periodi di tempo e a intervalli variabili durante l'esecuzione. È possibile usare un numero minore di istanze dei nodi, ma di dimensioni maggiori e con più memoria per gestire in modo efficiente i picchi. Queste istanze dei nodi avranno più attività in esecuzione in parallelo su ogni nodo, ma ogni attività può sfruttare i vantaggi offerti dall'uso di una memoria con molti nodi in momenti diversi.
- **Attenuare i limiti dei numeri di nodo** quando è richiesta la comunicazione tra nodi all'interno di un pool. Attualmente, per i pool configurati per la comunicazione tra nodi è previsto un limite di 50 nodi di calcolo, quindi, se ogni nodo nel pool può eseguire attività in parallelo, è possibile eseguire simultaneamente un maggior numero di attività.
- **Replicare un cluster di calcolo locale**, ad esempio quando si sposta per la prima volta un ambiente di calcolo in Azure. Se la soluzione locale corrente esegue più attività per ogni nodo di calcolo, è possibile aumentare il numero massimo di attività dei nodi per rispecchiare maggiormente tale configurazione.

## <a name="example-scenario"></a>Scenario di esempio

Si supponga, ad esempio, un'applicazione di attività con requisiti di CPU e memoria, in modo che i nodi [standard \_ D1](../cloud-services/cloud-services-sizes-specs.md) siano sufficienti. Tuttavia, per completare il processo nel tempo richiesto, sono necessari 1.000 di questi nodi.

Invece di usare nodi Standard\_D1 con 1 core CPU, è possibile usare nodi [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) con 16 core ognuno e abilitare l'esecuzione parallela delle attività. Ciò significa che è possibile utilizzare *16 volte meno nodi* , anziché 1.000 nodi, ma solo 63. Se per ogni nodo sono necessari file di applicazione o dati di riferimento di grandi dimensioni, la durata e l'efficienza del processo risultano migliorate, perché i dati vengono copiati solo in nodi 63.

## <a name="enable-parallel-task-execution"></a>Abilitare l'esecuzione parallela di attività

I nodi di calcolo per l'esecuzione di attività parallele vengono configurati a livello di pool. Con la libreria batch .NET, impostare la proprietà [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) quando si crea un pool. Se si usa l'API REST di batch, impostare l'elemento [taskSlotsPerNode](/rest/api/batchservice/pool/add) nel corpo della richiesta durante la creazione del pool.

> [!NOTE]
> È possibile impostare l' `taskSlotsPerNode` elemento e la proprietà [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) solo al momento della creazione del pool. Non possono essere modificati dopo che è già stato creato un pool.

Azure Batch consente di impostare gli slot attività per nodo fino a (4x) il numero di core del nodo. Ad esempio, se il pool è configurato con nodi di grandi dimensioni (quattro core), è possibile impostare il valore di `taskSlotsPerNode` su 16. Tuttavia, indipendentemente dal numero di core del nodo, non è possibile avere più di 256 slot attività per nodo. Per informazioni dettagliate sul numero di core per ognuna delle dimensioni del nodo, vedere [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md). Per altre informazioni sui limiti del servizio, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).

> [!TIP]
> Verificare di tener conto del valore `taskSlotsPerNode` durante la creazione di una [formula di scalabilità automatica](/rest/api/batchservice/pool/enableautoscale) per il pool. Ad esempio, l'impatto di un aumento delle attività per nodo può influire in modo significativo su una formula che valuta `$RunningTasks` . Per altre informazioni, vedere [ridimensionare automaticamente i nodi di calcolo in un pool di Azure batch](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Specifica distribuzione attività

Quando si abilitano le attività simultanee, è importante specificare come si desidera che le attività vengano distribuite tra i nodi del pool.

La proprietà [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) consente di specificare che le attività vengano assegnate in modo uniforme in tutti i nodi del pool ("distribuzione"). In alternativa, è possibile specificare che più attività possibili vengano assegnate a ciascun nodo prima di essere assegnate a un altro nodo del pool ("imballaggio").

Si consideri ad esempio il pool di nodi [ \_ D14 standard](../cloud-services/cloud-services-sizes-specs.md) , nell'esempio precedente, configurato con un valore di [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) pari a 16. Se [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) è configurato con un [tipo computenodefilltype](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) di *Pack*, l'utilizzo di tutti i 16 core di ogni nodo verrà ottimizzato e sarà possibile consentire a un [pool di scalabilità](batch-automatic-scaling.md) automatica di rimuovere i nodi inutilizzati (nodi senza attività assegnate) dal pool. Ciò consente di ridurre al minimo l'utilizzo delle risorse e di generare un risparmio sui costi.

## <a name="define-variable-slots-per-task"></a>Definire slot variabili per attività

Un'attività può essere definita con la proprietà [CloudTask. RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) , specificando il numero di slot necessari per l'esecuzione in un nodo di calcolo. Valore predefinito di 1. È possibile impostare gli slot di attività variabili se le attività hanno pesi diversi per l'utilizzo delle risorse nel nodo di calcolo. In questo modo ogni nodo di calcolo dispone di un numero ragionevole di attività in esecuzione simultanee senza sovraccaricare risorse di sistema come CPU o memoria.

Per un pool con proprietà, ad esempio, `taskSlotsPerNode = 8` è possibile inviare attività con utilizzo intensivo della CPU più core con `requiredSlots = 8` , mentre altre attività possono essere impostate su `requiredSlots = 1` . Quando si pianifica questo carico di lavoro misto, le attività con utilizzo intensivo della CPU vengono eseguite esclusivamente sui rispettivi nodi di calcolo, mentre altre attività possono essere eseguite simultaneamente (fino a otto attività contemporaneamente) in altri nodi. Questo consente di bilanciare il carico di lavoro tra i nodi di calcolo e migliorare l'efficienza di utilizzo delle risorse.

> [!TIP]
> Quando si usano gli slot di attività variabili, è possibile che le attività di grandi dimensioni con slot più richiesti possano non essere pianificate temporaneamente perché gli slot disponibili in un nodo di calcolo non sono sufficienti, anche quando sono ancora presenti slot inattivi in alcuni nodi. È possibile aumentare la priorità del processo per queste attività per aumentare la possibilità di competere per gli slot disponibili nei nodi.
>
> Il servizio batch emette il [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) quando non è in grado di pianificare un'attività da eseguire e continua a ritentare la pianificazione fino a quando non saranno disponibili gli slot necessari. È possibile restare in ascolto dell'evento per rilevare potenziali problemi di pianificazione delle attività e attenuare di conseguenza.

> [!NOTE]
> Non specificare un'attività maggiore di quella `requiredSlots` del pool `taskSlotsPerNode` . In questo modo l'attività non potrà mai essere eseguita. Il servizio batch non convalida attualmente questo conflitto quando si inviano attività perché un processo potrebbe non avere un pool associato al momento dell'invio oppure può essere modificato in un pool diverso disabilitando o riattivando.

## <a name="batch-net-example"></a>Esempio per Batch .NET

I frammenti di codice dell'API [batch .NET](/dotnet/api/microsoft.azure.batch) seguenti illustrano come creare un pool con più slot attività per nodo e come inviare un'attività con gli slot richiesti.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Creare un pool con più slot attività per nodo

Questo frammento di codice mostra una richiesta per creare un pool che contiene quattro nodi, con quattro slot attività consentiti per ogni nodo. Specifica i criteri di pianificazione delle attività che definiscono le attività da inserire in ogni nodo prima di assegnarle a un altro nodo nel pool.

Per altre informazioni sull'aggiunta di pool con l'API Batch .NET, vedere [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations).

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Creare un'attività con gli slot necessari

Questo frammento di codice crea un'attività con un valore non predefinito `requiredSlots` . Questa attività viene eseguita solo quando sono disponibili slot liberi sufficienti in un nodo di calcolo.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Elencare i nodi di calcolo con conteggi per l'esecuzione di attività e slot

Questo frammento di codice elenca tutti i nodi di calcolo nel pool e stampa i conteggi per le attività in esecuzione e gli slot attività per ogni nodo.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Elenca i conteggi delle attività per il processo

Questo frammento di codice ottiene i conteggi delle attività per il processo, che include le attività e il conteggio degli slot attività per ogni stato dell'attività.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Esempio per Batch REST

I frammenti di codice dell'API [Rest di batch](/rest/api/batchservice/) seguenti illustrano come creare un pool con più slot attività per nodo e come inviare un'attività con gli slot richiesti.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Creare un pool con più slot attività per nodo

Questo frammento di codice mostra una richiesta per creare un pool contenente due nodi di grandi dimensioni con un massimo di quattro attività per nodo.

Per altre informazioni sull'aggiunta di pool con l'API REST, vedere [Aggiungere un pool a un account](/rest/api/batchservice/pool/add).

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Creare un'attività con gli slot necessari

Questo frammento di codice mostra una richiesta di aggiunta di un'attività con un valore non predefinito `requiredSlots` . Questa attività viene eseguita solo quando sono disponibili slot liberi sufficienti nel nodo di calcolo.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>Esempio di codice in GitHub

Il progetto [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) su GitHub illustra l'uso della proprietà [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) .

Questa applicazione console C# usa la libreria [Batch .NET](/dotnet/api/microsoft.azure.batch) per creare un pool con uno o più nodi di calcolo. Esegue un numero configurabile di attività su tali nodi per simulare un carico variabile. L'output dell'applicazione Mostra i nodi che hanno eseguito tutte le attività. L'applicazione fornisce inoltre un riepilogo dei parametri e della durata del processo.

Ad esempio, di seguito è riportata la parte riepilogativa dell'output di due esecuzioni diverse dell'applicazione di esempio ParallelTasks. Le durate del processo indicate qui non includono l'ora di creazione del pool, perché ogni processo è stato inviato a un pool creato in precedenza i cui nodi di calcolo erano nello stato *inattivo* al momento dell'invio.

La prima esecuzione dell'applicazione di esempio mostra che con un singolo nodo nel pool e con l'impostazione predefinita di un'attività per nodo, la durata del processo è superiore a 30 minuti.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La seconda esecuzione dell'esempio illustra una diminuzione significativa nella durata del processo. Questo è dovuto al fatto che il pool è stato configurato con quattro attività per nodo, consentendo l'esecuzione parallela delle attività per il completamento del processo in quasi un trimestre del tempo.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Passaggi successivi

- Provare la mappa termica [Batch Explorer](https://azure.github.io/BatchExplorer/) . Batch Explorer è uno strumento client autonomo, gratuito e ricco di funzionalità che consente di creare, eseguire il debug e monitorare le applicazioni di Azure Batch. Quando si esegue l'applicazione di esempio [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) , la funzionalità batch Explorer mappa termica consente di visualizzare facilmente l'esecuzione delle attività in parallelo in ogni nodo.
- Esplorare [Azure batch esempi su GitHub](https://github.com/Azure/azure-batch-samples).
- Altre informazioni sulle [dipendenze delle attività batch](batch-task-dependencies.md).
