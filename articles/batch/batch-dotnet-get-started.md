<properties
	pageTitle="Esercitazione: Introduzione alla libreria Azure Batch .NET | Microsoft Azure"
	description="Informazioni sui concetti di base di Azure Batch e sullo sviluppo per il servizio Batch con uno scenario semplice."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="01/08/2016"
	ms.author="marsma"/>

# Introduzione alla libreria di Azure Batch per .NET  

È possibile apprendere i concetti di base relativi ad [Azure Batch][azure_batch] e alla libreria di [Batch .NET][net_api] esaminando nel dettaglio un'applicazione C# di esempio. Si vedrà in che modo usa il servizio Batch per elaborare un carico di lavoro parallelo nel cloud e come interagisce con l'[Archiviazione di Azure](./../storage/storage-introduction.md) per lo staging e il recupero di file. Si apprenderanno le tecniche comuni dei flussi di lavoro dell'applicazione Batch, acquisendo una conoscenza di base dei componenti principali di Batch, ad esempio processi, attività, pool e nodi di calcolo.

![Flusso di lavoro della soluzione Batch (di base)][11]<br/>

## Prerequisiti

Questo articolo presuppone che si sia in grado di usare C# e Visual Studio e di soddisfare i requisiti di creazione dell'account specificati di seguito per Azure e per i servizi Batch e di archiviazione.

### Account

- **Account Azure**: se non si ha già una sottoscrizione di Azure, è possibile creare un account di valutazione gratuito in pochi minuti nella pagina della [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).
- **Account Batch**: dopo avere creato una sottoscrizione di Azure, vedere [Creare e gestire un account Azure Batch](batch-account-create-portal.md).
- **Account di archiviazione**: vedere la sezione "Creare un account di archiviazione" in [Informazioni sugli account di archiviazione di Azure](../storage-create-storage-account.md).

### Visual Studio

Per compilare il progetto di esempio, è necessario **Visual Studio 2013** o **Visual Studio 2015**. Le versioni gratuite e di valutazione di Visual Studio sono disponibili nella [Panoramica dei prodotti Visual Studio 2015][visual_studio].

### Esempio di codice *DotNetTutorial*

L'esempio [DotNetTutorial][github_dotnettutorial] è uno dei molti esempi di codice disponibili nel repository [azure-batch-samples][github_samples] su GitHub. È possibile scaricare l'esempio facendo clic sul pulsante **Download ZIP** nella home page del repository oppure selezionando il collegamento di download diretto [azure-batch-samples-master.zip][github_samples_zip]. Dopo l'estrazione dei contenuti del file ZIP, la soluzione sarà disponibile nella cartella seguente:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### Azure Batch Explorer (facoltativo)

[Azure Batch Explorer][github_batchexplorer] è un'utilità gratuita inclusa nel repository [azure-batch-samples][github_samples] in GitHub. Anche se Batch Explorer non è necessario per il completamento di questa esercitazione, è consigliabile usarlo per il debug e l'amministrazione delle entità nell'account Batch. Per altre informazioni su una versione meno recente di Batch Explorer, vedere il post di blog contenente una [descrizione dettagliata dell'esempio Azure Batch Explorer][batch_explorer_blog].

## Panoramica del progetto di esempio DotNetTutorial

L'esempio di codice *DotNetTutorial* è una soluzione di Visual Studio 2013 costituita da due progetti: **DotNetTutorial** e **TaskApplication**.

- **DotNetTutorial** è l'applicazione client che interagisce con i servizi Batch e Archiviazione per eseguire un carico di lavoro parallelo nei nodi di calcolo (macchine virtuali). L'esempio DotNetTutorial viene eseguito nella workstation locale.

- **TaskApplication** è il programma che viene eseguito nei nodi di calcolo in Azure per completare le operazioni effettive. Nell'esempio `TaskApplication.exe` analizza il testo in un file scaricato da Archiviazione di Azure (file di input). Produce quindi un file di testo (file di output) che contiene un elenco delle prime tre parole visualizzate nel file di input. Dopo la creazione del file di output, TaskApplication carica il file in Archiviazione di Azure, rendendolo disponibile all'applicazione client per il download. TaskApplication viene eseguito in parallelo su più nodi di calcolo nel servizio Batch.

Il diagramma seguente illustra le operazioni principali eseguite dall'applicazione client, *DotNetTutorial*, e l'applicazione eseguita dalle attività, *TaskApplication*. Questo flusso di lavoro di base è tipico di molte soluzioni di calcolo create con Batch. Anche se non illustra ogni funzionalità disponibile nel servizio Batch, quasi tutti gli scenari di Batch includeranno processi analoghi.

![Flusso di lavoro dell'esempio di Batch][8]<br/>

**Passaggio 1.** Creare **contenitori** nell'archivio BLOB di Azure.<br/> **Passaggio 2.** Caricare nei contenitori i file dell'applicazione relativi alle attività e i file di input.<br/> **Passaggio 3.** Creare un **pool** di Batch.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Il pool **StartTask** scarica i file binari dell'attività (TaskApplication) nei nodi non appena vengono aggiunti al pool.<br/> **Passaggio 4.** Creare un **processo** Batch.<br/> **Passaggio 5.** Aggiungere **attività** al processo.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Viene pianificata l'esecuzione delle attività sui nodi.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Ogni attività scarica i rispettivi dati di input da Archiviazione di Azure, quindi avvia l'esecuzione.<br/> **Passaggio 6.** Monitorare le attività.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Dopo il completamento, le attività caricano i rispettivi dati di output in Archiviazione di Azure.<br/> **Passaggio 7.** Scaricare l'output delle attività dal servizio di archiviazione.

Come indicato, non tutte le soluzioni Batch eseguiranno esattamente questi passaggi e potrebbero includerne molti altri, ma l'applicazione di esempio *DotNetTutorial* illustra i processi comuni rilevati in una soluzione Batch.

## Compilare il progetto di esempio *DotNetTutorial*

Per eseguire correttamente l'esempio, è prima di tutto necessario specificare le credenziali dell'account Batch e dell'account di archiviazione nel file `Program.cs` del progetto *DotNetTutorial*. Se non è stato ancora fatto, aprire la soluzione in Visual Studio facendo doppio clic sul file della soluzione `DotNetTutorial.sln`. In alternativa, aprirlo da Visual Studio dal menu **File > Apri > Progetto/Soluzione**.

Aprire `Program.cs` all'interno del progetto *DotNetTutorial*. Aggiungere quindi le proprie credenziali, come specificato nella parte iniziale del file:

```
// Update the Batch and Storage account credential strings below with the values unique to your accounts.
// These are used when constructing connection strings for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

Le credenziali relative all'account Batch e all'account di archiviazione sono disponibili nel pannello dell'account di ogni servizio nel [portale di Azure][azure_portal]\:

![Credenziali di Batch nel portale][9] ![Credenziali di archiviazione nel portale][10]<br/>

Dopo avere aggiornato il progetto con le proprie credenziali, fare clic con il pulsante destro del mouse sulla soluzione in Esplora soluzioni e scegliere **Compila soluzione**. Confermare il ripristino di eventuali pacchetti NuGet, se richiesto.

> [AZURE.TIP] Se i pacchetti NuGet non vengono ripristinati automaticamente o se vengono visualizzati errori relativi all'impossibilità di ripristinare i pacchetti, assicurarsi che [Gestione pacchetti NuGet][nuget_packagemgr] sia installato, quindi abilitare il download dei pacchetti mancanti. Per abilitare il download dei pacchetti, vedere la sezione relativa all'[abilitazione del ripristino dei pacchetti durante la compilazione][nuget_restore].

Nelle sezioni seguenti si esamineranno in dettaglio i passaggi eseguiti dall'applicazione di esempio per l'elaborazione di un carico di lavoro nel servizio Batch. È consigliabile fare riferimento alla soluzione aperta in Visual Studio mentre si esamina il resto di questo articolo, perché non vengono illustrate tutte le righe di codice dell'esempio.

Passare all'inizio del metodo `MainAsync` nel file `Program.cs` del progetto *DotNetTutorial* per iniziare dal Passaggio 1. Ogni passaggio riportato segue quindi all'incirca la successione di chiamate ai metodi in `MainAsync`.

## Passaggio 1: Creare contenitori di archiviazione

![Creare contenitori in Archiviazione di Azure][1] <br/>

Batch include il supporto predefinito per l'interazione con Archiviazione di Azure. I contenitori nell'account di archiviazione forniranno alle attività eseguite nell'account Batch i file necessari per l'esecuzione, oltre a una posizione in cui archiviare i dati di output prodotti. La prima operazione eseguita dall'applicazione client *DotNetTutorial* consiste nel creare tre contenitori nell'[archivio BLOB di Azure](./../storage/storage-introduction.md):

- **application**: questo contenitore include l'applicazione che verrà eseguita dalle attività, oltre a eventuali dipendenze, ad esempio i file DLL.
- **input**: le attività scaricheranno i file di dati da elaborare dal contenitore *input*.
- **output**: dopo avere completato l'elaborazione dei file di input, le attività caricheranno i rispettivi risultati nel contenitore *output*.

Per interagire con l'account di archiviazione e creare contenitori, viene usata la [Libreria del client di Archiviazione di Azure per .NET][net_api_storage] per creare un riferimento all'account con [CloudStorageAccount][net_cloudstorageaccount] e quindi un oggetto client BLOB ([CloudBlobClient][net_cloudblobclient]):

```
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Il riferimento `blobClient` viene usato in tutta l'applicazione e viene passato come parametro ad alcuni metodi. Un esempio è costituito dal blocco di codice immediatamente successivo, in cui viene chiamato `CreateContainerIfNotExistAsync` per creare effettivamente i contenitori.

```
// Use the blob client to create the containers in Azure Storage if they don't yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```
private static async Task CreateContainerIfNotExistAsync(CloudBlobClient blobClient, string containerName)
{
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		if (await container.CreateIfNotExistsAsync())
		{
				Console.WriteLine("Container [{0}] created.", containerName);
		}
		else
		{
				Console.WriteLine("Container [{0}] exists, skipping creation.", containerName);
		}
}
```

Dopo la creazione dei contenitori, l'applicazione può caricare i file che verranno usati dalle attività.

> [AZURE.TIP] [How to use Blob Storage from .NET](./.. / storage/storage-dotnet-how-to-use-blobs.md) fornisce una utile panoramica dell'uso dei contenitori e dei BLOB di Archiviazione di Azure, quindi è consigliabile prenderne visione quando si inizia a usare Batch.

## Passaggio 2: Caricare l'applicazione dell'attività e i file di dati

![Caricare l'applicazione dell'attività e i file di input (dati) nei contenitori][2] <br/>

Nell'operazione di caricamento dei file *DotNetTutorial* definisce prima di tutto le raccolte di percorsi di file di tipo **application** e **input** esistenti nel computer locale, quindi carica i file nei contenitori creati nel passaggio precedente.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication, allowing us to
    // determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the application that will
// process the data files, and will be executed by each of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(blobClient, appContainerName, applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of the tasks that are
// executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(blobClient, inputContainerName, inputFilePaths);
```

Il processo di caricamento interessa due metodi in `Program.cs`:

- `UploadFilesToContainerAsync`: questo metodo restituisce una raccolta di oggetti [ResourceFile][net_resourcefile], illustrati di seguito, e chiama internamente `UploadFileToContainerAsync` per caricare ogni file passato nel parametro *filePaths*.
- `UploadFileToContainerAsync`: questo metodo esegue effettivamente il caricamento dei file e crea gli oggetti [ResourceFile][net_resourcefile]. Dopo il caricamento del file, ottiene una firma di accesso condiviso per il file e restituisce un oggetto ResourceFile che lo rappresenta. Più avanti vengono illustrate anche le firme di accesso condiviso.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(CloudBlobClient blobClient, string containerName, string filePath)
{
		Console.WriteLine("Uploading file {0} to container [{1}]...", filePath, containerName);

		string blobName = Path.GetFileName(filePath);

		CloudBlobContainer container = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
		await blobData.UploadFromFileAsync(filePath, FileMode.Open);

		// Set the expiry time and permissions for the blob shared access signature. In this case, no start time is specified,
		// so the shared access signature becomes valid immediately
		SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
		{
				SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
				Permissions = SharedAccessBlobPermissions.Read
		};

		// Construct the SAS URL for blob
		string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
		string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

		return new ResourceFile(blobSasUri, blobName);
}
```

### ResourceFiles

Un oggetto [ResourceFile][net_resourcefile] fornisce attività in Batch con l'URL per un file in Archiviazione di Azure che verrà scaricato in un nodo di calcolo prima dell'esecuzione dell'attività. La proprietà [ResourceFile.BlobSource][net_resourcefile_blobsource] specifica l'URL completo del file esistente in Archiviazione di Azure, che può includere anche una firma di accesso condiviso che fornisce l'accesso sicuro al file. Una proprietà *ResourceFiles* è inclusa nella maggior parte dei tipi di attività in Batch .NET, ad esempio:

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

L'applicazione di esempio DotNetTutorial non usa il tipo di attività JobPreparationTask o JobReleaseTask, ma altre informazioni su queste proprietà sono disponibili in [Eseguire attività di preparazione e completamento di processi in nodi di calcolo di Azure Batch](batch-job-prep-release.md).

### Firma di accesso condiviso

Le firme di accesso condiviso sono stringhe che, se incluse come parte di un URL, forniscono l'accesso sicuro a contenitori e BLOB in Archiviazione di Azure. L'applicazione DotNetTutorial usa gli URL di firma di accesso condiviso di BLOB e contenitori e illustra come ottenere queste stringhe di firma di accesso condiviso dal servizio di archiviazione.

- **Firme di accesso condiviso di BLOB**: l'attività StartTask del pool in DotNetTutorial usa le firme di accesso condiviso dei BLOB durante il download dei file binari dell'applicazione e dei file di dati di input dal servizio di archiviazione, come illustrato più avanti nel Passaggio 3. Il metodo `UploadFileToContainerAsync` in `Program.cs` di DotNetTutorial contiene il codice che ottiene la firma di accesso condiviso di ogni BLOB ed esegue questa operazione chiamando [CloudblobData.GetSharedAccessSignature][net_sas_blob].

- **Firma di accesso condiviso di contenitori**: quando ogni attività completa il proprio lavoro sul nodo di calcolo, carica il rispettivo file di output nel contenitore *output* in Archiviazione di Azure. A questo scopo, TaskApplication usa una firma di accesso condiviso del contenitore che fornisce l'accesso in scrittura al contenitore come parte del percorso durante il caricamento del file. Il recupero della firma di accesso condiviso del contenitore viene eseguito in modo analogo al recupero della firma di accesso condiviso del BLOB. In DotNetTutorial si noterà che il metodo helper `GetContainerSasUrl` chiama [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] per eseguire questa operazione. Altre informazioni sul modo in cui TaskApplication usa la firma di accesso condiviso del contenitore sono disponibili più avanti nel "Passaggio 6: Monitorare le attività".

> [AZURE.TIP] Per altre informazioni su come fornire l'accesso sicuro ai dati nell'account di archiviazione, vedere la serie in due parti sulle firme di accesso condiviso [Parte 1: Informazioni sul modello di firma di accesso condiviso](./../storage/storage-dotnet-shared-access-signature-part-1.md) e [Parte 2: Creare e usare una firma di accesso condiviso con il servizio BLOB](./../storage/storage-dotnet-shared-access-signature-part-2.md).

## Passaggio 3: Creare un pool di Batch

![Creare un pool di Batch][3] <br/>

Dopo il caricamento dei file dell'applicazione e dei file di dati nell'account di archiviazione, *DotNetTutorial* avvia l'interazione con il servizio Batch usando la libreria Batch .NET. A questo scopo, viene prima di tutto creato un oggetto [BatchClient][net_batchclient]\:

```
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);
using (BatchClient batchClient = BatchClient.Open(cred))
{
	...
```

Viene quindi creato un pool di nodi di calcolo nell'account Batch con una chiamata a `CreatePoolAsync`. `CreatePoolAsync` usa il metodo [BatchClient.PoolOperations.CreatePool][net_pool_create] per creare effettivamente il pool nel servizio Batch.

```
private static async Task CreatePoolAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
    // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
				poolId: poolId,
				targetDedicated: 3,           // 3 compute nodes
				virtualMachineSize: "small",  // single-core, 1.75 GB memory, 225 GB disk
				osFamily: "4");               // Windows Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join the pool.
    // In this case, we copy the StartTask's resource files (that will be automatically downloaded
    // to the node by the StartTask) into the shared directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application files to the
        // node's shared directory. Every compute node in a Batch pool is configured with several
        // pre-defined environment variables that you can reference by using commands or applications
        // run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
        // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
        // StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Quando si crea un pool con [CreatePool][net_pool_create], si specificano alcuni parametri, ad esempio il numero di nodi di calcolo, le [dimensioni dei nodi](./../cloud-services/cloud-services-sizes-specs.md) e il [sistema operativo](./../cloud-services/cloud-services-guestos-update-matrix.md) dei nodi.

> [AZURE.IMPORTANT] Vengono effettuati addebiti per le risorse di calcolo in Batch. Per ridurre al minimo i costi, è possibile abbassare `targetDedicated` a 1 prima di eseguire l'esempio.

Oltre alle proprietà relative al nodo fisico, è possibile specificare una proprietà [StartTask][net_pool_starttask] per il pool. La proprietà StartTask verrà eseguita in ogni nodo durante l'aggiunta al pool e a ogni riavvio del nodo. StartTask è particolarmente utile per l'installazione di applicazioni nei nodi di calcolo prima dell'esecuzione di attività. Ad esempio, se le attività elaborano dati usando script Python, è possibile usare StartTask per installare Python nei nodi di calcolo.

In questa applicazione di esempio StartTask copia i file scaricati dal servizio di archiviazione, specificati usando la proprietà *ResourceFiles* di StartTask, dalla directory di lavoro di StartTask alla directory condivisa a cui possono accedere *tutte* le attività in esecuzione sul nodo.

Da notare nel frammento di codice precedente è anche l'uso di due variabili di ambiente nella proprietà *CommandLine* di StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` e `%AZ_BATCH_NODE_SHARED_DIR%`. Ogni nodo di calcolo in un pool di Batch viene configurato automaticamente con alcune variabili di ambiente specifiche per Batch. Tutti processi eseguiti da un'attività possono accedere a queste variabili di ambiente.

> [AZURE.TIP] Per altre informazioni sulle variabili di ambiente disponibili nei nodi di calcolo in un pool di Batch, oltre a informazioni sulle directory di lavoro delle attività, vedere le sezioni "Impostazioni di ambiente per le attività" e "File e directory" in [Cenni preliminari sulle funzionalità di Azure Batch](batch-api-basics.md).

## Passaggio 4: Creare un processo di Batch

![Creare un processo di Batch][4]<br/>

Un processo Batch è essenzialmente una raccolta di attività associate a un pool di nodi di calcolo. Può essere usato non solo per organizzare e tenere traccia delle attività nei carichi di lavoro correlati, ma anche per imporre determinati vincoli, ad esempio il tempo di esecuzione massimo per il processo e, per estensione, per le rispettive attività, nonché per imporre una priorità dei processi rispetto ad altri nell'account Batch. In questo esempio, tuttavia, il processo viene associato solo al pool creato nel Passaggio 3. Non vengono configurate proprietà aggiuntive.

Tutti i processi di Batch sono associati a un pool specifico. Questa associazione indica i nodi in cui verranno eseguite le attività del processo e viene specificata con la proprietà [CloudJob.PoolInformation][net_job_poolinfo], come illustrato nel frammento di codice riportato di seguito.

```
private static async Task CreateJobAsync(BatchClient batchClient, string jobId, string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Dopo la creazione di un processo, vengono aggiunte attività per l'esecuzione delle operazioni.

## Passaggio 5: Aggiungere attività a un processo

![Aggiungere attività a un processo][5]<br/> *(1) Le attività vengono aggiunte al processo, (2) viene pianificata l'esecuzione delle attività sui nodi e (3) le attività scaricano i file di dati da elaborare*

Per eseguire effettivamente le operazioni, è necessario aggiungere attività a un processo. Ogni [CloudTask][net_task] viene configurato con una proprietà della riga di comando e, analogamente a StartTask del pool, con oggetti [ResourceFiles][net_task_resourcefiles] scaricati dall'attività nel nodo prima dell'esecuzione automatica della rispettiva riga di comando. Nel progetto di esempio *DotNetTutorial* ogni attività elabora un solo file. Di conseguenza, la rispettiva raccolta ResourceFiles contiene un singolo elemento.

```
private static async Task<List<CloudTask>> AddTasksAsync(BatchClient batchClient, string jobId, List<ResourceFile> inputFiles, string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format("cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 "{1}"", inputFile.FilePath, outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to adding a separate AddTask call for each. Bulk task submission
    // helps to ensure efficient underlying API calls to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Quando si accede a variabili di ambiente come `%AZ_BATCH_NODE_SHARED_DIR%` o si esegue un'applicazione non trovata in `PATH`del nodo, le righe di comando dell'attività devono avere il prefisso `cmd /c`. In questo modo verrà eseguito esplicitamente l'interprete dei comandi, indicando che è necessario terminare il processo dopo l'esecuzione del comando. Questo requisito è superfluo se le attività eseguono un'applicazione in `PATH` del nodo, ad esempio *robocopy.exe* o *powershell.exe*, e se non vengono usate variabili di ambiente.

Nel ciclo `foreach` del frammento di codice precedente è possibile notare che la riga di comando per l'attività è costruita in modo che tre argomenti della riga di comando vengano passati a *TaskApplication.exe*:

1. Il **primo argomento** è il percorso del file da elaborare. Corrisponde al percorso locale del file esistente sul nodo. Durante la creazione iniziale dell'oggetto ResourceFile in `UploadFileToContainerAsync`, il nome file è stato usato per questa proprietà come parametro per il costruttore ResourceFile. Ciò indica che il file è disponibile nella stessa directory di *TaskApplication.exe*.

2. Il **secondo argomento** specifica che le prime *N* parole devono essere scritte nel file di output. Queste informazioni sono hardcoded nell'esempio, quindi le prime 3 parole verranno scritte nel file di output.

3. Il **terzo argomento** è la firma di accesso condiviso che fornisce l'accesso in scrittura al contenitore **output** in Archiviazione di Azure. *TaskApplication.exe* usa questo URL della firma di accesso condiviso durante il caricamento del file di output in Archiviazione di Azure. Il codice corrispondente è disponibile nel metodo `UploadFileToContainer` del file `Program.cs` del progetto TaskApplication:

```
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
		string blobName = Path.GetFileName(filePath);

		// Obtain a reference to the container using the SAS URI.
		CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

		// Upload the file (as a new blob) to the container
		try
		{
				CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
				blob.UploadFromFile(filePath, FileMode.Open);

				Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
				Console.WriteLine();
		}
		catch (StorageException e)
		{

				Console.WriteLine("Write operation failed for SAS URL " + containerSas);
				Console.WriteLine("Additional error information: " + e.Message);
				Console.WriteLine();

				// Indicate that a failure has occurred so that when the Batch service sets the
				// CloudTask.ExecutionInformation.ExitCode for the task that executed this application,
				// it properly indicates that there was a problem with the task.
				Environment.ExitCode = -1;
		}
}
```

## Passaggio 6: Monitorare le attività

![Monitorare le attività][6]<br/> *L'applicazione client (1) monitora le attività per verificare lo stato di completamento e di esito positivo e (2) le attività caricano i dati risultanti in Archiviazione di Azure*

Quando le attività vengono aggiunte a un processo, vengono accodate automaticamente e ne viene pianificata l'esecuzione nei nodi di calcolo entro il pool associato al progetto. In base alle impostazioni specificate, Batch gestisce tutte le operazioni di accodamento, pianificazione, ripetizione di tentativi dell'attività e tutte le altre operazioni amministrative relative all'attività. Sono disponibili molti approcci al monitoraggio dell'esecuzione delle attività. DotNetTutorial illustra un semplice esempio che segnala solo gli stati di completamento ed esito positivo o negativo dell'attività.

Nel metodo `MonitorTasks` in `Program.cs` di DotNetTutorial sono presenti tre concetti di Batch .NET che meritano un approfondimento. I concetti sono elencati di seguito nell'ordine in cui appaiono:

1. **ODATADetailLevel**: specificare [ODATADetailLevel][net_odatadetaillevel] nelle operazioni di tipo elenco, ad esempio il recupero di un elenco di attività di un processo, è essenziale per assicurare prestazioni ottimali per l'applicazione Batch. Se si prevede di monitorare in qualche modo lo stato nelle applicazioni Batch, vedere anche [Eseguire query sul servizio Azure Batch in modo efficiente](batch-efficient-list-queries.md).

2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor] fornisce alle applicazioni Batch .NET le utilità helper per il monitoraggio degli stati delle attività. In `MonitorTasks` *DotNetTutorial* attende che tutte le attività raggiungano lo stato [TaskState.Completed][net_taskstate] entro un limite di tempo specificato, quindi termina il processo.

3. **TerminateJobAsync**: la terminazione di un processo con [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] o il valore bloccante JobOperations.TerminateJob contrassegnerà tale processo come completato. Ciò è essenziale se la soluzione Batch usa [JobReleaseTask][net_jobreltask], un tipo speciale di attività descritto in [Attività di preparazione e completamento dei processi](batch-job-prep-release).

Ecco il metodo `MonitorTasks` da `Program.cs` di *DotNetTutorial*:

```
private static async Task<bool> MonitorTasks(BatchClient batchClient, string jobId, TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use a detail level to
    // specify that only the "id" property of each task should be populated. Using a detail level for
    // all list operations helps to lower response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks = await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...", timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we will wait for all tasks to
    // reach the Completed state.
    TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
    bool timedOut = await taskStateMonitor.WaitAllAsync(tasks, TaskState.Completed, timeout);

    if (timedOut)
    {
        allTasksSuccessful = false;

        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);

        Console.WriteLine(failureMessage);
    }
    else
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

        // All tasks have reached the "Completed" state. However, this does not guarantee that all tasks were completed successfully.
        // Here we further check each task's ExecutionInfo property to ensure that it did not encounter a scheduling error
        // or return a non-zero exit code.

        // Update the detail level to populate only the task id and executionInfo properties.
        // We refresh the tasks below, and need only this information for each task.
        detail.SelectClause = "id, executionInfo";

        foreach (CloudTask task in tasks)
        {
            // Populate the task's properties with the latest info from the Batch service
            await task.RefreshAsync(detail);

            if (task.ExecutionInformation.SchedulingError != null)
            {
                // A scheduling error indicates a problem starting the task on the node. It is important to note that
                // the task's state can be "Completed," yet the task still might have encountered a scheduling error.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}", task.Id, task.ExecutionInformation.SchedulingError.Message);
            }
            else if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## Passaggio 7: Scaricare l'output dell'attività

![Scaricare l'output delle attività dal servizio di archiviazione][7]<br/>

Dopo il completamento del processo, l'output delle attività può essere scaricato da Archiviazione di Azure mediante una chiamata a `DownloadBlobsFromContainerAsync` in `Program.cs` di *DotNetTutorial*:

```
private static async Task DownloadBlobsFromContainerAsync(CloudBlobClient blobClient, string containerName, string directoryPath)
{
		Console.WriteLine("Downloading all files from container [{0}]...", containerName);

		// Retrieve a reference to a previously created container
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		// Get a flat listing of all the block blobs in the specified container
		foreach (IListBlobItem item in container.ListBlobs(prefix: null, useFlatBlobListing: true))
		{
				// Retrieve reference to the current blob
				CloudBlob blob = (CloudBlob)item;

				// Save blob contents to a file in the specified folder
				string localOutputFile = Path.Combine(directoryPath, blob.Name);
				await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
		}

		Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] La chiamata a `DownloadBlobsFromContainerAsync` nell'applicazione *DotNetTutorial* specifica che i file devono essere scaricati nella cartella `%TEMP%`. È possibile modificare questo percorso di output.

## Passaggio 8: Eliminare i contenitori

Poiché vengono effettuati addebiti per i dati che risiedono in Archiviazione di Azure, è consigliabile rimuovere eventuali BLOB non più necessari per i processi di Batch. In `Program.cs` di DotNetTutorial questa operazione viene eseguita con tre chiamate al metodo helper `DeleteContainerAsync`:

```
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

Il metodo stesso ottiene semplicemente un riferimento al contenitore e quindi chiama [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]\:

```
private static async Task DeleteContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.", containerName);
    }
}
```

## Passaggio 9: Eliminare il processo e il pool

Nel passaggio finale viene richiesto all'utente di eliminare il processo e il pool creati dall'applicazione DotNetTutorial. Anche se non vengono applicati addebiti per i processi stessi e per le attività, *vengono* effettuati addebiti per i nodi di calcolo. È quindi consigliabile allocare i nodi solo in base alla necessità. L'eliminazione dei pool inutilizzati può fare parte del processo di manutenzione.

[JobOperations][net_joboperations] e [PoolOperations][net_pooloperations] di BatchClient includono metodi di eliminazione corrispondenti, chiamati se l'utente conferma l'eliminazione:

```
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] Occorre ricordare che vengono effettuati addebiti per le risorse di calcolo e che l'eliminazione di pool inutilizzati consente di ridurre al minimo i costi. Si noti anche che l'eliminazione di un pool comporta l'eliminazione di tutti i nodi di calcolo in quel pool e che eventuali dati disponibili nei nodi non potranno essere più recuperati dopo l'eliminazione del pool.

## Eseguire l'esempio *DotNetTutorial*

Quando si esegue l'applicazione di esempio, l'output della console sarà simile al seguente. Durante l'esecuzione si verificherà una pausa in corrispondenza di `Awaiting task completion, timeout in 00:30:00...` mentre vengono avviati i nodi di calcolo del pool. Usare [Batch Explorer][github_batchexplorer] per monitorare il pool, i nodi di calcolo, il processo e le attività durante e dopo l'esecuzione. Usare il [portale di Azure][azure_portal] o uno degli [strumenti di esplorazione disponibili per Archiviazione di Azure][storage_explorers] per visualizzare le risorse di archiviazione, ovvero contenitori e BLOB, create dall'applicazione.

Il tempo di esecuzione tipico è pari a **5 minuti circa** se si esegue l'applicazione con la configurazione predefinita.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## Passaggi successivi

È possibile modificare *DotNetTutorial* e *TaskApplication* per provare a usare scenari di calcolo diversi. Si può provare ad aggiungere un ritardo di esecuzione a *TaskApplication*, ad esempio con [Thread.Sleep][net_thread_sleep], per simulare attività con esecuzione prolungata e monitorarle con la funzionalità *Mappa termica* di Batch Explorer. Provare ad aggiungere altre attività o a modificare il numero di nodi di calcolo. Aggiungere logica per controllare e consentire l'uso di un pool esistente per ridurre il tempo di esecuzione. *Suggerimento*: vedere `ArticleHelpers.cs` nel progetto [Microsoft.Azure.Batch.Samples.Common][github_samples_common] in [azure-batch-samples][github_samples].

Dopo avere acquisito familiarità con il flusso di lavoro di base di una soluzione Batch, è possibile esaminare in dettaglio le funzionalità aggiuntive del servizio Batch.

- Se non si ha familiarità con il servizio, è consigliabile vedere l'articolo [Cenni preliminari sulle funzionalità di Azure Batch](batch-api-basics.md).
- Per altri articoli sullo sviluppo in Batch, vedere **Approfondimenti sullo sviluppo** nel [Percorso di apprendimento per Batch][batch_learning_path].
- Una diversa implementazione dell'elaborazione del carico di lavoro di tipo "prime N parole" con Batch è disponibile nell'esempio [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_portal]: https://portal.azure.com
[batch_explorer_blog]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Creare contenitori in Archiviazione di Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Caricare l'applicazione dell'attività e i file di input (dati) nei contenitori"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Creare un pool di Batch"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Creare un processo di Batch"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Aggiungere attività a un processo"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Monitorare le attività"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Scaricare l'output delle attività dal servizio di archiviazione"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Flusso di lavoro della soluzione Batch (diagramma completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenziali di Batch nel portale"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenziali del servizio di archiviazione nel portale"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Flusso di lavoro della soluzione Batch (diagramma minimo)"

<!---HONumber=AcomDC_0204_2016-->