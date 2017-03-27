---
title: "Rendere persistenti l&quot;output di attività e processi - Azure Batch | Documentazione Microsoft"
description: "Informazioni su come usare Archiviazione di Azure come archivio permanente per l&quot;output di attività e processi di Batch e abilitare la visualizzazione di questo output permanente nel portale di Azure."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 3b3aa18eb52993843be1feeb8e0b2a43339413c3
ms.lasthandoff: 03/09/2017


---
# <a name="persist-results-from-completed-jobs-and-tasks-to-azure-storage"></a>Rendere persistenti i risultati di processi e attività completati in Archiviazione di Azure

Le attività eseguite in Batch in genere producono output che deve essere archiviato e quindi recuperato in un secondo momento da altre attività del processo, dall'applicazione client che ha eseguito il processo o entrambe. L'output può essere costituito da file creati dall'elaborazione dei dati di input o da file di log associati all'esecuzione di attività. Questo articolo presenta una libreria di classi .NET che usa una tecnica basata su convenzioni per salvare in modo permanente l'output delle attività nell'archivio BLOB di Azure, rendendolo disponibile anche dopo l'eliminazione di pool, processi e nodi di calcolo.

Con la tecnica descritta in questo articolo, è anche possibile visualizzare l'output delle attività in **Saved output files** (File di output salvati) e **Saved logs** (Log salvati) nel [portale di Azure][portal].

![Selettori di file di output salvati e di log salvati nel portale][1]

> [!NOTE]
> La libreria di classi .NET [Azure Batch File Conventions][nuget_package] illustrata in questo articolo è attualmente in anteprima. Alcune funzionalità descritte in questo articolo potrebbero subire modifiche prima della disponibilità a livello generale.
> 
> 

## <a name="task-output-considerations"></a>Considerazioni sull'output delle attività
Quando si progetta una soluzione Batch, è necessario considerare diversi fattori correlati agli output di processi e attività.

* **Durata dei nodi di calcolo**: questi nodi sono spesso temporanei, in particolare nei pool abilitati per il ridimensionamento automatico. Gli output delle attività eseguite in un nodo sono disponibili solo finché il nodo esiste e solo durante periodo di conservazione dei file impostato per l'attività. Per assicurare che l'output delle attività venga salvato in modo permanente, le attività devono quindi caricare i file di output in una risorsa di archiviazione permanente, ad esempio Archiviazione di Azure.
* **Archiviazione dell'output**: per salvare in modo permanente i dati di output delle attività in un archivio permanente, è possibile usare [Azure Storage SDK](../storage/storage-dotnet-how-to-use-blobs.md) nel codice dell'attività per caricare il relativo output in un contenitore di archiviazione BLOB. Se si implementa un contenitore e una convenzione di denominazione dei file, l'applicazione client o altre attività nel processo possono quindi individuare e scaricare l'output in base alla convenzione.
* **Recupero dell'output**: è possibile recuperare l'output delle attività direttamente dai nodi di calcolo nel pool o da Archiviazione di Azure se le attività salvano in modo permanente il relativo output. Per recuperare l'output di un'attività direttamente da un nodo di calcolo, è necessario il nome del file e il relativo percorso di output nel nodo. Se si salva in modo permanente l'output in Archiviazione di Azure, le attività downstream o l'applicazione client deve avere il percorso completo del file in Archiviazione di Azure per scaricarlo usando Azure Storage SDK.
* **Visualizzazione dell'output**: quando si passa a un'attività di Batch nel portale di Azure e si seleziona **File nel nodo**, vengono visualizzati tutti i file associati all'attività, non solo i file di output a cui si è interessati. Anche in questo caso, i file nei nodi di calcolo sono disponibili solo finché il nodo esiste e solo durante periodo di conservazione dei file impostato per l'attività. Per visualizzare l'output delle attività salvato in modo permanente in Archiviazione di Azure nel portale o in un'applicazione come [Azure Storage Explorer][storage_explorer], è necessario conoscerne la posizione e passare direttamente al file.

## <a name="help-for-persisted-output"></a>Guida per l'output salvato in modo permanente
Per facilitare il salvataggio permanente dell'output di attività e processi, il team di Batch ha definito e implementato un set di convenzioni di denominazione, nonché una libreria di classi .NET, la libreria [Azure Batch File Conventions][nuget_package], che è possibile usare nelle applicazioni Batch. Inoltre, il portale di Azure riconosce queste convenzioni di denominazione, consentendo di trovare facilmente i file archiviati usando la libreria.

## <a name="using-the-file-conventions-library"></a>Uso della libreria di convenzioni dei file
[Azure Batch File Conventions][nuget_package] è una libreria di classi .NET che le applicazioni Batch .NET possono usare per archiviare e recuperare facilmente gli output delle attività in e da Archiviazione di Azure. È destinata all'uso nel codice di attività e nel codice client: nel codice di attività per salvare in modo permanente i file e nel codice client per elencarli e recuperarli. Le attività possono anche usare la libreria per recuperare gli output delle attività upstream, ad esempio in uno scenario di [relazioni tra attività](batch-task-dependencies.md) .

La libreria di convenzioni assicura che i contenitori di archiviazione e i file di output delle attività siano denominati in base alla convenzione e caricati nella posizione corretta quando vengono salvati in modo permanente in Archiviazione di Azure. Quando si recuperano gli output, è possibile individuare facilmente quelli per un determinato processo o un'attività elencandoli o recuperandoli in base all'ID e allo scopo, senza dover conoscere i nomi file o la posizione in cui si trovano nella risorsa di archiviazione.

Ad esempio, è possibile usare la libreria per "elencare tutti i file intermedi per l'attività 7" o per "ottenere l'anteprima per il processo *filmpersonale*", senza dover conoscere i nomi dei file o il percorso all'interno dell'account di archiviazione.

### <a name="get-the-library"></a>Ottenere la libreria
È possibile ottenere la libreria, che contiene nuove classi ed estende le classi [CloudJob][net_cloudjob] and [CloudTask][net_cloudtask] con nuovi metodi, in [NuGet][nuget_package]. La libreria può essere aggiunta al progetto di Visual Studio tramite [NuGet Library Package Manager][nuget_manager].

> [!TIP]
> È possibile trovare il [codice sorgente][github_file_conventions] per la libreria Azure Batch File Conventions in GitHub nel repository Microsoft Azure SDK per .NET.
> 
> 

## <a name="requirement-linked-storage-account"></a>Requisito: account di archiviazione collegato
Per archiviare gli output in una risorsa di archiviazione permanente usando la libreria File Conventions e visualizzarli nel portale di Azure, è necessario [collegare un account di archiviazione di Azure](batch-application-packages.md#link-a-storage-account) all'account Batch. Se non è già stato fatto, collegare un account di archiviazione all'account Batch tramite il portale di Azure:

Pannello **Account Batch** > **Impostazioni** > **Account di archiviazione** > **Account di archiviazione** (nessuno) > Selezionare un account di archiviazione nella sottoscrizione

Per una procedura più dettagliata sul collegamento di un account di archiviazione, vedere [Distribuzione di applicazioni con i pacchetti dell'applicazione di Azure Batch](batch-application-packages.md).

## <a name="persist-output"></a>Salvare in modo permanente l'output
Esistono due azioni principali da eseguire durante il salvataggio dell'output di processi e attività con la libreria File Conventions: creare il contenitore di archiviazione e salvare l'output nel contenitore.

> [!WARNING]
> Poiché tutti gli output di attività e processi vengono archiviati nello stesso contenitore, possono essere applicate [limitazioni dell'archiviazione](../storage/storage-performance-checklist.md#blobs) se un numero elevato di attività prova a salvare in modo permanente i file nello stesso momento.
> 
> 

### <a name="create-storage-container"></a>Creare un contenitore di archiviazione
Prima che le attività inizino a salvare in modo permanente l'output nella risorsa di archiviazione, è necessario creare un contenitore di archiviazione BLOB in cui verrà caricato il relativo output. A questo scopo, chiamare [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync]. Questo metodo di estensione accetta un oggetto [CloudStorageAccount][net_cloudstorageaccount] come parametro e crea un contenitore denominato in modo che il relativo contenuto sia individuabile dal portale di Azure e dai metodi di recupero descritti più avanti in questo articolo.

In genere si inserisce questo codice nell'applicazione client, ovvero l'applicazione che crea i pool, i processi e le attività.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Archiviare gli output delle attività
Dopo avere preparato un contenitore nell'archiviazione BLOB, l'output può essere salvato nel contenitore usando la classe [TaskOutputStorage][net_taskoutputstorage] disponibile nella libreria File Conventions.

Nel codice dell'attività creare un oggetto [TaskOutputStorage][net_taskoutputstorage] e quindi, dopo che l'attività ha completato tutte le relative operazioni, chiamare il metodo [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync] per salvarne l'output in Archiviazione di Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Il parametro "output kind" consente di classificare i file salvati in modo permanente. Esistono quattro tipi [TaskOutputKind][net_taskoutputkind] predefiniti, ovvero "TaskOutput", "TaskPreview", "TaskLog" e "TaskIntermediate". È anche possibile definire tipi personalizzati, se sono utili per il flusso di lavoro.

Questi tipi di output consentono di specificare il tipo di output da elencare, quando in seguito si eseguono query su Batch per visualizzare gli output salvati in modo permanente per una determinata attività. In altre parole, quando si elencano gli output per un'attività, è possibile filtrare l'elenco in base a uno dei tipi di output. Ad esempio, "Scaricare l'output di *anteprima* per l'attività *109*." Altre informazioni su come elencare e recuperare gli output sono disponibili in [Recuperare l'output](#retrieve-output) più avanti nell'articolo.

> [!TIP]
> Il tipo di output indica anche dove viene visualizzato un file specifico nel portale di Azure. *TaskOutput*: i file classificati vengono visualizzati in "File di output delle attività" e i file di *TaskLog* vengono visualizzati in "Task logs" (Log delle attività).
> 
> 

### <a name="store-job-outputs"></a>Archiviare gli output del processo
Oltre ad archiviare gli output di un'attività, è possibile archiviare gli output associato a un intero processo. Ad esempio, nell'attività di unione di un processo di rendering di un filmato, è possibile salvare in modo permanente l'intero filmato sottoposto a rendering come output del processo. Una volta completato il processo, l'applicazione client può semplicemente elencare e recuperare gli output del processo senza dover eseguire query sulle singole attività.

Per archiviare l'output del processo, chiamare il metodo [JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] e specificare [JobOutputKind][net_joboutputkind] e il nome file:

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Come con TaskOutputKind per gli output di un'attività, usare il parametro [JobOutputKind][net_joboutputkind] per classificare i file salvati in modo permanente di un processo. Questo parametro consente di eseguire query in seguito, per ottenere l'elenco di un tipo specifico di output. JobOutputKind include tipi di output e di anteprima e supporta la creazione di tipi personalizzati.

### <a name="store-task-logs"></a>Archiviare i log delle attività
Oltre a salvare un file in una risorsa di archiviazione permanente quando un'attività o un processo viene completato, può essere necessario salvare in modo permanente i file aggiornati durante l'esecuzione di un'attività, ad esempio i file di log o `stdout.txt` e `stderr.txt`. A questo scopo, nella libreria Azure Batch File Conventions è disponibile il metodo [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync]. Con [SaveTrackedAsync][net_savetrackedasync] è possibile tenere traccia degli aggiornamenti a un file nel nodo (in base a un intervallo specificato) e salvare in modo permanente gli aggiornamenti in Archiviazione di Azure.

Nel frammento di codice seguente viene usato [SaveTrackedAsync][net_savetrackedasync] per aggiornare `stdout.txt` in Archiviazione di Azure ogni 15 secondi durante l'esecuzione dell'attività:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)` è semplicemente un segnaposto per il codice eseguito normalmente da un'attività. Ad esempio, potrebbe essere disponibile codice che scarica i dati da Archiviazione di Azure ed esegue un calcolo o una trasformazione dei dati. Questo frammento di codice è importante perché dimostra come è possibile eseguire il wrapping di tale codice in un blocco `using` per aggiornare periodicamente un file con [SaveTrackedAsync][net_savetrackedasync].

`Task.Delay` è necessario alla fine di questo blocco `using` per assicurarsi che l'agente del nodo abbia tempo di scaricare i contenuti dell'output standard nel file stdout.txt del nodo. L'agente del nodo è un programma che viene eseguito in ogni nodo del pool e fornisce l'interfaccia di comando e controllo tra il nodo e il servizio Batch. Senza questo ritardo, è possibile perdere gli ultimi secondi dell'output. Questo ritardo potrebbe non essere necessario per tutti i file.

> [!NOTE]
> Quando si abilita il rilevamento file con SaveTrackedAsync, solo le *aggiunte* al file rilevato vengono salvate in modo permanente in Archiviazione di Azure. Usare questo metodo solo per il rilevamento dei file di log non a rotazione o altri file aggiunti, ovvero i dati vengono aggiunti solo alla fine del file quando questo viene aggiornato.
> 
> 

## <a name="retrieve-output"></a>Recuperare l'output
Quando si recupera l'output salvato in modo permanente con la libreria Azure Batch File Conventions, si esegue questa operazione con un approccio incentrato su attività e processo. È possibile richiedere l'output per un'attività o un processo specifico senza dover conoscere il percorso nell'archivio BLOB, né il nome file. È possibile semplicemente specificare "Scaricare i file di output per l'attività *109*".

Il frammento di codice seguente esegue l'iterazione in tutte le attività del processo, stampa alcune informazioni sui file di output per l'attività e quindi scarica i file dalla risorsa di archiviazione.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Output delle attività e il portale di Azure
Il portale di Azure visualizza gli output e i log di un'attività salvati in modo permanente in un account di archiviazione di Azure collegato usando le convenzioni di denominazione disponibili nel file [Azure Batch File Conventions README][github_file_conventions_readme]. È possibile implementare queste convenzioni nel linguaggio preferito o usare la libreria File Conventions nelle applicazioni .NET.

### <a name="enable-portal-display"></a>Abilitare la visualizzazione del portale
Per abilitare la visualizzazione degli output nel portale, è necessario soddisfare i requisiti seguenti:

1. [Collegare un account di archiviazione di Azure](#requirement-linked-storage-account) all'account Batch.
2. Rispettare le convenzioni di denominazione predefinite per i contenitori di archiviazione e i file durante il salvataggio in modo permanente degli output. È possibile trovare la definizione di queste convenzioni nel file [README][github_file_conventions_readme] della libreria File Conventions. Se si usa la libreria [Azure Batch File Conventions][nuget_package] per salvare in modo permanente l'output, questo requisito è soddisfatto.

### <a name="view-outputs-in-the-portal"></a>Visualizzare gli output nel portale
Per visualizzare gli output delle attività e i log nel portale di Azure, passare all'attività di cui si vuole visualizzare l'output, quindi fare clic su **File di output salvati** o **Log salvati**. L'immagine illustra l'opzione **ile di output salvato** per l'attività con ID "007":

![Pannello dei file di output delle attività nel portale di Azure][2]

## <a name="code-sample"></a>Esempio di codice
Il progetto di esempio [PersistOutputs][github_persistoutputs] è uno degli [esempi di codice di Azure Batch][github_samples] disponibili in GitHub. Questa soluzione di Visual Studio descrive come usare la libreria Azure Batch File Conventions per salvare in modo permanente l'output dell'attività in una risorsa di archiviazione permanente. Per eseguire l'esempio, seguire questa procedura:

1. Aprire il progetto in **Visual Studio 2015 o in una versione più recente**.
2. Aggiungere **le credenziali dell'account** di archiviazione e Batch a **AccountSettings.settings** nel progetto Microsoft.Azure.Batch.Samples.Common.
3. **Compilare** , ma non eseguire, la soluzione. Se richiesto, ripristinare tutti i pacchetti NuGet.
4. Usare il portale di Azure per caricare un [pacchetto dell'applicazione](batch-application-packages.md) per **PersistOutputsTask**. Includere `PersistOutputsTask.exe` e relativi assembly dipendenti nel pacchetto ZIP, impostare l'ID applicazione su "PersistOutputsTask" e la versione del pacchetto dell'applicazione su "1.0".
5. **Avviare**, ovvero eseguire, il progetto **PersistOutputs**.

## <a name="next-steps"></a>Passaggi successivi
### <a name="application-deployment"></a>Distribuzione dell'applicazione
La funzionalità [Pacchetti dell'applicazione](batch-application-packages.md) di Batch offre un modo semplice per distribuire e controllare le versioni delle applicazioni eseguite dalle attività nei nodi di calcolo.

### <a name="installing-applications-and-staging-data"></a>Installazione delle applicazioni e staging dei dati
Per una panoramica delle diverse modalità di preparazione dei nodi per l'esecuzione di attività, vedere il post di blog sull'[installazione di applicazioni e staging dei dati nei nodi di calcolo di Batch][forum_post] nel forum di Azure Batch. Scritto da uno dei membri del team di Azure Batch, questo post è una panoramica utile dei diversi modi disponibili per inserire file, inclusi i dati relativi ad applicazioni e input di attività, nei nodi di calcolo e contiene alcune considerazioni specifiche per ogni metodo.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Selettori di file di output salvati e di log salvati nel portale"
[2]: ./media/batch-task-output/task-output-02.png "Pannello dei file di output delle attività nel portale di Azure"

