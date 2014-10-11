<properties linkid="develop-media-services-how-to-guides-encode-an-asset" urlDisplayName="How to Encode an Asset" pageTitle="How to Encode an Asset for Media Services - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Procedura: Codificare un asset

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Ottenere un'istanza del processore di contenuti multimediali][].

È possibile codificare il contenuto multimediale nel server utilizzando varie codifiche e formati multimediali disponibili in Azure Media Encoder. È inoltre possibile utilizzare un codificatore fornito da un partner di Servizi multimediali: i codificatori di terze parti sono disponibili tramite [Azure Marketplace][]. È possibile specificare i dettagli relativi alle attività di codifica utilizzando stringhe di [set di impostazioni per il codificatore][] oppure file di configurazione.

## Codifica in formato MP4

Il metodo seguente permette di caricare un singolo asset e creare un processo per codificare l'asset in formato MP4 tramite il set di impostazioni "H264 Broadband 720p" che creerà un singolo file MP4 con la codifica H264 a una risoluzione di 720p:

     static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder) { //Create an encrypted asset and upload to storage. IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);
        // Declare a new job.

        IJob job = _context.Jobs.Create("My encoding job");

        // Get a reference to the Azure Media Encoder
        IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
            "H264 Broadband 720p",
            _protectedConfig);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is in the clear (unencrypted). 
        task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-JobId.txt file 
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

    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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


</code>

</pre>

## Codifica nel formato Smooth Streaming

Se si desidera codificare un video in formato Smooth Streaming, sono disponibili due opzioni:

-   Eseguire la codifica direttamente nel formato Smooth Streaming
-   Eseguire la codifica nel formato MP4 e quindi eseguire la conversione in formato Smooth Streaming


Per eseguire la codifica direttamente nel formato Smooth Streaming, utilizzare il codice riportato sopra con uno dei set di impostazioni del codificatore per Smooth Streaming. Per un elenco completo dei set di impostazioni del codificatore, vedere [Stringhe di set di impostazioni di attività per Azure Media Encoder][].

Per convertire un file MP4 in formato Smooth Streaming, utilizzare Azure Media Packager. Azure Media Packager non supporta set di impostazioni in formato stringa. È quindi necessario specificare le opzioni di configurazione in XML. Il codice XML necessario per la conversione dal formato MP4 al formato Smooth Streaming è disponibile nel [set di impostazioni di attività per Azure Media Packager][]. Copiare e incollare il codice XML in un file denominato MediaPackager\_MP4ToSmooth.xml nel progetto. Nel codice seguente viene illustrato come convertire un asset MP4 nel formato Smooth Streaming. Il metodo seguente accetta un asset esistente e lo converte.

<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
	// Declare a new job to contain the tasks
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Set up the first Task to convert from MP4 to Smooth Streaming. 
    // Read in task configuration XML
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Get a media packager reference
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Create a task with the conversion details, using the configuration data
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Specify the input asset to be converted.
    task.InputAssets.Add(assetToConvert);
    // Add an output asset to contain the results of the job.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Use the following event handler to check job progress. 
	// The StateChange method is the same as the one in the previous sample
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    // Launch the job.
    job.Submit();
    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Get a refreshed job reference after waiting on a thread.
    job = GetJob(job.Id);
    // Check for errors
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>


Per altre informazioni sull'elaborazione di asset, vedere:

-   [Elaborazione di asset con Media Services SDK per .NET][]
-   [Elaborazione di asset con l'API REST di Servizi multimediali][]


## Passaggi successivi

Dopo avere creato un processo per la codifica di un asset, passare all'argomento [Come verificare l'avanzamento di un processo con Servizi multimediali][].

  [Procedura: Ottenere un'istanza del processore di contenuti multimediali]: http://go.microsoft.com/fwlink/?LinkID=301732&ampclcid=0x409
  [Azure Marketplace]: https://datamarket.azure.com/
  [set di impostazioni per il codificatore]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [Stringhe di set di impostazioni di attività per Azure Media Encoder]: http://msdn.microsoft.com/en-us/library/jj129582.aspx
  [set di impostazioni di attività per Azure Media Packager]: http://msdn.microsoft.com/en-us/library/windowsazure/hh973635.aspx
  [Elaborazione di asset con Media Services SDK per .NET]: http://msdn.microsoft.com/en-us/library/jj129580.aspx
  [Elaborazione di asset con l'API REST di Servizi multimediali]: http://msdn.microsoft.com/en-us/library/jj129574.aspx
  [Come verificare l'avanzamento di un processo con Servizi multimediali]: http://go.microsoft.com/fwlink/?LinkID=301737&ampclcid=0x409
