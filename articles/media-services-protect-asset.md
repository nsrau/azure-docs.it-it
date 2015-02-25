<properties pageTitle="Come crittografare asset in Servizi multimediali - Azure" description="Informazioni su come usare la tecnologia di protezione Microsoft PlayReady per crittografare un asset in Servizi multimediali. Negli esempi di codice, scritti in C#, viene usato Media Services SDK per .NET. Negli esempi di codice, scritti in C#, viene usato Media Services SDK per .NET." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>





<h1><a name="playready"></a>Procedura: Proteggere un asset con la tecnologia di protezione PlayReady</h1>

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Controllare lo stato dei processi](../media-services-check-job-progress/).

In Servizi multimediali di Azure è possibile inviare un processo che si integra con la tecnologia di protezione Microsoft PlayReady per crittografare un asset. Nel codice di questa sezione vengono usati diversi file di streaming da una cartella di input, viene creata un'attività e vengono crittografati i file con PlayReady. 

Nell'esempio seguente viene illustrato come creare un semplice processo per applicare la protezione PlayReady.

   1. Recuperare i dati di configurazione. È possibile ottenere un file di configurazione di esempio nell'argomento [Set di impostazioni di attività per Azure Media Encryptor](http://msdn.microsoft.com/it-it/library/hh973610.aspx).
   2. Caricare un file di input MP4
   3. Convertire il file MP4 in un asset Smooth Streaming
   4. Crittografare l'asset con PlayReady
   5. Inviare il processo

Nell'esempio di codice seguente viene illustrato come implementare i passaggi:

<pre><code>
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
    IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Packager");

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
    IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Windows Azure Media Encryptor");

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
            EventHandler&lt;JobStateChangedEventArgs&gt;(StateChanged);

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

</code></pre>
Per altre informazioni sulla protezione PlayReady, vedere:
<ul>
<li><a href="http://msdn.microsoft.com/it-it/library/dn189154.aspx">Protezione di asset con Microsoft PlayReady</a></li>
<li><a href="http://www.microsoft.com/PlayReady/">Microsoft PlayReady</a></li>
</ul>

<h2>Passaggi successivi</h2>
Dopo avere acquisito i concetti relativi alla protezione di asset con Servizi multimediali, è possibile passare all'argomento relativo alla [gestione degli asset](../media-services-manage-assets/).


<!--HONumber=42-->
