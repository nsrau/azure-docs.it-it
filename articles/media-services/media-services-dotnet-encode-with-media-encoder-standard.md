<properties 
	pageTitle="Come codificare un asset mediante Media Encoder Standard" 
	description="In questo argomento verrà illustrato come utilizzare .NET per codificare un asset con Media Encoder Standard." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015"    
	ms.author="juliako"/>


#Come codificare un asset mediante Media Encoder Standard

I processi di codifica sono tra le operazioni di elaborazione più frequenti in Servizi multimediali. Questi processi vengono creati per convertire i file multimediali da una codifica all'altra. Durante la codifica è possibile usare il codificatore multimediale incorporato in Servizi multimediali. È inoltre possibile usare un codificatore fornito da un partner di Servizi multimediali. I codificatori di terze parti sono disponibili tramite Azure Marketplace.

In questo argomento verrà illustrato come utilizzare .NET per codificare gli asset con Media Encoder Standard. Media Encoder Standard viene configurato mediante un set di impostazioni descritto [qui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

>[AZURE.NOTE]La versione corrente del processore multimediale richiede di passare un'intera stringa JSON o XML come set di impostazioni di codifica. Il supporto per il passaggio in una stringa denominata, come "H264 velocità in bit multipla 720p", sarà disponibile a breve tramite un aggiornamento del servizio.

È consigliabile codificare sempre i file in formato intermedio con un set MP4 a velocità in bit adattiva e quindi convertire il set nel formato desiderato mediante la [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md). Per avvalersi della creazione dinamica dei pacchetti, è necessario ottenere prima almeno un'unità di streaming on demand per l'endpoint di streaming da cui si intende distribuire il contenuto. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di Servizi multimediali](media-services-manage-origins.md#scale_streaming_endpoints).

Se l'asset di output è protetto con crittografia di archiviazione, è necessario configurare i criteri di distribuzione degli asset. Per altre informazioni, vedere [Procedura: Configurare i criteri di distribuzione degli asset](media-services-dotnet-configure-asset-delivery-policy.md).

##Esempio

Il seguente codice usa l'SDK .NET di Servizi multimediali per eseguire le seguenti attività:

- Creare un processo di codifica.
- Ottenere un riferimento al codificatore Media Encoder Standard.
- Caricare il set di impostazioni XML da uno dei set di impostazioni illustrati [qui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).
- Aggiungere una singola attività di codifica al processo. 
- Specificare l’asset di input da codificare.
- Creare un asset di output che conterrà l'asset codificato.
- Aggiungere un gestore eventi per controllare l'avanzamento del processo.
- Inviare il processo.
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset, string pathToLocalPresetFile)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		    // Load the XML (or JSON) from the local file
		    string configuration = File.ReadAllText(pathToLocalPresetFile);
		
		    // Create a task
		    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		        processor,
		        configuration,
		        TaskOptions.None);
		
		    // Specify the input asset to be encoded.
		    task.InputAssets.Add(asset);
		    // Add an output asset to contain the results of the job. 
		    // This output is specified as AssetCreationOptions.None, which 
		    // means the output asset is not encrypted. 
		    task.OutputAssets.AddNew("Output asset",
		        AssetCreationOptions.None);
		
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


##Percorsi di apprendimento di Media Services

È possibile visualizzare i percorsi di apprendimento AMS qui:

- [Flusso di lavoro AMS Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flusso di lavoro AMS Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Vedere anche 

[Panoramica sulla codifica dei servizi multimediali](media-services-encode-asset.md)

<!---HONumber=Sept15_HO2-->