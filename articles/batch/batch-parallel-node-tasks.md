<properties
   pageTitle="Ottimizzare l'utilizzo dei nodi di Azure Batch con le attività parallele | Microsoft Azure"
   description="Aumentare l'efficienza e ridurre i costi grazie all'uso di un minor numero di nodi di calcolo e all'esecuzione di attività simultanee in ogni nodo in un pool di Azure Batch."
   services="batch"
   documentationCenter=".net"
   authors="mmacy"
   manager="timlt"
   editor=""/>

   <tags
   	ms.service="batch"
   	ms.devlang="multiple"
   	ms.topic="article"
   	ms.tgt_pltfrm="vm-windows"
   	ms.workload="big-compute"
   	ms.date="11/02/2015"
   	ms.author="v-marsma"/>

# Ottimizzare l'utilizzo delle risorse di calcolo di Azure Batch con attività dei nodi simultanee

L'esecuzione di attività parallele su larga scala è una delle funzionalità principali di Azure Batch. Questa funzionalità si applica non solo a più nodi di calcolo che eseguono le attività, ma anche alle attività simultanee in esecuzione su tali nodi. Azure Batch offre la scalabilità completa perché permette di distribuire le attività su più nodi e di eseguire attività in parallelo.

L'abilitazione dell'esecuzione di attività simultanee nei nodi di calcolo di un pool consente di ottimizzare l'utilizzo delle risorse su un numero di nodi ridotto all'interno del pool. In alcuni scenari è utile che le risorse di un nodo siano disponibili per l'allocazione a una singola attività, ma in molti casi è consigliabile che più attività possano condividere tali risorse:

 - **Riduzione dei trasferimenti di dati** nei casi in cui le risorse possono condividere i dati. In questo scenario, è possibile ridurre notevolmente i costi di trasferimento dei dati copiando i dati condivisi in un numero inferiore di nodi ed eseguendo le attività in parallelo in ogni nodo. Questa opzione è praticabile soprattutto se i dati da copiare in ogni nodo devono essere trasferiti tra aree geografiche.

 - **Ottimizzazione dell'utilizzo della memoria** quando le attività richiedono quantità di memoria elevate ma solo per brevi periodi di tempo e in ore variabili durante l'esecuzione. È possibile usare un numero minore di istanze dei nodi, ma di dimensioni maggiori, ossia con più memoria, per gestire in modo efficiente i picchi. In queste istanze dei nodi sono presenti attività parallele eseguite su ogni nodo, che possono sfruttare i vantaggi offerti dall'uso di una memoria con molti nodi in momenti diversi.

 - **Riduzione dei limiti del numero di nodi** quando è necessaria la comunicazione tra nodi all'interno di un pool. Attualmente, per i pool configurati per la comunicazione tra nodi è previsto un limite di 50 nodi, quindi è possibile eseguire simultaneamente un maggior numero di attività se ogni nodo nel pool può eseguire attività in parallelo.

 - **Replica di un cluster di elaborazione locale**, ad esempio durante il primo spostamento di un ambiente di calcolo in Azure. È possibile aumentare il numero massimo di attività dei nodi per rispecchiare maggiormente una configurazione fisica esistente, se tale configurazione esegue attualmente più attività per ogni nodo di calcolo.

## Scenario di esempio

Di seguito è riportato un esempio che illustra i vantaggi dell'esecuzione di attività parallele. Si supponga che l'applicazione per l'attività disponga dei requisiti di CPU e memoria per cui la dimensione del nodo Standard\_D1 risulta appropriata, ma per poter eseguire il processo nel tempo necessario, sono necessari 1000 nodi.

Invece di usare nodi Standard D1, è possibile usare nodi Standard D14 con 16 core e abilitare l'esecuzione parallela di attività. In questo caso, è possibile usare *un numero di nodi inferiore di 16 volte* e invece di 1000 nodi ne servirebbero solo 63. Questo consente di migliorare in modo significativo i tempi di esecuzione del processo e l'efficienza, soprattutto se ogni nodo usa file dell'applicazione di grandi dimensioni o dati di riferimento.

## Abilitare l'esecuzione parallela di attività

È possibile configurare i nodi di calcolo nella soluzione Batch per l'esecuzione di attività parallele a livello di pool. Con l'API .NET di Batch impostare la proprietà [CloudPool.MaxTasksPerComputeNode][maxtasks_net] durante la creazione del pool. Nell'API REST di Batch l'elemento [maxTasksPerNode][maxtasks_rest] viene impostato nel corpo della richiesta durante la creazione del pool.

Con Azure Batch è possibile impostare il numero massimo di attività consentite per nodo fino a quattro volte (4x) il numero di core del nodo. Ad esempio se il pool è configurato con nodi di dimensioni "Large" (quattro core), è possibile impostare il valore di `maxTasksPerNode` su 16. Per informazioni dettagliate sul numero di core per ognuna delle dimensioni del nodo, vedere [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md). Per altre informazioni sui limiti del servizio, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).

> [AZURE.TIP]Assicurarsi di tenere conto del valore di `maxTasksPerNode` durante la creazione di una [formula di scalabilità automatica][enable_autoscaling] per il pool. Ad esempio, l'impatto di un aumento delle attività per nodo può influire in modo significativo su una formula che valuta `$RunningTasks`. Per altre informazioni, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](batch-automatic-scaling.md).

## Distribuzione delle attività

Quando i nodi di calcolo in un pool sono in grado di eseguire attività simultaneamente, è importante specificare come distribuire le attività tra i nodi nel pool.

Tramite la proprietà [CloudPool.TaskSchedulingPolicy][task_schedule] è possibile specificare che le attività vengano assegnate in modo uniforme in tutti i nodi del pool ("distribuzione"). In alternativa, è possibile specificare che più attività possibili vengano assegnate a ciascun nodo prima di essere assegnate a un altro nodo del pool ("imballaggio").

Per comprendere l'importanza di questa funzionalità, si consideri il pool di nodi Standard\_D14 dell'esempio precedente, configurato con un valore di 16 per [CloudPool.MaxTasksPerComputeNode][maxtasks_net]. Se i criteri [CloudPool.TaskSchedulingPolicy][task_schedule] sono configurati con il tipo [ComputeNodeFillType][fill_type] di *Pack*, verrà ottimizzato l'utilizzo di tutti e 16 i core di ogni nodo e, per i [pool con scalabilità automatica](./batch-automatic-scaling.md), sarà possibile escludere i nodi non utilizzati dal pool (nodi a cui non sono assegnate attività). Ciò consente di ridurre al minimo l'utilizzo delle risorse e di generare un risparmio sui costi.

## Esempio per Batch .NET

Questo frammento di codice dell'API [.NET di Batch][api_net] mostra una richiesta per creare un pool contenente quattro nodi di grandi dimensioni con un massimo di quattro attività per nodo. Specifica i criteri di pianificazione delle attività che definiscono le attività da inserire in ogni nodo prima di assegnarle a un altro nodo nel pool. Per altre informazioni sull'aggiunta di un pool con l'API .NET di Batch, vedere [BatchClient.PoolOperations.CreatePool][poolcreate_net].

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Esempio per Batch REST

Questo frammento di codice dell'API [REST di Batch][api_rest] mostra una richiesta per creare un pool contenente due nodi di grandi dimensioni con un massimo di quattro attività per nodo. Per altre informazioni sull'aggiunta di un pool con l'API REST, vedere [Aggiungere un pool a un account][maxtasks_rest].

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE]L'elemento `maxTasksPerNode` e la proprietà [MaxTasksPerComputeNode][maxtasks_net] possono essere impostati solo al momento della creazione del pool e non possono essere modificati una volta che il pool è stato creato.

## Esplorare il progetto di esempio

Consultare il progetto [ParallelNodeTasks][parallel_tasks_sample] su GitHub. Si tratta di un esempio di codice di lavoro che illustra l'uso della proprietà [CloudPool.MaxTasksPerComputeNode][maxtasks_net].

Questa applicazione console C# usa la libreria [.NET di Batch][api_net] per creare un pool con uno o più nodi di calcolo ed esegue un numero configurabile di attività su tali nodi per simulare un carico variabile. L'output dell'applicazione specifica quali nodi eseguono una specifica attività. L'applicazione fornisce inoltre un riepilogo dei parametri e della durata del processo. Di seguito è visualizzato la parte relativa al riepilogo dell'output da due diverse esecuzioni dell'applicazione di esempio.

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

> [AZURE.NOTE]Le durate del processo nei riepiloghi precedenti non includono il tempo di creazione del pool. Ognuno dei processi precedenti è stato inviato a pool creati in precedenza, i cui nodi di calcolo si trovavano nello stato *Inattivo* al momento dell'invio.

## Mappa termica di Batch Explorer

[Batch Explorer][batch_explorer], una delle [applicazioni di esempio][github_samples] di Azure Batch, contiene una funzionalità definita *Mappa termica* che fornisce una visualizzazione dell'esecuzione dell'attività. Durante l'esecuzione dell'applicazione di esempio [ParallelTasks][parallel_tasks_sample], è possibile usare la funzionalità Mappa termica per visualizzare facilmente l'esecuzione delle attività parallele in ogni nodo.

![Mappa termica di Batch Explorer][1]

*La mappa termica di Batch Explorer mostra un pool di quattro nodi, in cui ogni nodo esegue attualmente quattro attività*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=AcomDC_0114_2016-->