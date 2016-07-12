<properties
	pageTitle="Preparazione e pulizia del processo in Batch | Microsoft Azure"
	description="Usare le attività di preparazione a livello di processo per ridurre al minimo il trasferimento dei dati ai nodi di calcolo di Azure Batch e le attività di rilascio per la pulizia del nodo al completamento del processo."
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
	ms.date="06/22/2016"
	ms.author="marsma" />

# Eseguire attività di preparazione e completamento di processi in nodi di calcolo di Azure Batch

I processi di Azure Batch spesso richiedono che venga eseguito un qualche tipo di installazione prima dell'esecuzione e, analogamente, una sorta di manutenzione post-processo al completamento delle attività del processo. Batch offre i meccanismi per tali operazioni di preparazione e manutenzione sotto forma di attività facoltative di preparazione e rilascio del processo.

Prima dell'esecuzione di qualsiasi attività di un processo, **l'attività di preparazione del processo** viene eseguita in tutti i nodi di calcolo pianificati per l'esecuzione di attività. Al completamento del processo, viene eseguita **l'attività di rilascio del processo** in ogni nodo del pool che ha eseguito almeno un'attività. Come con le normali attività di Batch, è possibile specificare una riga di comando dell'attività di preparazione o rilascio del processo in modo che venga chiamata all'esecuzione dell'attività. Queste attività speciali offrono funzionalità familiari per le attività, quali download di file, esecuzione con privilegi elevati, variabili di ambiente personalizzate, durata massima di esecuzione, numero di tentativi e tempo di conservazione dei file.

Nelle sezioni seguenti viene descritto come usare questi due particolari tipi di attività mediante le classi [JobPreparationTask][net_job_prep] e [JobReleaseTask][net_job_release] nell'API [.NET di Batch][api_net].

> [AZURE.TIP] Le attività di preparazione e rilascio del processo sono particolarmente utili in ambienti con "pool condivisi", ossia ambienti in cui un pool di nodi di calcolo viene mantenuto durante l'esecuzione di un processo e viene condiviso tra più processi.

## Quando usare le attività di preparazione e rilascio dei processi

Ogni volta che è necessario preparare i nodi con una configurazione o con dati specifici per un processo e quindi pulire o rendere permanenti i dati dei risultati, è consigliabile usare attività di preparazione e rilascio del processo. Ecco alcuni esempi di queste situazioni:

**Trasferimento dei dati di attività comuni**

I processi di Batch spesso richiedono un set comune di dati come input per le attività del processo. Ad esempio, nei calcoli di analisi dei rischi giornalieri, i dati di mercato sono specifici del processo ma comuni a tutte le attività del processo. Tali dati di mercato, spesso di grandi dimensioni, devono essere scaricati una sola volta da ogni nodo di calcolo in modo che qualsiasi attività eseguita su un nodo possa usarli. Usare un'**attività di preparazione del processo** per scaricare i dati in ogni nodo prima dell'esecuzione di altre attività del processo.

**Eliminazione dei dati del processo**

In un ambiente di pool condiviso in cui i nodi di calcolo del pool non vengono rimossi tra i processi, potrebbe essere necessario eliminare i dati dei processi tra le esecuzioni, per risparmiare spazio su disco o per soddisfare i criteri di sicurezza dell'organizzazione. Usare un'**attività di rilascio del processo** per eliminare i dati scaricati da un'attività di preparazione del processo o generati durante l'esecuzione dell'attività.

**Conservazione dei log**

È possibile conservare una copia dei file di log generati dalle attività o dei file dump di arresto anomalo generati da errori nelle applicazioni. In questi casi, usare un'**attività di rilascio del processo** per comprimere e caricare dati in un account [di Archiviazione di Azure][azure_storage].

## Attività di preparazione del processo

Prima dell'esecuzione delle attività di un processo, viene eseguita l'attività di preparazione del processo su ogni nodo di calcolo pianificato per l'esecuzione di un'attività. Per impostazione predefinita, il servizio Batch attenderà il completamento dell'attività di preparazione del processo prima di eseguire le attività pianificate per l'esecuzione nel nodo, ma è possibile configurare il servizio affinché venga annullata la fase di attesa. L'attività di preparazione del processo verrà eseguita nuovamente su un nodo di calcolo se questo viene riavviato, ma è anche possibile disabilitare questo comportamento.

L'attività di preparazione del processo viene eseguita solo su nodi pianificati per l'esecuzione di un'attività. Ciò impedisce l'esecuzione di un'attività di preparazione non necessaria nel caso in cui a un nodo non venga assegnata un'attività. Questa situazione può verificarsi quando il numero di attività per un processo è inferiore al numero di nodi in un pool o quando è abilitata l'[esecuzione di attività simultanee](batch-parallel-node-tasks.md). In quest'ultimo caso, alcuni nodi rimangono inattivi se il numero delle attività è inferiore a quello totale delle attività simultanee possibili. Se non si esegue l'attività di preparazione dei processi sui inattivi nodi, è possibile risparmiare sui costi di trasferimento dati.

> [AZURE.NOTE] [JobPreparationTask]La classe [net\_job\_prep\_cloudjob] differisce dalla proprietà [CloudPool.StartTask][pool_starttask] perché JobPreparationTask viene eseguita all'avvio di ogni processo, mentre StartTask viene eseguita solo quando un nodo di calcolo viene aggiunto per la prima volta a un pool o viene riavviato.

## Attività di rilascio del processo

Dopo aver contrassegnato un processo come completato , viene eseguita l'attività di rilascio del processo in ogni nodo del pool che ha eseguito almeno un'attività. Un processo viene contrassegnato come completato generando una richiesta di interruzione. Il servizio Batch imposta quindi lo stato del processo su *arresto in corso*, termina le attività attive o in esecuzione associate al processo ed esegue l'attività di rilascio del processo. Il processo passa quindi allo stato *completato*.

> [AZURE.NOTE] Anche l'eliminazione del processo esegue l'attività di rilascio del processo. Tuttavia, se un processo è già stato terminato, l'attività di rilascio non viene eseguita una seconda volta se tale processo viene eliminato successivamente.

## Attività di preparazione e di rilascio del processo con Batch .NET

Per specificare un'attività di preparazione del processo, creare e configurare un oggetto [JobPreparationTask][net_job_prep] e assegnarlo alla proprietà [CloudJob.JobPreparationTask][net_job_prep_cloudjob] del processo. In modo analogo, inizializzare un oggetto [JobReleaseTask][net_job_release] e assegnarlo alla proprietà [CloudJob.JobReleaseTask][net_job_prep_cloudjob] del processo per impostare l'attività di rilascio del processo.

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

Come indicato in precedenza, l'attività di rilascio viene eseguita quando un processo viene concluso o eliminato. La conclusione di un processo con l'API .NET Batch viene eseguita chiamando [JobOperations.TerminateJobAsync][net_job_terminate]. L'eliminazione di un processo viene eseguita con [JobOperations.DeleteJobAsync][net_job_delete]. Entrambe queste azioni vengono in genere eseguite quando si completano le attività di un processo o quando si raggiunge un timeout definito dall'utente.

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## Esempio di codice in GitHub

Esaminare il progetto di esempio [JobPrepRelease][job_prep_release_sample] su GitHub per vedere il funzionamento delle attività di preparazione e rilascio dei processi. Questa applicazione console esegue le operazioni seguenti:

1. Crea un pool con due nodi "small".
2. Crea un processo con attività di preparazione e rilascio di processi e attività standard.
3. Esegue un'attività di preparazione del processo che scrive innanzitutto l'ID del nodo in un file di testo in una directory "condivisa" del nodo.
4. Esegue un'attività in ogni nodo che scrive il relativo ID attività nello stesso file di testo.
5. Dopo aver completato tutte le attività (o aver raggiunto il timeout), stampa i contenuti del file di testo di ogni nodo nella console.
6. Dopo aver completato il processo, esegue l'attività di rilascio del processo per eliminare il file dal nodo.
6. Stampa i codici di uscita delle attività di preparazione e rilascio dei processi per ogni nodo in cui vengono eseguiti.
7. Sospende l'esecuzione per consentire la conferma dell'eliminazione dei processi e/o del pool.

L'output dell'applicazione di esempio è simile al seguente:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] A causa degli orari variabili di creazione e di inizio per i nodi in un nuovo pool, poiché alcuni nodi sono pronti per le attività prima di altri, è possibile che l'output visualizzato sia diverso. Poiché le attività vengono completate rapidamente, in particolare, è possibile che uno dei nodi del pool esegua tutte le attività del processo. In questo caso, si potrà notare che le attività di preparazione e di rilascio dei processi non esistono per il nodo che non ha eseguito alcuna attività.

### Controllare le attività di preparazione e rilascio del processo nel portale di Azure

Quando si esegue l'applicazione di esempio precedente, è possibile usare il [portale di Azure][portal] per visualizzare le proprietà del processo e le rispettive attività oppure per scaricare il file di testo condiviso modificato dalle attività del processo.

Lo screenshot seguente mostra il pannello **Preparation tasks** (Attività di preparazione) nel portale di Azure dopo un'esecuzione dell'applicazione di esempio. Passare alle proprietà *JobPrepReleaseSampleJob* dopo il completamento delle attività ma prima dell'eliminazione del processo e del pool, quindi fare clic su **Preparation tasks** (Attività di preparazione) o **Release tasks** (Attività di rilascio) per visualizzare le rispettive proprietà.

![Proprietà di preparazione del processo nel portale di Azure][1]

## Passaggi successivi

### Pacchetti dell'applicazione

Oltre all'attività di preparazione del processo, è possibile usare anche la funzionalità [pacchetti dell'applicazione](batch-application-packages.md) di Batch per preparare i nodi di calcolo per l'esecuzione dell'attività. Questa funzionalità è particolarmente utile per la distribuzione di applicazioni che non richiedono l'esecuzione di un programma di installazione, applicazioni che contengono molti file (100+) o applicazioni che richiedono un controllo delle versioni rigoroso.

### Installazione delle applicazioni e staging dei dati

Per una panoramica dei diversi metodi di preparazione dei nodi per l'esecuzione di attività, vedere il post di blog sull'[installazione di applicazioni e sullo staging dei dati nei nodi di calcolo di Batch][forum_post] nel forum di Azure Batch. Scritto da uno dei membri del team di Azure Batch, questo post è una panoramica utile dei diversi modi disponibili per inserire file, inclusi i dati relativi ad applicazioni e input di attività, nei nodi di calcolo e contiene alcune considerazioni specifiche utili per ogni metodo.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/it-IT/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net\_job\_prep\_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
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

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png

<!---HONumber=AcomDC_0629_2016-->