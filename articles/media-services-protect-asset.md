<properties linkid="develop-media-services-how-to-guides-encrypt-assets" urlDisplayName="Encrypt Assets in Media Services" pageTitle="How to Encrypt Assets in Media Services - Azure" metaKeywords="" description="Learn how to use Microsoft PlayReady Protection to encrypt an asset in Media Services. Code samples are written in C# and use the Media Services SDK for .NET. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Protect an Asset with PlayReady Protection" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# <a name="playready"></a>Procedura: Proteggere un asset con la tecnologia di protezione PlayReady

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Controllare lo stato dei processi][].

In Servizi multimediali di Azure è possibile inviare un processo che si integra con la tecnologia di protezione Microsoft PlayReady per crittografare un asset. Nel codice di questa sezione vengono utilizzati diversi file di streaming da una cartella di input, viene creata un'attività e vengono crittografati i file con PlayReady.

Nell'esempio seguente viene illustrato come creare un semplice processo per applicare la protezione PlayReady.

1.  Recuperare i dati di configurazione. È possibile ottenere un file di configurazione di esempio nell'argomento [Set di impostazioni di attività per Azure Media Encryptor][].
2.  Caricare un file di input MP4
3.  Convertire il file MP4 in un asset Smooth Streaming
4.  Crittografare l'asset con PlayReady
5.  Inviare il processo

Nell'esempio di codice seguente viene illustrato come implementare i passaggi:

    private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
    {
        // Create a storage-encrypted asset and upload the MP4. 
        IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

        // Declare a new job to contain the tasks.
        IJob job = _context.Jobs.Create("My PlayReady Protection job");

        // Set up the first task. 

        // Read the task configuration data into a string. 
        string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

        // Get a media processor instance
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");

        // Create a task with the conversion details, using the configuration data 
        ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
            processor,
            configMp4ToSmooth,
            _clearConfig);

        // Specify the input asset to be converted.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. We do not need 
        // to persist the output asset to storage, so set the shouldPersistOutputOnCompletion
        // parameter to false. 
        task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
        IAsset smoothOutputAsset = task.OutputAssets[0];

        // Set up the encryption task. 

        // Read the configuration data into a string. 
        string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

        // Get a media processor instance
        IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Azure Media Encryptor");

        // Create a second task, specifying a task name, the media processor, and configuration
        ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
            playreadyProcessor,
            configPlayReady,
            _protectedConfig);

        // Add the input asset, which is the smooth streaming output asset from the first task. 
        playreadyTask.InputAssets.Add(smoothOutputAsset);

        // Add an output asset to contain the results of the job. Persist the output by setting 
        // the shouldPersistOutputOnCompletion param to true.
        playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
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

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("\nExiting method due to job error.");
            return job;
        }
        // Perform other tasks. For example, access the assets that are the output of a job, 
        // either by creating URLs to the asset on the server, or by downloading. 

        return job;
    }

Per altre informazioni sulla protezione PlayReady, vedere:

-   [Protezione di asset con Microsoft PlayReady][]
-   [Microsoft PlayReady][]

</p>
## Passaggi successivi

Dopo avere acquisito i concetti relativi alla protezione di asset con Servizi multimediali, è possibile passare all'argomento [Come gestire gli asset][].

  [Procedura: Controllare lo stato dei processi]: http://go.microsoft.com/fwlink/?LinkID=301737&clcid=0x409
  [Set di impostazioni di attività per Azure Media Encryptor]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [Protezione di asset con Microsoft PlayReady]: http://msdn.microsoft.com/en-us/library/dn189154.aspx
  [Microsoft PlayReady]: http://www.microsoft.com/PlayReady/
  [Come gestire gli asset]: http://go.microsoft.com/fwlink/?LinkID=301943&clcid=0x409
