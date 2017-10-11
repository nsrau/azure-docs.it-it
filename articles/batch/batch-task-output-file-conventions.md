---
title: "Rendere persistente l'output di processi e attività in Archiviazione di Azure con la libreria File Conventions per .NET - Azure Batch | Microsoft Docs"
description: "Informazioni su come usare la libreria Azure Batch File Conventions per .NET per rendere persistente l'output di attività e processi di Batch in Archiviazione di Azure e visualizzare l'output reso persistente nel portale di Azure."
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
ms.date: 06/16/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a9de327c20463469bc91d9720aa17333a36f919e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net-to-persist"></a>Rendere persistenti i dati di attività e processi in Archiviazione di Azure con la libreria Batch File Conventions per .NET 

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Un modo per rendere persistenti i dati consiste nell'usare la [libreria Azure Batch File Conventions per .NET][nuget_package]. La libreria File Conventions semplifica il processo di archiviazione dei dati di output delle attività in Archiviazione di Azure e il relativo recupero. È possibile usare la libreria File Conventions sia nel codice di attività che nel codice client: nel codice di attività per rendere persistenti i file e nel codice client per elencarli e recuperarli. È anche possibile usare la libreria nel codice di attività per recuperare l'output delle attività upstream, ad esempio in uno scenario di [dipendenze tra attività](batch-task-dependencies.md). 

Per recuperare i file di output con la libreria File Conventions, è possibile individuare i file per un determinato processo o un'attività elencandoli in base a ID e scopo. Non è necessario conoscere i nomi o i percorsi dei file. Ad esempio, è possibile usare la libreria File Conventions per elencare tutti i file intermedi per una determinata attività o per ottenere un file di anteprima per un determinato processo.

> [!TIP]
> A partire dalla versione 2017-05-01, l'API del servizio Batch supporta la persistenza dei dati di output in Archiviazione di Azure per le attività e le attività di gestione processo eseguite sui pool creati con la configurazione della macchina virtuale. L'API del servizio Batch mette a disposizione un modo semplice per rendere persistente l'output dall'interno del codice che crea un'attività e rappresenta un'alternativa alla libreria File Conventions. È possibile modificare le applicazioni client del servizio Batch in modo che rendano persistente l'output senza dover aggiornare l'applicazione eseguita dall'attività. Per altre informazioni, vedere [Rendere persistenti i dati di attività in Archiviazione di Azure con l'API del servizio Batch](batch-task-output-files.md).
> 
> 

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Quando è appropriato usare la libreria File Conventions per rendere persistente l'output delle attività?

Il servizio Azure Batch offre diversi modi per rendere persistente l'output delle attività. La libreria File Conventions è la scelta ottimale per questi scenari:

- È possibile modificare facilmente il codice per l'applicazione eseguita dall'attività per rendere persistenti i file usando la libreria File Conventions.
- Si vogliono inviare flussi di dati in Archiviazione di Azure mentre l'attività è ancora in esecuzione.
- Si vogliono rendere persistenti i dati dai pool creati con la configurazione del servizio cloud o con la configurazione della macchina virtuale.
- L'applicazione client o altre attività nel processo devono individuare e scaricare i file di output delle attività in base all'ID o allo scopo. 
- Si vuole visualizzare l'output delle attività nel portale di Azure.

Se lo scenario è diverso da quelli sopra elencati, potrebbe essere necessario prendere in considerazione un approccio diverso. Per altre informazioni sulle opzioni per rendere persistente l'output delle attività, vedere [Rendere persistente l'output di processi e attività](batch-task-output.md). 

## <a name="what-is-the-batch-file-conventions-standard"></a>Che cos'è lo standard Batch File Conventions?

Lo [standard Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) fornisce uno schema di denominazione per i percorsi di contenitori e BLOB di destinazione in cui vengono scritti i file di output. I file resi persistenti in Archiviazione di Azure e conformi allo standard File Conventions sono automaticamente disponibili per la visualizzazione nel portale di Azure. Il portale riconosce la convenzione di denominazione e pertanto può visualizzare i file che rispettano tale convenzione.

La libreria File Conventions per .NET assegna automaticamente i nomi ai contenitori di archiviazione e ai file di output delle attività in base allo standard File Conventions. La libreria File Conventions fornisce anche metodi per eseguire query sui file di output in Archiviazione di Azure in base all'ID del processo, all'ID dell'attività o allo scopo.   

Se si sviluppa con un linguaggio diverso da .NET, è possibile implementare autonomamente lo standard File Conventions nell'applicazione. Per altre informazioni, vedere [Informazioni sullo standard Batch File Conventions](batch-task-output.md#about-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Collegare un account di archiviazione di Azure all'account Batch

Per rendere persistenti i dati di output in Archiviazione di Azure usando la libreria File Conventions, è prima necessario collegare un account di Archiviazione di Azure all'account Batch. Se non è già stato fatto, collegare un account di archiviazione all'account Batch tramite il [portale di Azure](https://portal.azure.com):

1. Passare all'account Batch nel portale di Azure. 
2. In **Impostazioni** selezionare **Account di archiviazione**.
3. Se non è già disponibile un account di archiviazione associato all'account Batch, fare clic su **Account di archiviazione (nessuno)**.
4. Selezionare un account di archiviazione nell'elenco per la sottoscrizione corrente. Per prestazioni ottimali, usare un account di Archiviazione di Azure nella stessa area dell'account Batch in cui si eseguono le attività.

## <a name="persist-output-data"></a>Rendere persistenti i dati di output

Per rendere persistenti i dati di output di processi e attività con la libreria File Conventions, creare un contenitore in Archiviazione di Azure e quindi salvare l'output nel contenitore. Usare la [libreria client di Archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage) nel codice dell'attività per caricare l'output dell'attività nel contenitore. 

Per altre informazioni sull'uso di contenitori e BLOB in Archiviazione di Azure, vedere [Introduzione all'archiviazione BLOB di Azure con .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Tutti gli output di processi e attività resi persistenti con la libreria File Conventions vengono archiviati nello stesso contenitore. Se un numero elevato di attività tenta di rendere persistenti i file nello stesso momento, potrebbero essere applicate [limitazioni dell'archiviazione](../storage/common/storage-performance-checklist.md#blobs).
> 
> 

### <a name="create-storage-container"></a>Creare un contenitore di archiviazione

Per rendere persistente l'output delle attività in Archiviazione di Azure, creare innanzitutto un contenitore chiamando [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync]. Questo metodo di estensione accetta un oggetto [CloudStorageAccount] [ net_cloudstorageaccount] come parametro. Crea un contenitore denominato in base allo standard File Conventions, in modo che il relativo contenuto sia individuabile dal portale di Azure e dai metodi di recupero descritti più avanti in questo articolo.

In genere si inserisce il codice per creare un contenitore nell'applicazione client, ovvero l'applicazione che crea i pool, i processi e le attività.

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

Dopo avere preparato un contenitore in Archiviazione di Azure, le attività possono salvare l'output nel contenitore usando la classe [TaskOutputStorage][net_taskoutputstorage] disponibile nella libreria File Conventions.

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

Il parametro `kind` del metodo [TaskOutputStorage](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx).[SaveAsync](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx) consente di categorizzare i file persistenti. Esistono quattro tipi [TaskOutputKind][net_taskoutputkind] predefiniti: `TaskOutput`, `TaskPreview`, `TaskLog` e `TaskIntermediate.` È anche possibile definire categorie personalizzate di output.

Questi tipi di output consentono di specificare il tipo di output da elencare, quando in seguito si eseguono query su Batch per visualizzare gli output salvati in modo permanente per una determinata attività. In altre parole, quando si elencano gli output per un'attività, è possibile filtrare l'elenco in base a uno dei tipi di output. Ad esempio, "Scaricare l'output di *anteprima* per l'attività *109*." Altre informazioni su come elencare e recuperare gli output sono disponibili in [Recuperare l'output](#retrieve-output) più avanti nell'articolo.

> [!TIP]
> Il tipo di output determina anche dove viene visualizzato un file specifico nel portale di Azure. *TaskOutput*: i file categorizzati vengono visualizzati in **File di output delle attività** e i file di *TaskLog* vengono visualizzati in **Task logs** (Log delle attività).
> 
> 

### <a name="store-job-outputs"></a>Archiviare gli output del processo

Oltre ad archiviare gli output di un'attività, è possibile archiviare gli output associato a un intero processo. Ad esempio, nell'attività di unione di un processo di rendering di un filmato, è possibile salvare in modo permanente l'intero filmato sottoposto a rendering come output del processo. Una volta completato il processo, l'applicazione client può elencare e recuperare gli output del processo senza dover eseguire query sulle singole attività.

Per archiviare l'output del processo, chiamare il metodo [JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] e specificare [JobOutputKind][net_joboutputkind] e il nome file:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Come con il tipo **TaskOutputKind** per gli output di un'attività, usare il tipo [JobOutputKind][net_joboutputkind] per categorizzare i file persistenti di un processo. Questo parametro consente di eseguire query in seguito, per ottenere l'elenco di un tipo specifico di output. Il tipo **JobOutputKind** include sia categorie di output che di anteprima e supporta la creazione di categorie personalizzate.

### <a name="store-task-logs"></a>Archiviare i log delle attività

Oltre a rendere persistente un file in una risorsa di archiviazione permanente quando un'attività o un processo viene completato, può essere necessario rendere persistenti i file aggiornati durante l'esecuzione di un'attività, ad esempio i file di log o `stdout.txt` e `stderr.txt`. A questo scopo, nella libreria Azure Batch File Conventions è disponibile il metodo [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync]. Con [SaveTrackedAsync][net_savetrackedasync] è possibile tenere traccia degli aggiornamenti a un file nel nodo (in base a un intervallo specificato) e salvare in modo permanente gli aggiornamenti in Archiviazione di Azure.

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

La sezione impostata come commento `Code to process data and produce output file(s)` è un segnaposto per il codice eseguito normalmente da un'attività. Ad esempio, potrebbe essere disponibile codice che scarica i dati da Archiviazione di Azure ed esegue un calcolo o una trasformazione dei dati. Questo frammento di codice è importante perché dimostra come è possibile eseguire il wrapping di tale codice in un blocco `using` per aggiornare periodicamente un file con [SaveTrackedAsync][net_savetrackedasync].

L'agente del nodo è un programma in esecuzione in ogni nodo del pool e fornisce l'interfaccia di comando e controllo tra il nodo e il servizio Batch. La chiamata `Task.Delay` è necessaria alla fine di questo blocco `using` per garantire che l'agente del nodo abbia tempo sufficiente per scaricare il contenuto dell'output standard nel file stdout.txt nel nodo. Senza questo ritardo, è possibile perdere gli ultimi secondi dell'output. Questo ritardo potrebbe non essere necessario per tutti i file.

> [!NOTE]
> Quando si abilita il rilevamento file con **SaveTrackedAsync**, solo le *aggiunte* al file rilevato vengono rese persistenti in Archiviazione di Azure. Usare questo metodo solo per il rilevamento dei file di log non a rotazione o altri file in cui le scritture vengono eseguite con operazioni di aggiunta alla fine del file.
> 
> 

## <a name="retrieve-output-data"></a>Recuperare i dati di output

Quando si recupera l'output salvato in modo permanente con la libreria Azure Batch File Conventions, si esegue questa operazione con un approccio incentrato su attività e processo. È possibile richiedere l'output per un'attività o un processo specifico senza dover conoscere il percorso in Archiviazione di Azure, né il nome di file. In alternativa, è possibile richiedere i file di output in base all'ID dell'attività o del processo.

Il frammento di codice seguente esegue l'iterazione delle attività di un processo, stampa alcune informazioni sui file di output per l'attività e quindi scarica i file dalla risorsa di archiviazione.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
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

## <a name="view-output-files-in-the-azure-portal"></a>Visualizzare i file di output nel portale di Azure

Il portale di Azure visualizza gli output e i log di un'attività resi persistenti in un account di archiviazione di Azure collegato usando lo [standard Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). È possibile implementare queste convenzioni nel linguaggio preferito o usare la libreria File Conventions nelle applicazioni .NET.

Per abilitare la visualizzazione dei file di output nel portale, è necessario soddisfare i requisiti seguenti:

1. [Collegare un account di archiviazione di Azure](#requirement-linked-storage-account) all'account Batch.
2. Rispettare le convenzioni di denominazione predefinite per i contenitori di archiviazione e i file durante il salvataggio in modo permanente degli output. È possibile trovare la definizione di queste convenzioni nel file [LEGGIMI][github_file_conventions_readme] della libreria File Conventions. Se si usa la libreria [Azure Batch File Conventions][nuget_package] per rendere persistente l'output, i file vengono resi persistenti in base allo standard File Conventions.

Per visualizzare i file di output delle attività e i log nel portale di Azure, passare all'attività di cui si vuole visualizzare l'output, quindi fare clic su **File di output salvati** o **Log salvati**. L'immagine illustra l'opzione **ile di output salvato** per l'attività con ID "007":

![Pannello dei file di output delle attività nel portale di Azure][2]

## <a name="code-sample"></a>Esempio di codice

Il progetto di esempio [PersistOutputs][github_persistoutputs] è uno degli [esempi di codice di Azure Batch][github_samples] disponibili in GitHub. Questa soluzione di Visual Studio descrive come usare la libreria Azure Batch File Conventions per salvare in modo permanente l'output dell'attività in una risorsa di archiviazione permanente. Per eseguire l'esempio, seguire questa procedura:

1. Aprire il progetto in **Visual Studio 2015 o in una versione più recente**.
2. Aggiungere **le credenziali dell'account** di archiviazione e Batch a **AccountSettings.settings** nel progetto Microsoft.Azure.Batch.Samples.Common.
3. **Compilare** , ma non eseguire, la soluzione. Se richiesto, ripristinare tutti i pacchetti NuGet.
4. Usare il portale di Azure per caricare un [pacchetto dell'applicazione](batch-application-packages.md) per **PersistOutputsTask**. Includere `PersistOutputsTask.exe` e relativi assembly dipendenti nel pacchetto ZIP, impostare l'ID applicazione su "PersistOutputsTask" e la versione del pacchetto dell'applicazione su "1.0".
5. **Avviare**, ovvero eseguire, il progetto **PersistOutputs**.
6. Quando viene richiesto di scegliere la tecnologia di persistenza da usare per l'esecuzione dell'esempio, immettere **1** per eseguire l'esempio con la libreria File Conventions per rendere persistente l'output dell'attività. 

## <a name="next-steps"></a>Passaggi successivi

### <a name="get-the-batch-file-conventions-library-for-net"></a>Ottenere la libreria Batch File Conventions per .NET

La libreria Batch File Conventions per .NET è disponibile in [NuGet][nuget_package]. La libreria estende le classi [CloudJob][net_cloudjob] e [CloudTask][net_cloudtask] con nuovi metodi. Vedere anche la [documentazione di riferimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) per la libreria File Conventions.

Il [codice sorgente][github_file_conventions] per la libreria File Conventions è disponibile in GitHub nel repository Microsoft Azure SDK per .NET. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Esplorare altri approcci per rendere persistenti i dati di output

- Per una panoramica delle opzioni per rendere persistenti i dati di processi e attività, vedere [Rendere persistente l'output di processi e attività](batch-task-output.md).
- Vedere [Rendere persistenti i dati di attività in Archiviazione di Azure con l'API del servizio Batch](batch-task-output-files.md) per scoprire come usare l'API del servizio Batch per rendere persistenti i dati di output.

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
