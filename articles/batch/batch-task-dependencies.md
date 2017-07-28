---
title: "Usare le relazioni tra attività per eseguire attività basate sul completamento di altre attività: Azure Batch | Microsoft Docs"
description: "Creare attività che dipendono dal completamento di altre attività per l&quot;elaborazione di carichi di lavoro di tipo MapReduce e carichi di lavoro Big Data simili in Azure Batch."
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
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 465306d2de8d1dbe6ba1f0cd74be720b78a50de3
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Creare relazioni tra attività per eseguire attività che dipendono da altre attività

È possibile definire dipendenze delle attività per eseguire un'attività o un set di attività solo dopo il completamento di un'attività padre. Ecco alcuni degli scenari in cui le dipendenze delle attività sono utili:

* Carichi di lavoro di tipo MapReduce nel cloud.
* Processi le cui attività di elaborazione dati può essere espressa come grafo aciclico diretto (DAG).
* Processi di pre-rendering e post-rendering, in cui ogni attività deve essere completata prima di poter avviare quella successiva.
* Qualsiasi altro processo in cui le attività downstream dipendono l'output delle attività upstream.

Tramite le dipendenze delle attività di Batch è possibile creare attività di cui pianificare l'esecuzione nei nodi di calcolo dopo il completamento di una o più attività padre. Ad esempio, è possibile creare un processo che esegue il rendering di ogni fotogramma di un film 3D con le attività parallele separate. L'attività finale, ovvero "attività di unione", unisce i fotogrammi sottoposti a rendering in un filmato completo solo dopo che è stato eseguito il rendering di tutti i fotogrammi.

Per impostazione predefinita, l'esecuzione delle attività dipendenti è pianificata solo dopo il corretto completamento dell'attività padre. È possibile specificare un'azione di dipendenza per sostituire il comportamento predefinito ed eseguire attività quando l'attività padre non riesce. Per informazioni dettagliate, vedere la sezione [Azioni di dipendenza](#dependency-actions).  

È possibile creare attività che dipendono da altre attività in una relazione uno-a-uno o uno-a-molti. È anche possibile creare una dipendenza da un intervallo, in cui un'attività dipende dal completamento di un gruppo di attività all'interno di un intervallo di ID attività specifico. È possibile combinare questi tre scenari di base per creare relazioni molti-a-molti.

## <a name="task-dependencies-with-batch-net"></a>Relazioni tra attività con Batch .NET
Questo articolo illustra la configurazione di relazioni tra attività tramite la libreria [Batch .NET][net_msdn]. Viene illustrato prima come [abilitare le dipendenze tra attività](#enable-task-dependencies) nei processi, quindi viene spiegato come [configurare un'attività con dipendenze](#create-dependent-tasks). Viene inoltre descritto come specificare un'azione di dipendenza per l'esecuzione di attività dipendenti se l'attività padre non riesce. Vengono infine illustrati gli [scenari delle relazione](#dependency-scenarios) supportate da Batch.

## <a name="enable-task-dependencies"></a>Abilitare le relazioni tra attività
Per usare le dipendenze delle attività nell'applicazione Batch, è innanzitutto necessario configurare il processo per l'uso delle dipendenze. In Batch .NET abilitare la funzionalità in [CloudJob][net_cloudjob] impostando la rispettiva proprietà [UsesTaskDependencies][net_usestaskdependencies] su `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Nel frammento di codice precedente "batchClient" è un'istanza della classe [BatchClient][net_batchclient].

## <a name="create-dependent-tasks"></a>Creare attività dipendenti
Per creare un'attività che dipende dal completamento di una o più attività padre, è possibile specificare che l'attività "dipende" dalle altre attività. In Batch .NET configurare la proprietà [CloudTask][net_cloudtask].[DependsOn][net_dependson] con un'istanza della classe [TaskDependencies][net_taskdependencies]:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Questo frammento di codice crea un'attività dipendente con ID attività "Flowers". L'attività "Flowers" dipende dalle attività "Rain" e "Sun". L'esecuzione dell'attività "Flowers" in un nodo di calcolo verrà pianificata solo dopo il corretto completamento delle attività "Rain" e "Sun".

> [!NOTE]
> Un'attività viene considerata correttamente completata quando l'attività è in **stato completato** e il **codice di uscita** è `0`. In Batch .NET ciò corrisponde a un valore della proprietà [CloudTask][net_cloudtask].[State][net_taskstate] pari a `Completed` e il valore della proprietà [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] è `0`.
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
> Negli esempi di questa sezione un'attività dipendente viene eseguita solo dopo che le attività padre vengono completate correttamente. Questo comportamento è quello predefinito per un'attività dipendente. È possibile eseguire un'attività dipendente dopo che un'attività padre non riesce specificando un'azione di dipendenza per sostituire il comportamento predefinito. Per informazioni dettagliate, vedere la sezione [Azioni di dipendenza](#dependency-actions).

### <a name="one-to-one"></a>Uno-a-uno
In una relazione uno-a-uno un'attività dipende dal corretto completamento di una sola attività padre. Per creare la dipendenza, specificare un solo ID attività nel metodo statico [TaskDependencies][net_taskdependencies].[OnId][net_onid] quando si popola la proprietà [DependsOn][net_dependson] di [CloudTask][net_cloudtask].

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
In una relazione uno-a-molti un'attività dipende dal completamento di più attività padre. Per creare la dipendenza, specificare una raccolta di ID attività nel metodo statico [TaskDependencies][net_taskdependencies].[OnId][net_onids] quando si popola la proprietà [DependsOn][net_dependson] di [CloudTask][net_cloudtask].

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
In una dipendenza da un intervallo di attività padre un'attività dipende dal completamento delle attività il cui ID è compreso all'interno di un intervallo.
Per creare la dipendenza, specificare il primo e l'ultimo ID attività dell'intervallo nel metodo statico [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] quando si popola la proprietà [DependsOn][net_dependson] di [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Quando si usano intervalli di ID attività per le relazioni, gli ID attività inclusi nell'intervallo *devono* essere rappresentazioni di stringa di valori interi.
> 
> Ogni attività compresa nell'intervallo deve soddisfare la dipendenza attraverso il corretto completamento o il completamento con un errore associato a un'azione di dipendenza impostata su **Satisfy**. Per informazioni dettagliate, vedere la sezione [Azioni di dipendenza](#dependency-actions).
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

## <a name="dependency-actions"></a>Azioni di dipendenza

Per impostazione predefinita, un'attività o un set di attività dipendenti vengono eseguite solo dopo il corretto completamento di un'attività padre. In alcuni scenari potrebbe essere necessario eseguire attività dipendenti anche se l'attività padre non riesce. È possibile sostituire il comportamento predefinito specificando un'azione di dipendenza. Un'azione di dipendenza specifica se un'attività dipendente è idonea per l'esecuzione in base alla riuscita o meno dell'attività padre. 

Si supponga, ad esempio, un'attività dipendente in attesa di dati dal completamento dell'attività upstream. Se l'attività upstream non riesce, l'attività dipendente potrebbe comunque essere eseguita usando dati meno recenti. In questo caso, un'azione di dipendenza può specificare che l'attività dipendente è idonea per l'esecuzione nonostante l'errore dell'attività padre.

Un'azione di dipendenza è basata su una condizione di uscita per l'attività padre. È possibile specificare un'azione di dipendenza per una qualsiasi delle condizioni di uscita seguenti. Per .NET, vedere la classe [ExitConditions][net_exitconditions] per altre informazioni.

- Quando si verifica un errore di pre-elaborazione.
- Quando si verifica un errore di caricamento dei file. Se l'attività si chiude con un codice di uscita specificato tramite **exitCodes** o **exitCodeRanges** e quindi si verifica un errore di caricamento dei file, l'azione specificata dal codice di uscita ha la precedenza.
- Quando l'attività termina con un codice di uscita definito dalla proprietà **ExitCodes**.
- Quando l'attività termina con un codice di uscita compreso in un intervallo specificato dalla proprietà **ExitCodeRanges**.
- Il caso predefinito, ovvero se l'attività si chiude con un codice di uscita non definito da **ExitCodes** o **ExitCodeRanges** oppure se l'attività si chiude con un errore di pre-elaborazione e la proprietà **PreProcessingError** non è stata configurata o se l'attività ha esito negativo con un errore di caricamento dei file e la proprietà **FileUploadError** non è stata configurata. 

Per specificare un'azione di dipendenza in .NET, impostare la proprietà [ExitOptions][net_exitoptions].[DependencyAction][net_dependencyaction] per la condizione di uscita. La proprietà **DependencyAction** accetta uno di questi due valori:

- L'impostazione della proprietà **DependencyAction** su **Satisfy** indica che le attività dipendenti sono idonee per l'esecuzione se l'attività padre termina con un errore specificato.
- L'impostazione della proprietà **DependencyAction** su **Block** indica che le attività dipendenti non sono idonee per l'esecuzione.

L'impostazione predefinita per la proprietà **DependencyAction** è **Satisfy** per il codice di uscita 0 e **Block** per tutte le altre condizioni di uscita.

Il frammento di codice seguente imposta la proprietà **DependencyAction** per un'attività padre. Se l'attività padre termina con un errore di pre-elaborazione o con i codici di errore specificati, l'attività dipendente viene bloccata. Se l'attività padre termina con qualsiasi altro errore diverso da zero, l'attività dipendente è idonea per l'esecuzione.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Esempio di codice
Il progetto di esempio [TaskDependencies][github_taskdependencies] è uno degli [esempi di codice di Azure Batch][github_samples] disponibili in GitHub. Questa soluzione di Visual Studio mostra:

- Come abilitare la dipendenza delle attività in un processo
- Come creare attività che dipendono da altre attività
- Come eseguire queste attività in un pool di nodi di calcolo.

## <a name="next-steps"></a>Passaggi successivi
### <a name="application-deployment"></a>Distribuzione dell'applicazione
La funzionalità [Pacchetti dell'applicazione](batch-application-packages.md) di Batch offre un modo semplice per distribuire e controllare le versioni delle applicazioni eseguite dalle attività nei nodi di calcolo.

### <a name="installing-applications-and-staging-data"></a>Installazione delle applicazioni e staging dei dati
Per una panoramica dei metodi di preparazione dei nodi per l'esecuzione di attività, vedere [Installing applications and staging data on Batch compute nodes][forum_post] (Installazione di applicazioni e staging dei dati nei nodi di calcolo di Batch) nel forum di Azure Batch. Scritto da uno dei membri del team di Azure Batch, questo post è una panoramica utile dei diversi modi disponibili per copiare applicazioni, dati di input di attività e altri file nei nodi di calcolo.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions#Microsoft_Azure_Batch_ExitOptions_DependencyAction
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

