---
title: 'Eseguire attività in parallelo per usare le risorse di calcolo in modo efficiente: Azure Batch | Documentazione Microsoft'
description: Aumenta l'efficienza e si riducono i costi usando meno nodi di calcolo ed eseguendo attività simultanee in ogni nodo dei pool di Azure Batch
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f45c35e6d9fb611ebf73c4eab8b517d8575b8e82
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094936"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Eseguire attività contemporaneamente per ottimizzare l'uso dei nodi di calcolo Batch 

Eseguendo più attività contemporaneamente in ogni nodo di calcolo nel pool di Azure Batch, è possibile ottimizzare l'utilizzo delle risorse in un numero inferiore di nodi nel pool. Per alcuni carichi di lavoro, questo può consentire tempi di processo più brevi e riduzione del costo.

In alcuni scenari è utile che le risorse di un nodo siano dedicate a una singola attività, ma in molti casi è consigliabile che più attività possano condividere tali risorse:

* **Riduzione dei trasferimenti di dati** nei casi in cui le risorse possono condividere i dati. In questo scenario, è possibile ridurre notevolmente i costi di trasferimento dei dati copiando i dati condivisi in un numero inferiore di nodi ed eseguendo le attività in parallelo in ogni nodo. Questa opzione è praticabile soprattutto se i dati da copiare in ogni nodo devono essere trasferiti tra aree geografiche.
* **Ottimizzazione dell'utilizzo della memoria** quando le attività richiedono quantità di memoria elevate ma solo per brevi periodi di tempo e in ore variabili durante l'esecuzione. È possibile usare un numero minore di istanze dei nodi, ma di dimensioni maggiori e con più memoria per gestire in modo efficiente i picchi. Queste istanze dei nodi avranno più attività in esecuzione in parallelo su ogni nodo, ma ogni attività può sfruttare i vantaggi offerti dall'uso di una memoria con molti nodi in momenti diversi.
* **Riduzione dei limiti del numero di nodi** quando è necessaria la comunicazione tra nodi all'interno di un pool. Attualmente, per i pool configurati per la comunicazione tra nodi è previsto un limite di 50 nodi di calcolo, quindi, se ogni nodo nel pool può eseguire attività in parallelo, è possibile eseguire simultaneamente un maggior numero di attività.
* **Replica di un cluster di elaborazione locale**, ad esempio durante il primo spostamento di un ambiente di calcolo in Azure. Se la soluzione locale corrente esegue più attività per ogni nodo di calcolo, è possibile aumentare il numero massimo di attività dei nodi per rispecchiare maggiormente tale configurazione.

## <a name="example-scenario"></a>Scenario di esempio
Per mostrare i vantaggi dell'esecuzione parallela di attività, si supponga che l'applicazione delle attività abbia requisiti di CPU e memoria che possono essere soddisfatti da dimensioni del nodo [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md). Per poter terminare il processo nei tempi previsti sono tuttavia necessari 1.000 nodi.

Invece di usare nodi Standard\_D1 con 1 core CPU, è possibile usare nodi [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) con 16 core ognuno e abilitare l'esecuzione parallela delle attività. Sarà quindi possibile usare *un numero di nodi inferiore di 16 volte* e invece di 1.000 nodi ne serviranno solo 63. Se ogni nodo usa file dell'applicazione o dati di riferimento di grandi dimensioni, è anche possibile ottimizzare la durata e l'efficienza dei processi perché i dati vengono copiati solo in 63 nodi.

## <a name="enable-parallel-task-execution"></a>Abilitare l'esecuzione parallela di attività
I nodi di calcolo per l'esecuzione di attività parallele vengono configurati a livello di pool. Con la libreria batch .NET, impostare la proprietà [CloudPool. MaxTasksPerComputeNode][maxtasks_net] quando si crea un pool. Se si usa l'API REST di batch, impostare l'elemento [maxTasksPerNode][rest_addpool] nel corpo della richiesta durante la creazione del pool.

Azure Batch consente di impostare attività per nodo fino a (4x) il numero di nodi principali. Ad esempio, se il pool è configurato con nodi di grandi dimensioni (quattro core), è possibile impostare il valore di `maxTasksPerNode` su 16. Tuttavia, indipendentemente dal numero di core del nodo, non è possibile avere più di 256 attività per nodo. Per informazioni dettagliate sul numero di core per ognuna delle dimensioni del nodo, vedere [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md). Per altre informazioni sui limiti del servizio, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).

> [!TIP]
> Assicurarsi di tenere conto del `maxTasksPerNode` valore quando si crea una formula di [scalabilità][enable_autoscaling] automatica per il pool. Ad esempio, l'impatto di un aumento delle attività per nodo può influire in modo significativo su una formula che valuta `$RunningTasks` . Per altre informazioni, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](batch-automatic-scaling.md) .
>
>

## <a name="distribution-of-tasks"></a>Distribuzione delle attività
Quando i nodi di calcolo in un pool possono eseguire attività simultaneamente, è importante specificare come distribuire le attività tra i nodi nel pool.

Utilizzando la proprietà [CloudPool. TaskSchedulingPolicy][task_schedule] , è possibile specificare che le attività devono essere assegnate in modo uniforme in tutti i nodi del pool ("distribuzione"). In alternativa, è possibile specificare che più attività possibili vengano assegnate a ciascun nodo prima di essere assegnate a un altro nodo del pool ("imballaggio").

Per un esempio di come questa funzionalità è utile, prendere in considerazione il pool di nodi [D14 standard\_](../cloud-services/cloud-services-sizes-specs.md) (nell'esempio precedente) configurato con un valore [CloudPool. MaxTasksPerComputeNode][maxtasks_net] pari a 16. Se [CloudPool. TaskSchedulingPolicy][task_schedule] è configurato con un [tipo computenodefilltype][fill_type] di *Pack*, l'utilizzo di tutti i 16 core di ogni nodo potrebbe essere ottimizzato e il [pool](batch-automatic-scaling.md) di scalabilità automatica consente di eliminare i nodi inutilizzati dal pool (nodi senza qualsiasi attività assegnata). Ciò consente di ridurre al minimo l'utilizzo delle risorse e di generare un risparmio sui costi.

## <a name="batch-net-example"></a>Esempio per Batch .NET
Questo frammento di codice dell'API [batch .NET][api_net] Mostra una richiesta per creare un pool contenente quattro nodi con un massimo di quattro attività per nodo. Specifica i criteri di pianificazione delle attività che definiscono le attività da inserire in ogni nodo prima di assegnarle a un altro nodo nel pool. Per altre informazioni sull'aggiunta di pool con l'API batch .NET, vedere [BatchClient. PoolOperations. CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Esempio per Batch REST
Questo frammento di codice API [Rest batch][api_rest] Mostra una richiesta per creare un pool contenente due nodi di grandi dimensioni con un massimo di quattro attività per nodo. Per altre informazioni sull'aggiunta di pool con l'API REST, vedere [aggiungere un pool a un account][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> È possibile impostare l' `maxTasksPerNode` elemento e la proprietà [MaxTasksPerComputeNode][maxtasks_net] solo al momento della creazione del pool. e non possono essere modificati una volta che il pool è stato creato.
>
>

## <a name="code-sample"></a>Esempio di codice
Il progetto [ParallelNodeTasks][parallel_tasks_sample] su GitHub illustra l'uso della proprietà [CloudPool. MaxTasksPerComputeNode][maxtasks_net] .

Questa C# applicazione console usa la libreria [batch .NET][api_net] per creare un pool con uno o più nodi di calcolo. ed esegue un numero configurabile di attività su tali nodi per simulare un carico variabile. L'output dell'applicazione specifica quali nodi eseguono una specifica attività. L'applicazione fornisce inoltre un riepilogo dei parametri e della durata del processo. Di seguito è visualizzato la parte relativa al riepilogo dell'output da due diverse esecuzioni dell'applicazione di esempio.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La prima esecuzione dell'applicazione di esempio mostra che con un singolo nodo nel pool e con l'impostazione predefinita di un'attività per nodo, la durata del processo è superiore a 30 minuti.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La seconda esecuzione dell'esempio illustra una diminuzione significativa nella durata del processo. In questo modo il pool è stato configurato con quattro attività per ogni nodo, consentendo all'esecuzione di attività parallele di completare il processo in circa un quarto del tempo.

> [!NOTE]
> Le durate del processo nei riepiloghi precedenti non includono il tempo di creazione del pool. Tutti i processi precedenti sono stati inviati a pool creati in precedenza, i cui nodi di calcolo si trovavano nello stato *inattivo* al momento dell'invio.
>
>

## <a name="next-steps"></a>Passaggi successivi
### <a name="batch-explorer-heat-map"></a>Mappa termica di Batch Explorer
[Batch Explorer][batch_labs] è uno strumento client autonomo, gratuito e ricco di funzionalità che consente di creare, eseguire il debug e monitorare le applicazioni di Azure Batch. Batch Explorer contiene una funzione *Mappa termica* che fornisce una visualizzazione dell'esecuzione delle attività. Quando si esegue l'applicazione di esempio [ParallelTasks][parallel_tasks_sample] , è possibile usare la funzionalità mappa termica per visualizzare facilmente l'esecuzione delle attività parallele in ogni nodo.


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

