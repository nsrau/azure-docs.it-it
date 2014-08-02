<properties linkid="develop-media-services-tutorials-get-started" urlDisplayName="Get Started with Media Services" pageTitle="Get Started with Media Services - Azure" metaKeywords="Azure media services" description="An introduction to using Media Services with Azure." metaCanonical="" services="media-services" documentationCenter="" title="Get started with Media Services" authors="" solutions="" manager="" editor="" />

Introduzione a Servizi multimediali
===================================

In questa esercitazione viene illustrato come iniziare a sviluppare tramite Servizi multimediali di Azure. Vengono illustrati il flusso di lavoro di base di Servizi multimediali nonché gli oggetti e le attività di programmazione utilizzati più di frequente per lo sviluppo basato su Servizi multimediali. Al termine dell'esercitazione, si sarà in grado di riprodurre un file multimediale di esempio caricato, codificato e scaricato oppure di selezionare l'asset codificato e riprodurlo nel server.

Per un progetto C\# creato in Visual Studio contenente il codice di questa esercitazione, fare clic qui: [Download](http://go.microsoft.com/fwlink/?linkid=253275).

In questa esercitazione vengono descritte le operazioni di base seguenti:

-   [Configurazione del progetto](#Step1)
-   [Recupero del contesto del server di Servizi multimediali](#Step2)
-   [Creazione di un asset e caricamento dei file associati all'asset in Servizi multimediali](#Step3)
-   [Codifica di un asset e download di un asset di output](#Step4)

Prerequisiti
------------

Per la procedura dettagliata e lo sviluppo basato su Azure Media Services SDK, sono necessari i prerequisiti illustrati di seguito.

-   Un account di Servizi multimediali ottenuto con una sottoscrizione di Azure nuova o esistente. Per informazioni dettagliate, vedere [Come creare un account di Servizi multimediali](http://go.microsoft.com/fwlink/?LinkId=256662).
-   Sistemi operativi: Windows 7, Windows 2008 R2 o Windows 8.
-   .NET Framework 4.5 o .NET Framework 4.
-   Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express).
-   Installare le librerie di **Azure SDK per .NET**, **Azure Media Services SDK per .NET** e **WCF Data Services 5.0 per OData V3** e aggiungere riferimenti al progetto utilizzando il pacchetto [NuGet windowsazure.mediaservice](http://nuget.org/packages/windowsazure.mediaservices). Nella sezione riportata di seguito viene illustrato come installare e aggiungere questi riferimenti.

**Nota**

Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A8A8397B5).

Configurazione del progetto
---------------------------

1.  Creare una nuova applicazione console C\# in Visual Studio 2012 o Visual Studio 2010 SP1. Immettere un valore nei campi **Nome**, **Percorso** e **Nome soluzione**, quindi fare clic su OK.

2.  Aggiungere un riferimento all'assembly System.Configuration.

    Per aggiungere riferimenti utilizzando la finestra di dialogo **Gestione riferimenti**, eseguire le operazioni seguenti. Fare clic con il pulsante destro del mouse sul nodo **Riferimenti** in **Esplora soluzioni**, quindi scegliere **Aggiungi riferimento**. Nella finestra di dialogo **Gestione riferimenti** selezionare gli assembly appropriati, in questo caso System.Configuration.

3.  Se non si è ancora eseguita questa operazione, aggiungere i riferimenti alle librerie di **Azure SDK per .NET** (Microsoft.WindowsAzure.StorageClient.dll), **Azure Media Services SDK per .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) e **WCF Data Services 5.0 per OData V3** (Microsoft.Data.OData.dll) utilizzando il pacchetto [NuGet windowsazure.mediaservices](http://nuget.org/packages/windowsazure.mediaservices).

    Per aggiungere riferimenti utilizzando il pacchetto NuGet, eseguire le operazioni seguenti. Scegliere STRUMENTI -\> Gestione pacchetti libreria -\> Console di Gestione pacchetti dal menu principale di Visual Studio. Nella finestra della console digitare *Install-Package [nome pacchetto]* e premere INVIO. In questo caso utilizzare comando seguente: *Install-Package windowsazure.mediaservices*.)

4.  Aggiungere una sezione *appSettings* al file **app.config** e impostare i valori per il nome e la chiave dell'account di Servizi multimediali di Azure. Il nome e la chiave sono stati forniti durante il processo di configurazione dell'account di Servizi multimediali. Aggiungere tali valori all'attributo value per ciascuna impostazione nel file app.config del progetto Visual Studio.

    > [WACOM.NOTE] 
    > In Visual Studio 2012 il file app.config viene aggiunto per impostazione predefinita. In Visual Studio 2010 è invece necessario aggiungere manualmente il file di configurazione dell'applicazione.

	<pre><code>
	&lt;configuration&gt;
  	. . . 
  	&lt;appSettings&gt;
    	&lt;add key="accountName" value="Add-Media-Services-Account-Name" /&gt;
    	&lt;add key="accountKey" value="Add-Media-Services-Account-Key" /&gt;
  	&lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>

5.  Creare nel computer locale una nuova cartella denominata supportFiles. In questo esempio la cartella supportFiles si trova nella directory del progetto MediaServicesGettingStarted. Nel [progetto](http://go.microsoft.com/fwlink/?linkid=253275) relativo a questa procedura dettagliata è presente la directory supportFiles. È possibile copiare nella cartella supportFiles il contenuto di questa directory.

6.  Sovrascrivere le istruzioni using esistenti all'inizio del file Program.cs con il codice seguente.

         using System;
         using System.Linq;
         using System.Configuration;
         using System.IO;
         using System.Text;
         using System.Threading;
         using System.Threading.Tasks;
         using System.Collections.Generic;
         using Microsoft.WindowsAzure;
         using Microsoft.WindowsAzure.MediaServices.Client;

7.  Aggiungere le variabili di percorso a livello di classe riportate di seguito. Il percorso **\_supportFiles** deve puntare alla cartella creata in un passaggio precedente.

         // Base support files path.  Update this field to point to the base path  
         // for the local support files folder that you create. 
         private static readonly string _supportFiles =
                     Path.GetFullPath(@"../..\supportFiles");
            
         // Paths to support files (within the above base path). You can use 
         // the provided sample media files from the "supportFiles" folder, or 
         // provide paths to your own media files below to run these samples.
         private static readonly string _singleInputFilePath =
             Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
         private static readonly string _outputFilesFolder =
             Path.GetFullPath(_supportFiles + @"\outputfiles");

8.  Aggiungere le variabili a livello di classe riportate di seguito per recuperare le impostazioni di autenticazione e connessione. Queste impostazioni vengono recuperate tramite pull dal file app.config e sono necessarie per la connessione a Servizi multimediali, l'autenticazione e il recupero di un token per l'accesso al contesto del server. Nel codice del progetto viene fatto riferimento a queste variabili per creare un'istanza del contesto del server.

         private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
         private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9.  Aggiungere la variabile a livello di classe riportata di seguito utilizzata come riferimento statico al contesto del server.

         // Field for service context.
         private static CloudMediaContext _context = null;

Recupero del contesto di Servizi multimediali
---------------------------------------------

L'oggetto contesto di Servizi multimediali contiene tutti gli oggetti e le raccolte fondamentali per la programmazione basata su Servizi multimediali. Il contesto include riferimenti a raccolte importanti, tra cui processi, asset, file, criteri di accesso, localizzatori e altri oggetti. Il contesto del server deve essere recuperato per la maggior parte delle attività di programmazione di Servizi multimediali.

Nel file Program.cs aggiungere il codice seguente come primo elemento del metodo **Main**. Questo codice utilizza i valori del nome e della chiave dell'account di Servizi multimediali presenti nel file app.config per creare un'istanza del contesto del server. L'istanza viene assegnata alla variabile **\_context** creata a livello di classe.

    // Get the service context.
    _context = new CloudMediaContext(_accountName, _accountKey);

Creazione di un asset e caricamento di un file
----------------------------------------------

Il codice contenuto in questa sezione consente di effettuare le seguenti operazioni:

1.  Creare un asset vuoto
     Quando si crea un asset, e\` possibile specificare tre diverse opzioni per la crittografia.

    -   **AssetCreationOptions.None**: non applica alcuna crittografia. Impostare questa opzione se si desidera creare un asset non crittografato.
    -   **AssetCreationOptions.CommonEncryptionProtected**: consente di creare file CENC (Common Encryption Protected). Un esempio è un set di file già crittografati con PlayReady.
    -   **AssetCreationOptions.StorageEncrypted**: applica la crittografia di archiviazione. Crittografa un file di input non crittografato prima che venga caricato nei servizi di archiviazione di Azure.

        **Nota**

        Si noti che Servizi multimediali offre una crittografia di archiviazione su disco, non in rete come Digital Rights Management (DRM).

  

2.  Creare un'istanza di AssetFile da associare all'asset.
3.  Creare un'istanza di AccessPolicy che definisce le autorizzazioni e la durata dell'accesso all'asset.
4.  Creare un'istanza di Locator che consente l'accesso all'asset.
5.  Caricare in Servizi multimediali un singolo file multimediale. Il processo di creazione e caricamento è anche noto come inserimento di asset.

Aggiungere alla classe i metodi riportati di seguito.

<pre><code>
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(3),
                                                        AccessPermissions.Write | AccessPermissions.List);

    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    locator.Delete();
    accessPolicy.Delete();

    return asset;
}

</code></pre>

Aggiungere una chiamata al metodo dopo la riga **\_context = new CloudMediaContext(\_accountName, \_accountKey);** del metodo Main.

    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

Codifica di un asset nel server e download di un asset di output
----------------------------------------------------------------

In Servizi multimediali è possibile creare processi che eseguono diversi tipi di elaborazione sui contenuti multimediali: codifica, crittografia, conversioni di formato e così via. Un processo di Servizi multimediali contiene sempre una o più attività che specificano i dettagli dell'elaborazione da eseguire. In questa sezione verrà creata un'attività di codifica di base e verrà eseguito un processo che effettua tale attività mediante Azure Media Encoder. L'attività utilizza una stringa di set di impostazioni per specificare il tipo di codifica da eseguire. Per verificare i valori di codifica disponibili nel set di impostazioni, vedere [Stringhe di set di impostazioni di attività per Azure Media Encoder](http://msdn.microsoft.com/en-us/library/windowsazure/jj129582.aspx). Servizi multimediali supporta gli stessi formati di input e di output dei file multimediali di Microsoft Expression Encoder. Per un elenco dei formati supportati, vedere l'articolo relativo ai [tipi di file supportati per Servizi multimediali](http://msdn.microsoft.com/en-us/library/windowsazure/hh973634.aspx).

1.  Aggiungere alla classe la definizione del metodo **CreateEncodingJob** riportata di seguito. Questo metodo mostra come eseguire numerose attività obbligatorie per un processo di codifica.

    -   Dichiarare un nuovo processo.
    -   Dichiarare un processore multimediale per la gestione del processo. Un processore multimediale è un componente che gestisce processi di codifica, crittografia, conversione di formato e altri processi di elaborazione correlati. Sono disponibili vari tipi di processori multimediali ed è possibile scorrerli tutti tramite \_context.MediaProcessors. Il metodo GetLatestMediaProcessorByName, illustrato più avanti in questa procedura dettagliata, restituisce il processore Azure Media Encoder.
    -   Dichiarare una nuova attività. Ogni processo contiene una o più attività. È necessario passare un nome descrittivo, un'istanza del processore multimediale, una stringa di configurazione dell'attività e le opzioni di creazione dell'attività. La stringa di configurazione specifica le impostazioni di codifica. In questo esempio viene utilizzata l'impostazione **H264 Broadband 720p**. Questo set di impostazioni genera un singolo file MP4. Per ulteriori informazioni su questo e altri set di impostazioni, vedere [Stringhe di set di impostazioni di attività per Azure Media Encoder](http://msdn.microsoft.com/library/windowsazure/jj129582.aspx).
    -   Aggiungere all'attività un asset di input. In questo esempio come asset di input viene utilizzato l'asset creato nella sezione precedente.
    -   Aggiungere all'attività un asset di output. Per un asset di output è necessario specificare un nome descrittivo, un valore booleano per indicare se l'output deve essere salvato nel server al termine del processo o meno e un valore **AssetCreationOptions.None** per indicare che l'output non deve essere crittografato per l'archiviazione e il trasporto.
    -   Inviare il processo.
         L'invio è l'ultimo passaggio necessario per un processo di codifica.

    Il metodo mostra inoltre come eseguire altre attività utili ma facoltative quali il monitoraggio dell'avanzamento del processo e l'accesso all'asset creato dal processo di codifica.

    ``` {}
    static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
	{
	    // Declare a new job.
        IJob job = _context.Jobs.Create("My encoding job");
        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
            "H264 Broadband 720p",
            Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);
        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);
        // Use the following event handler to check job progress.  
        job.StateChanged += new
                EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-{JobId}.txt file 
        // in your output folder.
        LogJobDetails(job.Id);

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();
	   
		// **********
    	// Optional code.  Code after this point is not required for 
        // an encoding job, but shows how to access the assets that 
        // are the output of a job, either by creating URLs to the 
        // asset on the server, or by downloading. ?
	    // **********

	    // Get an updated job reference.
        job = GetJob(job.Id);

        // If job state is Error the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
    {        
			Console.WriteLine("\nExiting method due to job error.");
            return job;
    }

	    // Get a reference to the output asset from the job.
        IAsset outputAsset = job.OutputMediaAssets[0];
        IAccessPolicy policy = null;
        ILocator locator = null;

        // Declare an access policy for permissions on the asset. 
        // You can call an async or sync create method. 
        policy =
            _context.AccessPolicies.Create("My 30 days readonly policy",
                TimeSpan.FromDays(30),
                AccessPermissions.Read);

        // Create a SAS locator to enable direct access to the asset 
        // in blob storage. You can call a sync or async create method.  
        // You can set the optional startTime param as 5 minutes 
        // earlier than Now to compensate for differences in time  
        // between the client and server clocks. 

        locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Build a list of SAS URLs to each file in the asset. 
        List<String> sasUrlList = GetAssetSasUrlList(outputAsset, locator);

        // Write the URL list to a local file. You can use the saved 
        // SAS URLs to browse directly to the files in the asset.
        if (sasUrlList != null)
    {
        string outFilePath = Path.GetFullPath(outputFolder + @"\" + "FileSasUrlList.txt");
            StringBuilder fileList = new StringBuilder();
            foreach (string url in sasUrlList)
        {
            fileList.AppendLine(url);
            fileList.AppendLine();
        }        
		WriteToFile(outFilePath, fileList.ToString());

            // Optionally download the output to the local machine.
            DownloadAssetToLocal(job.Id, outputFolder);
    }
  

    return job;
    
    }
    

2 .  Aggiungere nel metodo **Main** una chiamata al metodo **CreateEncodingJob**, dopo le righe aggiunte in precedenza.

    
    CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);
   

3 .  Aggiungere alla classe i metodi helper riportati di seguito. Questi metodi sono necessari per il supporto del metodo **CreateEncodingJob**. Di seguito è riportato un riepilogo dei metodi helper.
    -   Il metodo **GetLatestMediaProcessorByName** restituisce un processore multimediale appropriato per la gestione di un'attività di codifica, crittografia o un'altra attività di elaborazione correlata. Per creare un processore multimediale è necessario utilizzare il nome di stringa appropriato del processore da creare. Le stringhe che è possibile passare al metodo per il parametro mediaProcessor sono le seguenti: **Azure Media Encoder**, **Azure Media Packager**, **Azure Media Encryptor**, **Storage Decryption**.

 
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		{
		    // The possible strings that can be passed into the 
            // method for the mediaProcessor parameter:
            //   Azure Media Encoder
            //   Azure Media Packager
            //   Azure Media Encryptor
            //   Storage Decryption

            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
		}



-   Quando si esegue un processo, spesso è necessario monitorarne l'avanzamento. Nell'esempio di codice seguente viene definito il gestore dell'evento StateChanged, che monitora l'avanzamento del processo e ne indica lo stato aggiornato. Il codice consente inoltre di definire il metodo helper LogJobStop, che registra i dettagli degli errori.

        
        private static void StateChanged(object sender, JobStateChangedEventArgs e)
		{
		    Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
	    {
		        case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("********************");
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine("Please wait while local tasks or downloads complete...");
                    Console.WriteLine("********************");
                    Console.WriteLine();
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    LogJobStop(job.Id);
                    break;
                default:
                    break;
		    }
		}
		private static void LogJobStop(string jobId)
		{
		    StringBuilder builder = new StringBuilder();
            IJob job = GetJob(jobId);

            builder.AppendLine("\nThe job stopped due to cancellation or an error.");
            builder.AppendLine("***************************");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Job Name: " + job.Name);
            builder.AppendLine("Job State: " + job.State.ToString());
            builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
            builder.AppendLine("Media Services account name: " + _accountName);
            // Log job errors if they exist.  
            if (job.State == JobState.Error)
	    {
		        builder.Append("Error Details: \n");
                foreach (ITask task in job.Tasks)
        {
            		foreach (ErrorDetail detail in task.ErrorDetails)            
					{                
						builder.AppendLine("  Task Id: " + task.Id);
                        builder.AppendLine("    Error Code: " + detail.Code);
                        builder.AppendLine("    Error Message: " + detail.Message + "\n");
            		}
        		}
   		 }
    	 	builder.AppendLine("***************************\n");
            // Write the output to a local file and to the console. The template 
            // for an error output file is:  JobStop-{JobId}.txt
            string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
            WriteToFile(outputFile, builder.ToString());
            Console.Write(builder.ToString());
		}

		private static void LogJobDetails(string jobId)
		{
		    StringBuilder builder = new StringBuilder();
            IJob job = GetJob(jobId);

            builder.AppendLine("\nJob ID: " + job.Id);
            builder.AppendLine("Job Name: " + job.Name);
            builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
            builder.AppendLine("Media Services account name: " + _accountName);

            // Write the output to a local file and to the console. The template 
            // for an error output file is:  JobDetails-{JobId}.txt
            string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
            WriteToFile(outputFile, builder.ToString());
            Console.Write(builder.ToString());
		}
        
		private static string JobIdAsFileName(string jobID)
		{
		    return jobID.Replace(":", "_");
		}
        

-   Il metodo WriteToFile scrive un file nella cartella di output specificata.


		static void WriteToFile(string outFilePath, string fileContent)
		{
    			StreamWriter sr = File.CreateText(outFilePath);
    			sr.Write(fileContent);
    			sr.Close();
		}



-   Dopo la codifica degli asset in Servizi multimediali, è possibile accedere agli asset di output risultanti da un processo di codifica. In questa procedura dettagliata vengono illustrate due modalità per l'accesso all'output di un processo di codifica:

     -   Creazione di un URL di firma di accesso condiviso di un asset nel server.
     -   Download dell'asset di output dal server.

   Il metodo GetAssetSasUrlList crea un elenco di URL di firma di accesso condiviso di tutti i file di un asset.

        
        static List<String> GetAssetSasUrlList(IAsset asset, ILocator locator)
		{
		   // Declare a list to contain all the SAS URLs.
            List<String> fileSasUrlList = new List<String>();

            // If the asset has files, build a list of URLs to 
            // each file in the asset and return. 
            foreach (IAssetFile file in asset.AssetFiles)
	    {
	        string sasUrl = BuildFileSasUrl(file, locator);
            fileSasUrlList.Add(sasUrl);
	    }

		    // Return the list of SAS URLs.
            return fileSasUrlList;
		}

		// Create and return a SAS URL to a single file in an asset. 
        static string BuildFileSasUrl(IAssetFile file, ILocator locator)
		{
		    // Take the locator path, add the file name, and build 
            // a full SAS URL to access this file. This is the only 
            // code required to build the full URL.
            var uriBuilder = new UriBuilder(locator.Path);
            uriBuilder.Path += "/" + file.Name;

            // Optional:  print the locator.Path to the asset, and 
            // the full SAS URL to the file
            Console.WriteLine("Locator path: ");
		    Console.WriteLine(locator.Path);
            Console.WriteLine();
            Console.WriteLine("Full URL to file: ");
		    Console.WriteLine(uriBuilder.Uri.AbsoluteUri);
            Console.WriteLine();


            //Return the SAS URL.
            return uriBuilder.Uri.AbsoluteUri;
		}
        

-   Il metodo **DownloadAssetToLocal** scarica in una cartella locale ogni file nell'asset. In questo esempio, poiché l'asset è stato creato con un file multimediale di input, la raccolta di file di asset di output contiene due file: il file multimediale codificato (con estensione mp4) e un file con estensione xml contenente i metadati dell'asset. Il metodo scarica entrambi i file.

        
        static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
		{
    		// This method illustrates how to download a single asset. 
            // However, you can iterate through the OutputAssets
            // collection, and download all assets if there are many. 

            // Get a reference to the job. 
            IJob job = GetJob(jobId);
            // Get a reference to the first output asset. If there were multiple 
            // output media assets you could iterate and handle each one.
            IAsset outputAsset = job.OutputMediaAssets[0];

            IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
            ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);
            BlobTransferClient blobTransfer = new BlobTransferClient
	    {
        		NumberOfConcurrentTransfers = 10,
                ParallelTransferThreadCount = 10
	    };

		    var downloadTasks = new List<Task>();
            foreach (IAssetFile outputFile in outputAsset.AssetFiles)
	    {
	        	// Use the following event handler to check download progress.
                outputFile.DownloadProgressChanged += DownloadProgress;

                string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

                Console.WriteLine("File download path:  " + localDownloadPath);

                downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

                outputFile.DownloadProgressChanged -= DownloadProgress;
	    }

	    Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
		}

		static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
		{
	    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
		}



-   I metodi helper GetJob e GetAsset recuperano tramite query e restituiscono un riferimento a un oggetto processo e un oggetto asset con gli ID specificati. È possibile utilizzare un tipo simile di query LINQ per restituire riferimenti ad altri oggetti Servizi multimediali nel server.

        
        static IJob GetJob(string jobId)
		{
		    // Use a Linq select query to get an updated 
            // reference by Id. 
            var jobInstance =
                from j in _context.Jobs
                where j.Id == jobId
                select j;
            // Return the job reference as an Ijob. 
            IJob job = jobInstance.FirstOrDefault();

            return job;?}?static IAsset GetAsset(string assetId)
		{
		    // Use a LINQ Select query to get an asset.
            var assetInstance =
                from a in _context.Assets
                where a.Id == assetId
                select a;
            // Reference the asset as an IAsset.
            IAsset asset = assetInstance.FirstOrDefault();

            return asset;
		}
        

Test del codice
---------------

Eseguire il programma premendo F5. Nella console verrà visualizzato un output simile a quello riportato di seguito:

<pre><code>
Asset name: UploadSingleFile_11/14/2012 10:09:11 PM
Time created: 11/14/2012 12:00:00 AM
Created assetFile interview2.wmv
Upload interview2.wmv
Done uploading of interview2.wmv using Upload()

Job ID: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
Job Name: My encoding job
Job submitted (client UTC time): 11/14/2012 10:09:39 PM
Media Services account name: Add-Media-Services-Account-Name
Media Services account location: Add-Media-Services-account-location-name

Job(My encoding job) state: Queued.
Please wait...

Job(My encoding job) state: Processing.
Please wait...

********************
Job(My encoding job) is finished.
Please wait while local tasks or downloads complete...
********************

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&amp;se=2012-11-14T23%3A07%3A01Z&amp;sr=c&amp;
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&amp;sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&amp;se=2012-11-14T23%3
A07%3A01Z&amp;sr=c&amp;si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&amp;sig=XKMo0qJI5w8Fod3NsV%2F
BxERnav8Jb6hL7fxylq3oESc%3D

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&amp;se=2012-11-14T23%3A07%3A01Z&amp;sr=c&amp;
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&amp;sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&amp;se=2012-1
1-14T23%3A07%3A01Z&amp;sr=c&amp;si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&amp;sig=XKMo0qJI5w8F
od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

Downloads are in progress, please wait.

File download path:  C:\supportFiles\outputfiles\interview2.mp4
1.70952185308162 % download progress.
3.68508804454907 % download progress.
6.48870388360293 % download progress.
6.83808741232649 % download progress.
. . . 
99.0763740574049 % download progress.
99.1522674787341 % download progress.
100 % download progress.
File download path:  C:\supportFiles\outputfiles\interview2_metadata.xml
100 % download progress.

</code></pre>

1.  Durante l'esecuzione di questa applicazione, si verifica quanto descritto di seguito:

2.  In Servizi multimediali viene caricato un file con estensione wmv.

3.  Il file viene quindi codificato tramite il set di impostazioni **H264 Broadband 720p** di **Azure Media Encoder**.

4.  Nella cartella \\supportFiles\\outputFiles viene creato il file FileSasUrlList.txt contenente l'URL dell'asset codificato.

    Per riprodurre il file multimediale, copiare l'URL dell'asset dal file di testo e incollarlo in un browser.

5.  Il file multimediale con estensione mp4 e il file \_metadata.xml vengono scaricati nella cartella outputFiles.

**Nota**

Nel modello a oggetti di Servizi multimediali un asset è un oggetto raccolta di contenuto di Servizi multimediali che rappresenta uno o più file. Il percorso localizzatore fornisce un URL BLOB di Azure corrispondente al percorso di base dell'asset nei servizi di archiviazione di Azure. Per accedere a file specifici all'interno dell'asset, aggiungere un nome di file al percorso localizzatore di base.

Passaggi successivi
-------------------

In questa procedura dettagliata è stata illustrata una sequenza di attività di programmazione per la compilazione di un'applicazione Servizi multimediali semplice. Sono state illustrate le attività di programmazione fondamentali di Servizi multimediali, tra cui il recupero del contesto del server, la creazione e la codifica di asset nonché il download di asset presenti nel server o il relativo accesso. Per i passaggi successivi e attività di sviluppo più avanzate, vedere:

-   [Come utilizzare Servizi multimediali](http://www.windowsazure.com/en-us/develop/net/how-to-guides/media-services/)
-   [Creazione di applicazioni con l'API REST di Servizi multimediali](http://msdn.microsoft.com/en-us/library/windowsazure/hh973618.aspx)

