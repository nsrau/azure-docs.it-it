<properties 
	pageTitle="Come codificare un asset mediante Azure Media Encoder" 
	description="Informazioni su come usare Azure Media Encoder per codificare contenuti multimediali in Servizi multimediali. Negli esempi di codice, scritti in C#, viene usato l'SDK di Servizi multimediali per .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# Come codificare un asset mediante Azure Media Encoder

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md). 

## Informazioni generali
Per distribuire un video digitale tramite Internet è necessario comprimere il file multimediale. I file video digitali hanno dimensioni piuttosto elevate e possono risultare troppo grandi per la distribuzione su Internet o per la visualizzazione corretta sui dispositivi dei clienti. Mediante il processo di codifica è possibile comprimere video e audio per consentire ai clienti di visualizzare i file multimediali.

I processi di codifica sono tra le operazioni di elaborazione più frequenti in Servizi multimediali. Questi processi vengono creati per convertire i file multimediali da una codifica all'altra. Durante la codifica è possibile usare il codificatore multimediale incorporato in Servizi multimediali. È inoltre possibile usare un codificatore fornito da un partner di Servizi multimediali. I codificatori di terze parti sono disponibili tramite Azure Marketplace. È possibile specificare i dettagli relativi alle attività di codifica usando stringhe di set di impostazioni definite per il codificatore oppure file di configurazione di set di impostazioni. Per i tipi di set di impostazioni disponibili, vedere Set di impostazioni di attività per Servizi multimediali di Azure. Se si usa un codificatore di terze parti, è necessario [convalidare i file](https://msdn.microsoft.com/it-it/library/azure/dn750842.aspx).

Si consiglia di codificare sempre i file in formato intermedio con un set MP4 a velocità in bit adattiva e quindi convertire il set nel formato desiderato mediante la [creazione dinamica dei pacchetti](https://msdn.microsoft.com/it-it/library/azure/jj889436.aspx).


## Creare un processo con una singola attività di codifica 

Quando si esegue la codifica mediante Azure Media Encoder, è possibile usare i set di impostazioni di attività specificati [qui](https://msdn.microsoft.com/it-it/library/azure/dn619389.aspx).

### Usare l'SDK di Servizi multimediali per .NET  

Il seguente metodo **EncodeToAdaptiveBitrateMP4Set** crea un processo di codifica e aggiunge una singola attività di codifica al processo. Nell'attività viene usato "Azure Media Encoder" per la codifica in "H264 Adaptive Bitrate MP4 Set 720p". 

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
    {
        var encodingPreset = "H264 Adaptive Bitrate MP4 Set 720p";

        IJob job = _context.Jobs.Create(String.Format("Encoding {0} into to {1}",
                                inputAsset.Name,
                                encodingPreset));

        var mediaProcessors = GetLatestMediaProcessorByName("Azure Media Encoder");

        ITask encodeTask = job.Tasks.AddNew("Encoding", mediaProcessors, encodingPreset, TaskOptions.None);
        
        encodeTask.InputAssets.Add(inputAsset);

        // Specify the storage-encrypted output asset.
        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 
            AssetCreationOptions.StorageEncrypted);


        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }

    private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
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
                break;
            default:
                break;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
           ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

### Usare le estensioni dell'SDK di Servizi multimediali per .NET

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
    {
        // 1. Prepare a job with a single task to transcode the specified mezzanine asset
        //    into a multi-bitrate asset.
        IJob job = _context.Jobs.CreateWithSingleTask(
            MediaProcessorNames.AzureMediaEncoder,
            MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
            asset,
            "Adaptive Bitrate MP4",
            AssetCreationOptions.None);

        Console.WriteLine("Submitting transcoding job...");

        // 2. Submit the job and wait until it is completed.
        job.Submit();
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    } 

## Creare un processo con attività concatenate 

In molti scenari di applicazione, gli sviluppatori desiderano creare una serie di attività di elaborazione. In Servizi multimediali è possibile creare una serie di attività concatenate. Ogni attività esegue diversi passaggi di elaborazione e può usare processori di contenuti multimediali differenti. Le attività concatenate possono trasferire un asset da un'attività a un'altra e consentono quindi l'esecuzione delle attività dell'asset in sequenza lineare. Tuttavia, non è necessario che le attività eseguite in un processo siano in sequenza. Quando si crea un'attività concatenata, gli oggetti **ITask** concatenati vengono creati in un singolo oggetto **IJob**.

>[AZURE.NOTE] Attualmente è previsto un limite di 30 attività per processo. Se è necessario concatenare più di 30 attività, creare più processi in modo da contenerle tutte.

Il seguente metodo **CreateChainedTaskEncodingJob** crea un processo contenente due attività concatenate e restituisce come risultato un processo contenente due asset di output.

	
    public static IJob CreateChainedTaskEncodingJob(IAsset asset)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("My task-chained encoding job");

        // Set up the first task to encode the input file.

        // Get a media processor reference
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
           "H264 Adaptive Bitrate MP4 Set 720p",
            TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Specify the storage-encrypted output asset.
        task.OutputAssets.AddNew("My storage-encrypted output asset",
            AssetCreationOptions.StorageEncrypted);

        // Set up the second task to decrypt the encoded output file from 
        // the first task.

        // Get another media processor instance
        IMediaProcessor decryptProcessor = GetLatestMediaProcessorByName("Storage Decryption");

        // Declare the decryption task. 
        ITask decryptTask = job.Tasks.AddNew("My decryption task",
            decryptProcessor,
            string.Empty,
            TaskOptions.None);

        // Specify the input asset to be decrypted. This is the output 
        // asset from the first task. 
        decryptTask.InputAssets.Add(task.OutputAssets[0]);

        // Specify an output asset to contain the results of the job. 
        // This should have AssetCreationOptions.None. 
        decryptTask.OutputAssets.AddNew("My decrypted output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(JobStateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        //return job that contains two output assets.
        return job;
    }


## Passaggi successivi
Dopo avere creato un processo per la codifica di un asset, passare all'argomento [Come verificare l'avanzamento di un processo con Servizi multimediali](media-services-check-job-progress.md).

[Azure Marketplace]: https://datamarket.azure.com/
[Set di impostazioni del codificatore]: http://msdn.microsoft.com/library/dn619392.aspx
[Procedura: Ottenere un'istanza del processore di contenuti multimediali]:http://go.microsoft.com/fwlink/?LinkId=301732
[Procedura: Caricare un asset crittografato]:http://go.microsoft.com/fwlink/?LinkId=301733
[Procedura: Distribuire un asset mediante download]:http://go.microsoft.com/fwlink/?LinkId=301734
[Come controllare lo stato dei processi]:http://go.microsoft.com/fwlink/?LinkId=301737
[Set di impostazioni per Azure Media Packager]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=45--> 
