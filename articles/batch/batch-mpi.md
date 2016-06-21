<properties
	pageTitle="Eseguire applicazioni MPI in Azure Batch con attività a istanze multiple | Microsoft Azure"
	description="Informazioni su come eseguire applicazioni MPI (Message Passing Interface) usando il tipo di attività a istanze multiple in Azure Batch."
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
	ms.date="06/03/2016"
	ms.author="marsma" />

# Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch

Le attività a istanze multiple permettono di eseguire un'attività di Azure Batch in più nodi di calcolo contemporaneamente per abilitare scenari high performance computing, ad esempio le applicazioni MPI (Message Passing Interface). In questo articolo si apprenderà come eseguire attività a istanze multiple usando la libreria [Batch .NET][api_net].

## Panoramica sulle attività a istanze multiple

In Batch ogni attività viene in genere eseguita in un singolo nodo di calcolo, si inviano più attività a un processo e il servizio Batch pianifica l'esecuzione di ogni attività in un nodo. Tuttavia, configurando le **impostazioni per istanze multiple** di un'attività è possibile indicare a Batch di dividere l'attività in sottoattività per l'esecuzione in più nodi.

![Panoramica sulle attività a istanze multiple][1]

Quando si invia a un processo un'attività con impostazioni per istanze multiple, Batch esegue diversi passaggi relativi esclusivamente alle attività a istanze multiple:

1. Il servizio Batch suddivide automaticamente l'attività in un'attività **primaria** e varie **sottoattività**. Pianifica quindi l'attività primaria e le sottoattività per l'esecuzione nei nodi di calcolo del pool.
2. L'attività primaria e le sottoattività scaricano gli eventuali **file di risorse comuni** specificati nelle impostazioni per istanze multiple.
3. Dopo aver scaricato i file di risorse comuni, l'attività primaria e le sottoattività eseguono il **comando di coordinamento** specificato nelle impostazioni per istanze multiple. Il comando di coordinamento viene usato, in genere, per avviare un servizio in background, ad esempio `smpd.exe` di [Microsoft MPI][msmpi_msdn], e consente di verificare che i nodi siano pronti per l'elaborazione di messaggi tra i nodi.
4. Dopo l'esecuzione del comando di coordinamento da parte dell'attività primaria e di tutte le sottoattività, la **riga di comando**, anche detta "comando applicazione", dell'attività a istanze multiple viene eseguita *esclusivamente* dall'**attività primaria**. Ad esempio, in una soluzione basata su [Microsoft MPI][msmpi_msdn], qui viene eseguita l'applicazione abilitata per MPI tramite `mpiexec.exe`.

> [AZURE.NOTE] Anche se distinta a livello funzionale, l'attività a istanze multiple non è un tipo di attività univoco come ad esempio [StartTask][net_starttask] o [JobPreparationTask][net_jobprep]. Si tratta semplicemente di un'attività Batch Standard, [CloudTask][net_task] in Batch .NET, per cui sono state configurate le impostazioni per istanze multiple. In questo articolo viene definita **attività a istanze multiple**.

## Requisiti delle attività a istanze multiple

Per le attività a istanze multiple è necessario un pool in cui sia **abilitata la comunicazione tra i nodi** e **disabilitata l'esecuzione di attività simultanee**. Se si prova a eseguire un'attività a istanze multiple in un pool con la comunicazione tra i nodi disabilitata o con un valore *maxTasksPerNode* maggiore di 1, l'attività non viene pianificata e rimane nello stato "attivo" per un periodo illimitato. Questo frammento di codice illustra la creazione di un pool di questo tipo usando la libreria Batch .NET.

```csharp
CloudPool myCloudPool =
	myBatchClient.PoolOperations.CreatePool(
		poolId: "MultiInstanceSamplePool",
		targetDedicated: 3
		virtualMachineSize: "small",
		cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Le attività a istanze multiple verranno eseguite *soltanto* nei nodi all'interno di **pool creati dopo il 14 dicembre 2015**.

> [AZURE.TIP] Quando si usano [nodi di calcolo con dimensioni A8 o A9](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) nel pool Batch, l'applicazione MPI può sfruttare i vantaggi legati alle prestazioni elevate e alla latenza ridotta della rete con Accesso diretto a memoria remota (RDMA) di Azure. Per l'elenco completo delle dimensioni dei nodi di calcolo disponibili per i pool di Batch, vedere [Dimensioni dei servizi cloud](./../cloud-services/cloud-services-sizes-specs.md).

### Usare StartTask per l'installazione dell'applicazione MPI

Per eseguire applicazioni MPI con un'attività a istanze multiple, prima di tutto è necessario installare il software MPI nei nodi di calcolo del pool. Questo è il momento giusto per usare un oggetto [StartTask][net_starttask], che viene eseguito ogni volta che un nodo viene aggiunto a un pool o viene riavviato. Questo frammento di codice crea un oggetto StartTask che specifica il pacchetto di installazione di MS-MPI come [file di risorse][net_resourcefile] e la riga di comando che verrà eseguita dopo il download del file di risorse nel nodo.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

> [AZURE.NOTE] Quando si implementa una soluzione MPI con attività a istanze multiple in Batch non si è obbligati a usare MS-MPI. È possibile usare qualsiasi implementazione dello standard MPI che sia compatibile con il sistema operativo specificato per i nodi di calcolo nel pool.

## Creare un'attività a istanze multiple con Batch .NET

Ora che sono stati illustrati i requisiti del pool e l'installazione del pacchetto MPI, è possibile passare alla creazione dell'attività a istanze multiple. In questo frammento di codice viene creato un oggetto [CloudTask][net_task] standard e viene quindi configurata la relativa proprietà [MultiInstanceSettings][net_multiinstance_prop]. Come specificato in precedenza, l'attività a istanze multiple non è un tipo di attività distinto ma un'attività Batch Standard configurata con impostazioni per istanze multiple.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## Attività primaria e sottoattività

Quando si creano le impostazioni per istanze multiple per un'attività, è necessario specificare il numero di nodi di calcolo che devono eseguire l'attività. Quando si invia l'attività a un processo, il servizio Batch crea un'attività **primaria** e un numero sufficiente di **sottoattività** che, insieme, corrispondono al numero di nodi specificato.

Alle attività viene assegnato un ID intero compreso tra 0 e *numberOfInstances - 1*. L'attività con ID 0 è quella primaria, tutti gli altri ID corrispondono a sottoattività. Ad esempio, se si creano le impostazioni per istanze multiple seguenti per un'attività, l'attività primaria avrà l'ID 0 e le sottoattività avranno un ID compreso tra 1 e 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

## Comando di coordinamento e comando applicazione

Il **comando di coordinamento** viene eseguita sia dall'attività primaria che dalle sottoattività. Al termine dell'esecuzione del comando di coordinamento da parte dell'attività primaria e di tutte le sottoattività, la riga di comando dell'attività a istanze multiple viene eseguita *solo* dall'attività primaria. La riga di comando viene definita **comando applicazione** per distinguerla dal comando di coordinamento.

La chiamata del comando di coordinamento blocca l'esecuzione del comando applicazione da parte del servizio Batch fino a quando il comando di coordinamento non viene restituito per tutte le sottoattività. Il comando di coordinamento deve quindi avviare eventuali servizi in background necessari, verificare che siano pronti per l'uso e chiudersi. Ad esempio, questo comando di coordinamento per una soluzione con MS-MPI versione 7 avvia il servizio SMPD nel nodo e quindi viene chiuso:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Si noti che l'uso di `start` in questo comando di coordinamento è necessario perché l'applicazione `smpd.exe` non viene restituita immediatamente dopo l'esecuzione. Senza l'uso del comando [start][cmd_start], questo comando di coordinamento non verrebbe restituito e bloccherebbe l'esecuzione del comando applicazione.

Il **comando applicazione**, vale a dire la riga di comando specificata per l'attività a istanze multiple, viene eseguito *solo* dall'attività primaria. Per le applicazioni MS-MPI, si tratta dell'esecuzione dell'applicazione abilitata per MPI con `mpiexec.exe`. Di seguito è riportato, a titolo di esempio, il comando applicazione per una soluzione con MS-MPI versione 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

## File di risorse

Sono disponibili due set di file di risorse da prendere in considerazione per le attività a istanze multiple: **file di risorse comuni**, scaricati da *tutte le attività*, sia da quella primaria che dalle sottoattività, e **file di risorse specifici** per la stessa attività a istanze multiple, scaricati *solo dall'attività primaria*.

È possibile specificare uno o più **file di risorse comuni** nelle impostazioni per istanze multiple relative a un'attività. Questi file di risorse comuni vengono scaricati da [Archiviazione di Azure](./../storage/storage-introduction.md) dall'attività primaria e da tutte le sottoattività nella directory condivisa dell'attività di ogni nodo. È possibile accedere alla directory condivisa dell'attività dalle righe del comando applicazione e del comando di coordinamento usando la variabile di ambiente `AZ_BATCH_TASK_SHARED_DIR`.

I file di risorse specificati per l'attività a istanze multiple stessa vengono scaricati nella directory di lavoro dell'attività, `AZ_BATCH_TASK_WORKING_DIR`, *solo* dall'attività primaria, le sottoattività non scaricano i file di risorse specificati per l'attività a istanze multiple.

Il contenuto di `AZ_BATCH_TASK_SHARED_DIR` è accessibile dall'attività primaria e da tutte le sottoattività eseguite in un nodo. Un esempio di directory condivisa dell'attività è `tasks/mybatchjob/job-1/mymultiinstancetask/`. Anche l'attività primaria e le singole sottoattività hanno una directory di lavoro, accessibile unicamente dalla relativa attività con la variabile di ambiente `AZ_BATCH_TASK_WORKING_DIR`.

Si noti che negli esempi di codice in questo articolo non vengono specificati i file di risorse per l'attività a istanze multiple stessa, ma solo per l'oggetto StartTask del pool e l'oggetto [CommonResourceFiles][net_multiinsance_commonresfiles] delle impostazioni per istanze multiple.

> [AZURE.IMPORTANT] Usare sempre le variabili di ambiente `AZ_BATCH_TASK_SHARED_DIR` e `AZ_BATCH_TASK_WORKING_DIR` per fare riferimento a tali directory nelle righe di comando. Evitare di provare a costruire i percorsi manualmente.

## Durata dell'attività

Dalla durata dell'attività principale dipende la durata dell'intera attività a istanze multiple. Quando viene chiusa l'attività primaria, vengono terminate tutte le sottoattività. Il codice di uscita dell'attività primaria è il codice di uscita dell'attività e viene quindi usato per determinare l'esito positivo o negativo dell'attività per la ripetizione dei tentativi.

Se una delle sottoattività ha esito negativo e viene chiusa con un codice restituito diverso da zero, ad esempio, l'intera attività a istanze multiple ha esito negativo. L'attività a istanze multiple viene quindi terminata e la sua esecuzione verrà ripetuta fino a raggiungere il limite di tentativi.

Quando si elimina un'attività a istanze multiple, il servizio Batch elimina anche l'attività primaria e tutte le sottoattività. Dai nodi di calcolo vengono eliminate tutte le directory delle sottoattività e i relativi file, come avviene per un'attività standard.

I [TaskConstraints][net_taskconstraints] per un'attività a istanze multiple, come le proprietà [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock] e [RetentionTime][net_taskconstraint_retention], vengono rispettati come per un'attività standard e si applicano all'attività primaria e a tutte le sottoattività. Tuttavia, se si modifica la proprietà [RetentionTime][net_taskconstraint_retention] dopo aver aggiunto l'attività a istanze multiple al processo, la modifica viene applicata solo all'attività primaria. Tutte le sottoattività continueranno a usare la proprietà [RetentionTime][net_taskconstraint_retention] originale.

Se l'attività recente faceva parte di un'attività a istanze multiple, nell'elenco delle attività recenti di un nodo di calcolo è incluso l'ID di una sottoattività.

## Ottenere informazioni sulle sottoattività

Per ottenere informazioni sulle sottoattività usando la libreria Batch .NET, chiamare il metodo [CloudTask.ListSubtasks][net_task_listsubtasks]. Questo metodo restituisce informazioni su tutte le sottoattività e sul nodo di calcolo che ha eseguito le attività. Queste informazioni permettono di determinare la directory radice di ogni sottoattività, l'ID del pool, lo stato corrente, il codice di uscita e altro ancora. È possibile usare queste informazioni in combinazione con il metodo [PoolOperations.GetNodeFile][poolops_getnodefile] per ottenere i file della sottoattività. Si noti che questo metodo non restituisce informazioni per l'attività primaria, con ID 0.

> [AZURE.NOTE] Se non diversamente specificato, i metodi Batch .NET che operano sull'attività a istanze multiple [CloudTask][net_task] stessa si applicano *solo* all'attività primaria. Ad esempio, quando si chiama il metodo [CloudTask.ListNodeFiles][net_task_listnodefiles] in un'attività a istanze multiple, vengono restituiti solo i file dell'attività primaria.

Il frammento di codice seguente illustra come ottenere informazioni sulle sottoattività e come richiedere il contenuto dei file dai nodi in cui vengono eseguiti.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
			await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## Passaggi successivi

- Si consiglia di compilare una semplice applicazione MS-MPI da usare per il test delle attività a istanze multiple in Batch. L'articolo relativo alla [compilazione ed esecuzione di un semplice programma MS-MPI][msmpi_howto], nel blog del team Microsoft HPC & Azure Batch, contiene una procedura dettagliata per la creazione di una semplice applicazione MPI con MS-MPI.

- Per informazioni aggiornate su MS-MPI, vedere la pagina relativa a [Microsoft MPI][msmpi_msdn] in MSDN.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Panoramica sulle istanze multiple"

<!---HONumber=AcomDC_0608_2016-->