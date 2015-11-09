<properties
	pageTitle="Preparazione e pulizia del processo in Batch | Microsoft Azure"
	description="Usare le attività di preparazione a livello di processo per ridurre al minimo il trasferimento dei dati ai nodi di calcolo di Azure Batch e le attività di rilascio per la pulizia del nodo al completamento del processo."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/15/2015"
	ms.author="v-marsma"/>

# Eseguire attività di preparazione e completamento di processi in nodi di calcolo di Azure Batch

I processi di Azure Batch spesso richiedono una forma di installazione prima dell'esecuzione e, analogamente, una sorta di manutenzione post-processo al completamento delle attività del processo. Batch fornisce i meccanismi per tali operazioni di preparazione e manutenzione sotto forma di attività facoltative di *preparazione del processo* e *rilascio del processo*.

L'attività di preparazione del processo viene eseguita su tutti i nodi di calcolo pianificati per l'esecuzione di attività, prima dell'esecuzione di qualsiasi attività di un processo. Dopo aver completato il processo, viene eseguita l'attività di rilascio del processo in ogni nodo nel pool che ha eseguito almeno un'attività. Le attività di preparazione e rilascio dei processi consentono di specificare una riga di comando da eseguire quando l'attività viene richiamata e offrono funzionalità quali download del file, esecuzione con privilegi elevati, variabili di ambiente personalizzate, durata di esecuzione massima, numero di nuovi tentativi e periodo di memorizzazione di file.

Nelle sezioni seguenti viene descritto come usare questi due particolari tipi di attività mediante le classi [JobPreparationTask][net_job_prep] e [JobReleaseTask][net_job_release] nell'API [.NET di Batch][api_net].

> [AZURE.TIP]Le attività di preparazione e rilascio dei processi sono particolarmente utili in ambienti con "pool condivisi": ossia ambienti in cui un pool di nodi di calcolo viene mantenuto durante l'esecuzione di un processo e viene condiviso tra più processi.

## Quando usare le attività di preparazione e rilascio dei processi

Le attività di preparazione e rilascio dei processi risultano utili in numerose situazioni, tra cui le seguenti:

- **Trasferimento di dati di attività comuni**: i processi di Batch spesso richiedono un set comune di dati come input per le attività del processo. Ad esempio, nei calcoli di analisi dei rischi giornalieri, i dati di mercato sono specifici del processo ma comuni a tutte le attività del processo. Tali dati di mercato, spesso di grandi dimensioni, devono essere scaricati una sola volta da ogni nodo di calcolo in modo che ogni attività che viene eseguita su un nodo possa utilizzarli. Usare un'*attività di preparazione del processo* per scaricare i dati in ogni nodo prima dell'esecuzione di altre attività.
- **Eliminazione dei dati del processo**: in un ambiente di pool condiviso in cui i nodi di calcolo del pool non vengono rimossi tra i processi, l'eliminazione dei dati di processo tra le esecuzioni può essere necessaria per risparmiare spazio su disco sui nodi o per soddisfare i criteri di sicurezza di un'organizzazione. Usare un'*attività di rilascio del processo* per eliminare i dati scaricati da un'attività di preparazione del processo o generati durante l'esecuzione dell'attività.
- **Memorizzazione del log**: è possibile conservare una copia dei file di log generati dalle attività o dei file dump di arresto anomalo generati da errori nelle applicazioni. In questi casi, usare un'*attività di rilascio del processo* per comprimere e caricare dati in un account [di Archiviazione di Azure][azure_storage].

## Attività di preparazione del processo

Prima dell'esecuzione delle attività di un processo, viene eseguita l'attività di preparazione del processo su ogni nodo di calcolo pianificato per l'esecuzione di un'attività. Per impostazione predefinita, il servizio di Batch attenderà il completamento dell'attività di preparazione del processo prima di eseguire le attività pianificate sul nodo, ma è possibile configurare il servizio affinché venga annullata la fase di attesa. L'attività di preparazione del processo verrà eseguita nuovamente su un nodo di calcolo se questo viene riavviato, ma è anche possibile disabilitare questo comportamento.

L'attività di preparazione del processo viene eseguita solo su nodi pianificati per l'esecuzione di un'attività. Ciò impedisce l'esecuzione non necessaria di un'attività di preparazione se a un nodo non è assegnata alcuna attività, con conseguente riduzione dei costi di trasferimento dei dati. Questa situazione si applica quando il numero di attività per un processo è inferiore al numero di nodi in un pool o quando è abilitata l'[esecuzione di attività simultanee](batch-parallel-node-tasks.md), lasciando alcuni nodi inattivi se il numero di attività è inferiore al totale delle possibili attività simultanee.

> [AZURE.NOTE]La classe [JobPreparationTask][net_job_prep_cloudjob] differisce dalla proprietà [CloudPool.StartTask][pool_starttask] in quanto JobPreparationTask viene eseguita all'inizio di ogni processo, mentre StartTask viene eseguita solo quando un nodo di calcolo viene aggiunto per la prima volta a un pool o viene riavviato.

## Attività di rilascio del processo

Dopo aver completato un processo, viene eseguita l'attività di rilascio del processo in ogni nodo nel pool che ha eseguito almeno un'attività. Un processo viene contrassegnato come completato generando una richiesta di interruzione. Quindi, il servizio di Batch imposta lo stato del processo su *arresto in corso*, termina le attività attive o in esecuzione associate al processo ed esegue l'attività di rilascio del processo. Il processo passa quindi allo stato *completato*.

> [AZURE.NOTE]Anche l'eliminazione del processo esegue l'attività di rilascio del processo. Tuttavia, se un processo è stato terminato in precedenza, l'attività di rilascio non viene eseguita una seconda volta quando tale processo viene successivamente eliminato.

## Attività di preparazione e rilascio dei processi in .NET di Batch

La specifica di un'attività di preparazione del processo viene eseguita creando e configurando una classe [JobPreparationTask][net_job_prep] e assegnando al proprio lavoro la proprietà [CloudJob.JobPreparationTask][net_job_prep_cloudjob]. In modo analogo, inizializzare una classe [JobReleaseTask][net_job_release] e assegnarla alla proprietà [CloudJob.JobReleaseTask][net_job_prep_cloudjob] del processo per impostare l'attività di rilascio del processo.

In questo frammento di codice `myBatchClient` è un'istanza completamente inizializzata di [BatchClient][net_batch_client] e `myPool` è un pool esistente nell'account Batch.

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

Come indicato in precedenza, l'attività di rilascio viene eseguita quando un processo viene concluso o eliminato. La conclusione di un processo con l'API .NET di Batch viene eseguita chiamando [PoolOperations.TerminateJobAsync][net_job_terminate] e l'eliminazione del processo viene eseguita con [PoolOperations.DeleteJobAsync][net_job_delete]. Entrambe le operazioni vengono in genere eseguite quando le attività di un processo vengono completate o viene raggiunto un timeout definito.

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## Passaggi successivi

### Progetto di esempio su GitHub

Esaminare il progetto di esempio [JobPrepRelease][job_prep_release_sample] su GitHub per vedere il funzionamento delle attività di preparazione e rilascio dei processi. Questa applicazione console esegue le operazioni seguenti:

1. Crea un pool con due nodi "small"
2. Crea un processo con attività di preparazione e rilascio di processi e attività standard
3. Esegue un'attività di preparazione del processo che scrive innanzitutto l'ID del nodo in un file di testo in una directory "condivisa" del nodo
4. Esegue un'attività in ogni nodo che scrive il relativo ID attività nello stesso file di testo
5. Dopo aver completato tutte le attività (o aver raggiunto il timeout), stampa i contenuti del file di testo di ciascun nodo nella console
6. Una volta completato il processo, esegue l'attività di rilascio del processo per eliminare il file dal nodo
6. Stampa i codici di uscita delle attività di preparazione e rilascio dei processi per ogni nodo in cui vengono eseguiti
7. Sospende l'esecuzione per consentire la conferma dell'eliminazione dei processi e/o del pool

L'output dell'applicazione di esempio è simile al seguente:

```
Attempting to create pool: JobPrepReleaseSamplePool
The pool already existed when we tried to create it
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-3105992504_1-20151015t150030z:
-------------------------------------------
tvm-3105992504_1-20151015t150030z tasks:
  task001
  task002
  task006
  task007

Contents of shared\job_prep_and_release.txt on tvm-3105992504_2-20151015t150030z:
-------------------------------------------
tvm-3105992504_2-20151015t150030z tasks:
  task003
  task005
  task004
  task008

Waiting for job JobPrepReleaseSampleJob to reach state Completed
....

tvm-3105992504_1-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-3105992504_2-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
no

Sample complete, hit ENTER to exit...
```

### Controllare le attività di preparazione e rilascio del processo con Batch Explorer

[Batch Explorer][batch_explorer_article], disponibile anche nel [repository del codice di esempio][batch_explorer_project] di Batch su GitHub, è uno strumento eccellente da usare per lo sviluppo di soluzioni con Azure Batch. Quando si esegue l'applicazione di esempio precedente, è possibile usare Batch Explorer per visualizzare le proprietà del processo e le relative attività o anche scaricare il file di testo condiviso modificato dalle attività del processo.

L'immagine seguente illustra le proprietà dell'attività di preparazione e rilascio dei processi nel riquadro dei dettagli del processo quando il processo *JobPrepReleaseSampleJob* viene selezionato nella scheda Processi.

![Batch Explorer][1]

*Schermata di Batch Explorer che illustra le attività di preparazione e rilascio dei processi*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_article]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/batchexplorer-01.png
[2]: ./media/batch-job-prep-release/batchexplorer-02.png

<!---HONumber=Nov15_HO1-->