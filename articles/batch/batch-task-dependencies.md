<properties
	pageTitle="Relazioni tra attività in Azure Batch | Microsoft Azure"
	description="È possibile creare attività che dipendono dal completamento corretto di altre attività per l'elaborazione di carichi di lavoro di tipo MapReduce e carichi di lavoro Big Data simili in Azure Batch."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="06/29/2016"
	ms.author="marsma" />

# Relazioni tra attività in Azure Batch

Se si vuole elaborare un carico di lavoro di calcolo di tipo MapReduce sul cloud, è presente un processo di elaborazione dei dati, le cui attività possono essere espresse come grafo aciclico diretto o un qualsiasi altro processo in cui le attività downstream dipendono dall'output delle attività upstream, è possibile usare la funzionalità relazioni tra attività di Azure Batch.

Questa funzionalità consente di creare attività pianificate per l'esecuzione nei nodi di calcolo solo dopo il completamento corretto di una o più attività. È ad esempio possibile creare un processo per il rendering di ogni frame di un film 3D con attività parallele separate e la cui attività finale, ovvero un'attività di unione, unisce i frame con rendering nel filmato competo, solo dopo l'esecuzione corretta del rendering di tutti i frame.

È possibile creare attività che dipendono da altre attività in una relazione uno-a-uno o una relazione uno-a-molti oppure una relazione tra intervalli, in cui un'attività dipende dal completamento corretto di un gruppo di attività entro un intervallo specifico di ID attività. È possibile combinare questi tre scenari di base per creare relazioni molti-a-molti.

## Relazioni tra attività con Batch .NET

In questo articolo viene illustrata la configurazione di relazioni tra attività tramite la raccolta [Batch .NET][net_msdn]. Viene illustrato prima di tutto come [abilitare le relazioni tra attività](#enable-task-dependency) nei processi, quindi viene spiegato brevemente come [configurare un'attività con relazioni](#create-dependent-tasks). Vengono infine illustrati gli [scenari di relazione](#dependency-scenarios) supportate da Batch.

## Abilitare le relazioni tra attività

Per usare le relazioni tra attività nell'applicazione Batch, è prima di tutto necessario indicare al servizio Batch che il processo userà relazioni tra attività. In Batch .NET abilitare la funzionalità in [CloudJob][net_cloudjob] impostando la rispettiva proprietà [UsesTaskDependencies][net_usestaskdependencies] su `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Nel frammento di codice precedente "batchClient" è un'istanza della classe [BatchClient][net_batchclient].

## Creare attività dipendenti

Per creare un'attività che dipende dal completamento corretto di una o più attività, si indica a Batch che l'attività "dipende" dalle altre attività. In Batch .NET configurare la proprietà [CloudTask][net_cloudtask].[DependsOn][net_dependson] con un'istanza della classe [TaskDependencies][net_taskdependencies]\:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Questo frammento di codice crea un'attività con ID "Flowers" che verrà pianificata per l'esecuzione su un nodo di calcolo solo dopo il completamento corretto delle attività con ID "Rain" e "Sun".

 > [AZURE.NOTE] Un'attività viene considerata come completata correttamente quando lo stato dell'attività è **completed** e il **codice di uscita** è `0`. In Batch .NET ciò corrisponde a un valore di proprietà [CloudTask][net_cloudtask].[State][net_taskstate] pari a `Completed` e il valore della proprietà [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] di CloudTask è `0`.

## Scenari di relazioni tra attività

In Azure Batch è possibile usare tre scenari di relazioni tra attività di base, ovvero uno-a-uno, uno-a-molti e la relazione tra intervalli di ID. Questi scenari possono essere combinati per ottenere un quarto scenario, ovvero molti-a-molti.

 Scenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Esempio | |
 :-------------------: | ------------------- | -------------------
 [Uno-a-uno](#one-to-one) | L'*attivitàB* dipende dall'*attivitàA* <p/> L'*attivitàB* sarà pianificata per l'esecuzione solo dopo il completamento corretto dell'*attivitàA*. | ![Diagramma: relazione uno-a-uno tra attività][1]
 [Uno-a-molti](#one-to-many) | L'*attivitàC* dipende dall'*attivitàA* e dall'*attivitàB* <p/> L'*attivitàC* sarà pianificata per l'esecuzione solo dopo il completamento corretto dell'*attivitàA* e *attivitàB*. | ![Diagramma: relazione uno-a-molti tra attività][2]
 [Intervallo di ID attività](#task-id-range) | L'*attivitàD* dipende da un intervallo di attività <p/> L'*attivitàD* sarà pianificata per l'esecuzione solo dopo il completamento corretto delle attività con ID compresi tra *1* e *10*. | ![Diagramma: relazione tra intervalli di ID attività][3]

>[AZURE.TIP] È possibile creare relazioni **molti-a-molti**, ad esempio relazioni in cui le attività C, D, E e F dipendono dalle attività A e B. Questo tipo di relazione risulta utile, ad esempio, negli scenari di pre-elaborazione parallelizzata, in cui le attività downstream dipendono dall'output di più attività upstream.

## Uno-a-uno

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

## Uno-a-molti

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

## Intervallo di ID attività

Per creare un'attività che dipende dal corretto completamento di un gruppo di attività, i cui ID rientrano in un intervallo specifico, è necessario fornire il primo e l'ultimo ID attività dell'intervallo al metodo statico [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] quando si popola la proprietà [DependsOn][net_dependson] di [CloudTask][net_cloudtask].

>[AZURE.IMPORTANT] Quando si usano intervalli di ID attività per le relazioni, gli ID attività inclusi nell'intervallo *devono* essere **rappresentazioni di stringa** di **valori interi**. **Ogni attività nell'intervallo**, inoltre, deve essere completata correttamente per consentire la pianificazione per l'esecuzione dell'attività dipendente.

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

## Esempio di codice

Il progetto di esempio [TaskDependencies][github_taskdependencies] è uno degli [esempi di codice di Azure Batch][github_samples] su GitHub. Questa soluzione di Visual Studio 2015 illustra come abilitare le relazioni tra attività in un processo, come creare attività dipendenti da altre attività e come eseguire tali attività in un pool di nodi di calcolo.

## Passaggi successivi

### Distribuzione dell'applicazione

La funzionalità [pacchetti dell'applicazione](batch-application-packages.md) di Batch fornisce un modo semplice per distribuire e controllare le versioni delle applicazioni eseguite dalle attività nei nodi di calcolo.

### Installazione delle applicazioni e staging dei dati

Per una panoramica dei diversi metodi di preparazione dei nodi per l'esecuzione di attività, vedere il post di blog sull'[installazione di applicazioni e sullo staging dei dati nei nodi di calcolo di Batch][forum_post] nel forum di Azure Batch. Scritto da uno dei membri del team di Azure Batch, questo post è una panoramica utile dei diversi modi disponibili per inserire file, inclusi i dati relativi ad applicazioni e input di attività, nei nodi di calcolo e contiene alcune considerazioni specifiche utili per ogni metodo.

[forum_post]: https://social.msdn.microsoft.com/Forums/it-IT/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
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

<!---HONumber=AcomDC_0629_2016-->