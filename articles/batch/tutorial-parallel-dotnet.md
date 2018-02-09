---
title: Eseguire un carico di lavoro parallelo - .NET in Azure Batch
description: Esercitazione - Transcodificare file multimediali in parallelo con ffmpeg in Azure Batch usando la libreria client .NET di Batch
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: 
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 1100f8fddcd2f802b5f38e0b9789bc9ec359e03a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Esercitazione: Eseguire un carico di lavoro parallelo con Azure Batch usando l'API .NET

Usare Azure Batch per eseguire in modo efficiente processi batch paralleli e HPC (High Performance Computing) su larga scala in Azure. Questa esercitazione illustra un esempio C# di esecuzione di un carico di lavoro parallelo usando Batch. Vengono fornite informazioni su un flusso di lavoro dell'applicazione Batch comune e su come interagire a livello di codice con le risorse di Archiviazione e Batch. Si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un pacchetto dell'applicazione all'account Batch
> * Eseguire l'autenticazione con gli account di archiviazione e Batch
> * Caricare i file di input nella risorsa di archiviazione
> * Creare un pool di nodi di calcolo per eseguire un'applicazione
> * Creare un processo e le attività per elaborare i file di input
> * Monitorare l'esecuzione delle attività
> * Recuperare i file di output

In questa esercitazione file multimediali MP4 vengono convertiti in parallelo in formato MP3 usando lo strumento open source [ffmpeg](http://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

* [IDE di Visual Studio](https://www.visualstudio.com/vs) (Visual Studio 2015 o una versione più recente). 

* Un account Batch e un account di archiviazione per utilizzo generico collegato. Per creare questi account, vedere le guide introduttive di Batch usando il [portale di Azure](quick-create-portal.md) o l'[interfaccia della riga di comando di Azure](quick-create-cli.md).

* [Versione per Windows 64 bit di ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip) (file ZIP). Scaricare il file ZIP nel computer locale. Per questa esercitazione è necessario solo il file ZIP. Non è necessario decomprimere il file o installarlo in locale. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).


## <a name="add-an-application-package"></a>Aggiungere un pacchetto dell'applicazione

Usare il portale di Azure per aggiungere ffmpeg all'account Batch come [pacchetto dell'applicazione](batch-application-packages.md). I pacchetti dell'applicazione aiutano a gestire le applicazioni di attività e la loro distribuzione nei nodi di calcolo del pool. 

1. Nel portale di Azure fare clic su **Altri servizi** > **Account Batch** e quindi fare clic sul nome dell'account Batch.
3. Fare clic su **Applicazioni** > **Aggiungi**.
4. Per **ID applicazione** immettere *ffmpeg* e come versione del pacchetto specificare *3.4*. Selezionare il file ZIP ffmpeg scaricato in precedenza e quindi fare clic su **OK**. Il pacchetto dell'applicazione ffmpeg viene aggiunto all'account Batch.

![Aggiungere il pacchetto dell'applicazione](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Scaricare ed eseguire l'esempio

### <a name="download-the-sample"></a>Scaricare l'esempio

[Scaricare o clonare l'app di esempio](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) da GitHub. Per clonare il repository dell'app di esempio con un client Git, usare il comando seguente:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Passare alla directory contenente il file di soluzione Visual Studio `BatchDotNetFfmpegTutorial.sln`.

Aprire il file di soluzione in Visual Studio e aggiornare le stringhe delle credenziali in `program.cs` con i valori ottenuti per gli account. Ad esempio: 

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

Assicurarsi inoltre che il riferimento al pacchetto dell'applicazione ffmpeg nella soluzione corrisponda all'ID e alla versione del pacchetto ffmpeg caricato nell'account Batch.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "3.4";
```
### <a name="build-and-run-the-sample-project"></a>Compilare ed eseguire il progetto di esempio

* In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Compila soluzione**. 

* Confermare il ripristino di eventuali pacchetti NuGet, se richiesto. Se è necessario scaricare i pacchetti mancanti, verificare che lo strumento [Gestione pacchetti NuGet](https://docs.nuget.org/consume/installing-nuget) sia installato.

Eseguire quindi lo strumento. Quando si esegue l'applicazione di esempio, l'output della console è simile al seguente. Durante l'esecuzione si verifica una pausa in corrispondenza di `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` mentre vengono avviati i nodi di calcolo del pool. 

```
Sample start: 12/12/2017 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 12/12/2017 3:29:36 PM
Elapsed time: 00:09:14.3418742
```


Passare all'account Batch nel portale di Azure per monitorare il pool, i nodi di calcolo, il processo e le attività. Per visualizzare, ad esempio, una mappa termica dei nodi di calcolo nel pool, fare clic su **Pool** > *WinFFmpegPool*.

Quando le attività sono in esecuzione, la mappa termica è simile all'esempio seguente:

![Mappa termica del pool](./media/tutorial-parallel-dotnet/pool.png)


Quando si esegue l'applicazione con la configurazione predefinita, il tempo di esecuzione tipico è di circa **10 minuti**. La maggior parte del tempo è necessaria per la creazione del pool.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Esaminare il codice

Nelle sezioni seguenti si esamineranno in dettaglio i singoli passaggi eseguiti dall'applicazione di esempio per l'elaborazione di un carico di lavoro nel servizio Batch. Fare riferimento alla soluzione aperta in Visual Studio mentre si legge la parte restante di questo articolo, perché non vengono illustrate tutte le righe di codice dell'esempio.

### <a name="authenticate-blob-and-batch-clients"></a>Autenticare i client BLOB e Batch

Per interagire con l'account di archiviazione collegato, l'app usa la libreria client di Archiviazione di Azure per .NET. Crea un riferimento all'account con [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) ed esegue l'autenticazione usando l'autenticazione con chiave condivisa. Crea quindi un oggetto [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient).

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

L'app crea un oggetto [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) per creare e gestire pool, processi e attività nel servizio Batch. Il client Batch nell'esempio usa l'autenticazione con chiave condivisa. Batch supporta anche l'autenticazione tramite [Azure Active Directory](batch-aad-auth.md), per autenticare singoli utenti o un'applicazione eseguita automaticamente.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Caricare i file di input

L'app passa l'oggetto `blobClient` al metodo `CreateContainerIfNotExist` per creare un contenitore di archiviazione per i file di input (formato MP4) e un contenitore per l'output dell'attività.

```csharp
  CreateContainerIfNotExist(blobClient, inputContainerName;
  CreateContainerIfNotExist(blobClient, outputContainerName);
```

I file vengono quindi caricati nel contenitore di input dalla cartella `InputFiles` locale. I file nel contenitore di archiviazione sono definiti come oggetti [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) di Batch che successivamente Batch può scaricare nei nodi di calcolo. 

Per il caricamento dei file vengono usati due metodi in `Program.cs`:

* `UploadResourceFilesToContainer`: restituisce una raccolta di oggetti ResourceFile e chiama internamente `UploadResourceFileToContainer` per caricare ogni file passato nel parametro `filePaths`.
* `UploadResourceFileToContainer`: carica ogni file come BLOB nel contenitore di input. Dopo il caricamento del file, ottiene una firma di accesso condiviso per il BLOB e restituisce un oggetto ResourceFile che lo rappresenta. 

```csharp
  List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(@"..\..\InputFiles", "*.mp4",
      SearchOption.TopDirectoryOnly));

  List<ResourceFile> inputFiles = UploadResourceFilesToContainer(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Per informazioni dettagliate sul caricamento di file come BLOB in un account di archiviazione con .NET, vedere [Introduzione all'archivio BLOB di Azure con .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="create-a-pool-of-compute-nodes"></a>Creare un pool di nodi di calcolo

L'esempio crea quindi un pool di nodi di calcolo nell'account Batch con una chiamata a `CreatePoolIfNotExist`. Questo metodo definito usa il metodo [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) per impostare il numero di nodi, le dimensioni delle VM e una configurazione del pool. In questo caso, un oggetto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) specifica un oggetto [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) che fa riferimento a un'immagine di Windows Server pubblicata in Azure Marketplace. Batch supporta una vasta gamma di immagini di VM in Azure Marketplace, oltre che immagini di VM personalizzate.

Il numero di nodi e le dimensioni delle VM vengono impostati usando costanti definite. Batch supporta nodi dedicati e [nodi per priorità bassa](batch-low-pri-vms.md) e nei pool è possibile usare uno di questi tipi o entrambi. I nodi dedicati sono riservati per il pool. I nodi per priorità bassa vengono offerti a un prezzo ridotto usando la capacità in eccesso delle VM in Azure. I nodi per priorità bassa non sono disponibili se Azure non ha capacità sufficiente. L'esempio crea per impostazione predefinita un pool che contiene solo 5 nodi per priorità bassa con dimensioni *Standard_A1_v2*. 

L'applicazione ffmpeg viene distribuita nei nodi di calcolo aggiungendo un oggetto [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) alla configurazione del pool. 

Il metodo [Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) invia il pool al servizio Batch.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2012-R2-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,                                                
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

pool.Commit();  
```

### <a name="create-a-job"></a>Creare un processo

Un processo Batch specifica un pool in cui eseguire le attività e impostazioni facoltative, ad esempio una priorità e una pianificazione per il lavoro. L'esempio crea un processo con una chiamata a `CreateJobIfNotExist`. Questo metodo definito usa il metodo [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) per creare un processo nel pool. 

Il metodo [Commit](/dotnet/api/microsoft.azure.batch.cloudjob.commit) invia il processo al servizio Batch. Inizialmente il processo è privo di attività.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = JobId;
    job.PoolInformation = new PoolInformation { PoolId = PoolId };

job.Commit();        
```

### <a name="create-tasks"></a>Creare le attività

L'esempio crea le attività del processo con una chiamata al metodo `AddTasks`, che crea un elenco di oggetti [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask). Ogni oggetto `CloudTask` esegue ffmpeg per elaborare un oggetto `ResourceFile` di input usando una proprietà [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline). Lo strumento ffmpeg è stato installato in precedenza in ogni nodo al momento della creazione del pool. In questo caso, la riga di comando esegue ffmpeg per convertire ogni file (video) MP4 di input in un file (audio) MP3.

L'esempio crea un oggetto [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) per il file MP3 dopo l'esecuzione della riga di comando. I file di output di ogni attività, in questo caso uno, vengono caricati in un contenitore nell'account di archiviazione collegato, usando la proprietà [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles) dell'attività.

L'esempio aggiunge quindi le attività al processo con il metodo [AddTask](/dotnet/api/microsoft.azure.batch.joboperations.addtask), che le accoda per l'esecuzione nei nodi di calcolo. 

```csharp
for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-3.4-win64-static\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line) 
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };
   

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
batchClient.JobOperations.AddTask(jobId, tasks);
```

### <a name="monitor-tasks"></a>Monitorare le attività

Quando Batch aggiunge attività a un processo, il servizio le accoda automaticamente e ne pianifica l'esecuzione nei nodi di calcolo nel pool associato. In base alle impostazioni specificate, Batch gestisce tutte le operazioni di accodamento, pianificazione, ripetizione di tentativi e tutte le altre operazioni di amministrazione relative alle attività. 

Sono disponibili molti approcci al monitoraggio dell'esecuzione delle attività. Questo esempio definisce un metodo `MonitorTasks` che segnala solo gli stati di completamento ed esito positivo o negativo dell'attività. Il codice `MonitorTasks` specifica un oggetto [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) per selezionare in modo efficiente solo informazioni minime sulle attività. Crea quindi un oggetto [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor), che fornisce utilità helper per il monitoraggio degli stati delle attività. In `MonitorTasks` l'esempio attende che tutte le attività raggiungano uno stato `TaskState.Completed` entro un limite di tempo. Termina quindi il processo e segnala le attività che sono state completate ma per cui potrebbe essersi verificato un errore, ad esempio un codice di uscita diverso da zero.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    batchClient.Utilities.CreateTaskStateMonitor().WaitAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId, failureMessage);
    Console.WriteLine(failureMessage);
}
batchClient.JobOperations.TerminateJob(jobId, successMessage);
...

```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esecuzione delle attività, l'app elimina automaticamente il contenitore di archiviazione di input creato e consente di scegliere se eliminare il processo e il pool di Batch. Le classi [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) e [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) di BatchClient hanno entrambe metodi di eliminazione corrispondenti, che vengono chiamati se l'utente conferma l'eliminazione. Anche se non vengono addebitati costi per i processi e per le attività, vengono invece addebiti costi per i nodi di calcolo. È quindi consigliabile allocare solo i pool necessari. Quando si elimina il pool, tutto l'output delle attività nei nodi viene eliminato. I file di input e output rimangono tuttavia nell'account di archiviazione.

Quando non sono più necessari, eliminare il gruppo di risorse, l'account Batch e l'account di archiviazione. A tale scopo, nel portale di Azure selezionare il gruppo di risorse per l'account Batch e fare clic su **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso a:

> [!div class="checklist"]
> * Aggiungere un pacchetto dell'applicazione all'account Batch
> * Eseguire l'autenticazione con gli account di archiviazione e Batch
> * Caricare i file di input nella risorsa di archiviazione
> * Creare un pool di nodi di calcolo per eseguire un'applicazione
> * Creare un processo e le attività per elaborare i file di input
> * Monitorare l'esecuzione delle attività
> * Recuperare i file di output

Per altri esempi di uso dell'API .NET per pianificare ed elaborare i carichi di lavoro di Batch, vedere gli esempi su GitHub.

> [!div class="nextstepaction"]
> [Esempi C# per Batch](https://github.com/Azure/azure-batch-samples/tree/master/CSharp)
