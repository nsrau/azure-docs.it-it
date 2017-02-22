---
title: "Configurare attività che dipendono da altre attività: Azure Batch | Documentazione Microsoft"
description: "È possibile creare attività che dipendono dal completamento corretto di altre attività per l&quot;elaborazione di carichi di lavoro di tipo MapReduce e carichi di lavoro Big Data simili in Azure Batch."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 01/23/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bf22cd3426e936c8d74377f59443e5e1a6834286
ms.openlocfilehash: 32293e15b6f610a3613d6b666bdd0eea0495b2b8


---
# <a name="task-dependencies-in-azure-batch"></a>Relazioni tra attività in Azure Batch
La funzionalità relazioni tra attività di Azure Batch è ideale se si vogliono elaborare:

* Carichi di lavoro di tipo MapReduce nel cloud.
* Processi le cui attività di elaborazione dati può essere espressa come grafo aciclico diretto (DAG).
* Qualsiasi altro processo in cui le attività downstream dipendono l'output delle attività upstream.

Le dipendenze dell'attività di batch consentono di creare attività pianificate per l'esecuzione nei nodi di calcolo solo dopo il completamento corretto di una o più attività. Ad esempio, è possibile creare un processo che esegue il rendering di ogni fotogramma di un film 3D con le attività parallele separate. L'attività finale, ovvero "attività di unione", unisce i fotogrammi sottoposti a rendering in un filmato completo solo dopo che è stato eseguito il rendering di tutti i fotogrammi.

È possibile creare attività che dipendono da altre attività in una relazione uno-a-uno o uno-a-molti. È anche possibile creare una dipendenza dell'intervallo in cui un'attività dipende dal corretto completamento di un gruppo di attività in un intervallo di ID attività specifico. È possibile combinare questi tre scenari di base per creare relazioni molti-a-molti.

## <a name="task-dependencies-with-batch-net"></a>Relazioni tra attività con Batch .NET
Questo articolo illustra la configurazione di relazioni tra attività tramite la libreria [Batch .NET][net_msdn]. Viene illustrato prima come [abilitare le dipendenze tra attività](#enable-task-dependencies) nei processi, quindi viene spiegato come [configurare un'attività con dipendenze](#create-dependent-tasks). Vengono infine illustrati gli [scenari delle relazione](#dependency-scenarios) supportate da Batch.

## <a name="enable-task-dependencies"></a>Abilitare le relazioni tra attività
Per usare le relazioni tra attività nell'applicazione Batch, è prima di tutto necessario indicare al servizio Batch che il processo userà le relazioni tra attività. In Batch .NET abilitare la funzionalità in [CloudJob][net_cloudjob] impostando la rispettiva proprietà [UsesTaskDependencies][net_usestaskdependencies] su `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Nel frammento di codice precedente "batchClient" è un'istanza della classe [BatchClient][net_batchclient].

## <a name="create-dependent-tasks"></a>Creare attività dipendenti
Per creare un'attività che dipende dal completamento corretto di una o più attività, indicare a Batch che l'attività "dipende" dalle altre attività. In Batch .NET configurare la proprietà [CloudTask][net_cloudtask].[DependsOn][net_dependson] con un'istanza della classe [TaskDependencies][net_taskdependencies]:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Questo frammento di codice crea un'attività con ID "Flowers" che verrà pianificata per l'esecuzione su un nodo di calcolo solo dopo il completamento corretto delle attività con ID "Rain" e "Sun".

> [!NOTE]
> Un'attività viene considerata come completata quando lo stato dell'attività è **completato** e il relativo **codice di uscita** è `0`. In Batch .NET ciò corrisponde a un valore della proprietà [CloudTask][net_cloudtask].[State][net_taskstate] pari a `Completed` e il valore della proprietà [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] è `0`.
> 
> 

## <a name="dependency-scenarios"></a>scenari delle relazione
In Azure Batch è possibile usare tre scenari di relazioni tra attività di base, ovvero uno-a-uno, uno-a-molti e la relazione tra intervalli di ID. Questi scenari possono essere combinati per ottenere un quarto scenario, ovvero molti-a-molti.

| Scenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Esempio |  |
|:---:| --- | --- |
|  [Uno-a-uno](#one-to-one) |L'*attivitàB* dipende dall'*attivitàA* <p/> L'*attivitàB* sarà pianificata per l'esecuzione solo dopo il completamento corretto dell'*attivitàA* |![Diagramma: relazione uno-a-uno tra attività][1] |
|  [Uno-a-molti](#one-to-many) |L'*attivitàC* dipende sia dall'*attivitàA* che dall'*attivitàB*. <p/> L'*attivitàC* sarà pianificata per l'esecuzione solo dopo il completamento corretto dell'*attivitàA* e dell'*attivitàB* |![Diagramma: relazione uno-a-molti tra attività][2] |
|  [Intervallo di ID attività](#task-id-range) |L'*attivitàD* dipende da un intervallo di attività <p/> L'*attivitàD* sarà pianificata per l'esecuzione solo dopo il completamento corretto delle attività con ID compresi tra *1* e *10* |![Diagramma: relazione tra intervalli di ID attività][3] |

> [!TIP]
> È possibile creare relazioni **molti-a-molti**, ad esempio relazioni in cui le attività C, D, E e F dipendono dalle attività A e B. Questo tipo di relazione risulta utile, ad esempio, negli scenari di pre-elaborazione parallelizzata, in cui le attività downstream dipendono dall'output di più attività upstream.
> 
> 

### <a name="one-to-one"></a>Uno-a-uno
Per creare un'attività che dipende dal corretto completamento di un'altra attività, è necessario fornire un singolo ID attività al metodo statico [TaskDependencies][net_taskdependencies].[OnId][net_onid] quando si popola la proprietà [DependsOn][net_dependson] di [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Uno-a-molti
Per creare un'attività che dipende dal corretto completamento di più attività, è necessario fornire una raccolta di ID attività al metodo statico [TaskDependencies][net_taskdependencies].[OnIds][net_onids] quando si popola la proprietà [DependsOn][net_dependson] di [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Intervallo di ID attività
Per creare un'attività che dipende dal corretto completamento di un gruppo di attività, i cui ID rientrano in un intervallo specifico, è necessario fornire il primo e l'ultimo ID attività dell'intervallo al metodo statico [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] quando si popola la proprietà [DependsOn][net_dependson] di [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Quando si usano intervalli di ID attività per le relazioni, gli ID attività inclusi nell'intervallo *devono* essere rappresentazioni di stringa di valori interi. Ogni attività nell'intervallo, inoltre, deve essere completata correttamente per consentire la pianificazione per l'esecuzione dell'attività dipendente.
> 
> 

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Esempio di codice
Il progetto di esempio [TaskDependencies][github_taskdependencies] è uno degli [esempi di codice di Azure Batch][github_samples] disponibili in GitHub. Questa soluzione di Visual Studio 2015 illustra come abilitare le dipendenze tra attività in un processo, come creare attività che dipendono da altre attività e come eseguire tali attività in un pool di nodi di calcolo.

## <a name="next-steps"></a>Passaggi successivi
### <a name="application-deployment"></a>Distribuzione dell'applicazione
La funzionalità [Pacchetti dell'applicazione](batch-application-packages.md) di Batch offre un modo semplice per distribuire e controllare le versioni delle applicazioni eseguite dalle attività nei nodi di calcolo.

### <a name="installing-applications-and-staging-data"></a>Installazione delle applicazioni e staging dei dati
Per una panoramica delle diverse modalità di preparazione dei nodi per l'esecuzione di attività, vedere il post di blog [Installing applications and staging data on Batch compute nodes][forum_post] (Installazione di applicazioni e staging dei dati nei nodi di calcolo di Batch) nel forum di Azure Batch. Scritto da uno dei membri del team di Azure Batch, questo post è una panoramica utile dei diversi modi disponibili per ottenere file, inclusi i dati relativi ad applicazioni e input di attività, nei nodi di calcolo e

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagramma: relazione uno-a-uno"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagramma: relazione uno-a-molti"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagramma: relazione tra intervalli di ID attività"



<!--HONumber=Jan17_HO4-->


