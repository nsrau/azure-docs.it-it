<properties urlDisplayName="How to Encode an Asset" pageTitle="Come codificare un asset per Servizi multimediali - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#Procedura: Codificare un asset
Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Ottenere un'istanza del processore di contenuti multimediali](../media-services-get-media-processor/).

È possibile codificare il contenuto multimediale nel server usando varie codifiche e formati multimediali disponibili in Azure Media Encoder. È inoltre possibile usare un codificatore fornito da un partner di Servizi multimediali: i codificatori di terze parti sono disponibili tramite [Azure Marketplace][]. È possibile specificare i dettagli relativi alle attività di codifica usando [stringhe di set di impostazioni per il codificatore][] oppure file di configurazione. 

##Codifica in un set MP4 velocità in bit adattiva
È consigliabile codificare il file in formato intermedio in set MP4 velocità in bit adattiva e quindi usare funzionalità Dynamic Packaging per la distribuzione del contenuto. Per altre informazioni, vedere [Creazione di un processo di codifica con Media Services SDK per .NET](http://msdn.microsoft.com/it-it/library/azure/dn282273.aspx), [Creazione dinamica dei pacchetti](http://msdn.microsoft.com/it-it/library/azure/jj889436.aspx) e [Distribuzione di contenuto](http://msdn.microsoft.com/it-it/library/azure/hh973618.aspx).

##Codifica in formato MP4
Il metodo seguente consente di caricare un singolo asset e creare un processo per codificare l'asset in formato MP4 tramite il set di impostazioni "H264 Broadband 720p" che creerà un singolo file MP4 con la codifica H264 a una risoluzione di 720p:
<pre><code>
	static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
	{
    	//Create an encrypted asset and upload to storage.
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
			inputMediaFilePath);

		// Declare a new job.

    	IJob job = _context.Jobs.Create("My encoding job");
	
		// Get a reference to the Azure Media Encoder
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
    
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
</code></pre>
<h2>Codifica nel formato Smooth Streaming</h2>
Se si desidera codificare un video in formato Smooth Streaming, sono disponibili due opzioni:
<ul>
<li> Eseguire la codifica direttamente nel formato Smooth Streaming </li>
<li> Eseguire la codifica nel formato MP4 e quindi eseguire la conversione in formato Smooth Streaming</li>
</ul>

Per eseguire la codifica direttamente nel formato Smooth Streaming, usare il codice riportato sopra con uno dei set di impostazioni del codificatore per Smooth Streaming. Per un elenco completo dei set di impostazioni del codificatore, vedere [Stringhe di set di impostazioni di attività per Azure Media Encoder](http://msdn.microsoft.com/it-it/library/jj129582.aspx). 

Per convertire un file MP4 in formato Smooth Streaming, usare Azure Media Packager. Azure Media Packager non supporta set di impostazioni in formato stringa. È quindi necessario specificare le opzioni di configurazione in XML. Il codice XML necessario per la conversione dal formato MP4 al formato Smooth Streaming è disponibile nel [set di impostazioni di attività per Azure Media Packager][]. Copiare e incollare il codice XML in un file denominato MediaPackager_MP4ToSmooth.xml nel progetto. Il codice seguente illustra come convertire un asset MP4 nel formato Smooth Streaming. Il metodo seguente accetta un asset esistente e lo converte. 
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
<ul>
<li><a href="http://msdn.microsoft.com/it-it/library/jj129580.aspx">Elaborazione di asset con Media Services SDK per .NET</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/jj129574.aspx">Elaborazione di asset con l'API REST di Servizi multimediali</a></li>
</ul>

##Passaggi successivi
Dopo avere creato un processo per la codifica di un asset, passare all'argomento [Come verificare l'avanzamento di un processo con Servizi multimediali](../media-services-check-job-progress/).

[Azure Marketplace]: https://datamarket.azure.com/
[Impostazioni per il codificatore]: http://msdn.microsoft.com/it-it/library/dn619392.aspx
[Procedura: Ottenere un'istanza del processore di contenuti multimediali]:http://go.microsoft.com/fwlink/?LinkId=301732
[Procedura: Caricare un asset crittografato]:http://go.microsoft.com/fwlink/?LinkId=301733
[Procedura: Distribuire un asset mediante download]:http://go.microsoft.com/fwlink/?LinkId=301734
[Come controllare lo stato dei processi]:http://go.microsoft.com/fwlink/?LinkId=301737
[Set di impostazioni per Azure Media Packager]:http://msdn.microsoft.com/it-it/library/windowsazure/hh973635.aspx

<!--HONumber=35_1-->
