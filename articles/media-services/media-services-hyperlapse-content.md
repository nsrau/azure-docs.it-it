<properties
	pageTitle="File multimediali di Hyperlapse con Azure Media Hyperlapse"
	description="Azure Media Hyperlapse crea fluidi video in time-lapse da contenuti registrati in prima persona o da fotocamere d'azione. Questo argomento illustra come usare Media Indexer."
	services="media-services"
	documentationCenter=""
	authors="asolanki"
	manager="johndeu"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/19/2015"   
	ms.author="adsolank"/>


# File multimediali di Hyperlapse con Azure Media Hyperlapse

Azure Media Hyperlapse è un processore di contenuti multimediali che crea fluidi video in time-lapse da contenuti registrati in prima persona o da fotocamere d'azione. Elemento di pari livello basato sul cloud rispetto a [Hyperlapse Pro desktop per Microsoft Research e Hyperlapse Mobile basato su telefono](http://aka.ms/hyperlapse), Microsoft Hyperlapse per Servizi multimediali di Azure usa l'elevata scalabilità della piattaforma di elaborazione multimediale di Servizi multimediali di Azure per scalare orizzontalmente e parallelizzare l'elaborazione in blocco di Hyperlapse.

>[AZURE.IMPORTANT]Microsoft Hyperlapse per Servizi multimediali di Azure si trova attualmente in uno stato di anteprima pubblica gratuita. I processi sono limitati ai primi 10.000 fotogrammi dell'asset di input.


> Microsoft Hyperlapse è progettato per un utilizzo ottimale con contenuti in prima persona registrati con una fotocamera in movimento. Sebbene siano supportate anche riprese con telecamere fisse, le prestazioni e la qualità del processore di contenuti multimediali Azure Media Hyperlapse non può essere garantita con tipi di contenuti diversi. Per altre informazioni su Microsoft Hyperlapse per Servizi multimediali di Azure e vedere alcuni video di esempio, consultare il [post di blog introduttivo](http://aka.ms/azurehyperlapseblog) dall'anteprima pubblica.

Un processo di Azure Media Hyperlapse accetta come input un file di asset MP4, MOV o WMV, insieme a un file di configurazione che specifica i fotogrammi del video da rendere in time-lapse e a quale velocità (ad esempio, i primi 10.000 fotogrammi a velocità doppia). L'output è costituito da un rendering stabilizzato e in time-lapse del video di input.

Per gli aggiornamenti più recenti relativi ad Azure Media Hyperlapse, vedere i [blog di Servizi multimediali](http://azure.microsoft.com/blog/topics/media-services/).

## Eseguire Hyperlapse su un asset

In primo luogo, è necessario caricare il file di input desiderato in Servizi multimediali di Azure. Per altre informazioni sui concetti inerenti al caricamento e alla gestione dei contenuti, leggere l'[articolo sulla gestione dei contenuti](media-services-manage-content.md#upload).

###  <a id="configuration"></a>Set di impostazioni di configurazione per Hyperlapse

Dopo aver caricato il contenuto nel proprio account di Servizi multimediali, è necessario costruire il set di impostazioni di configurazione. La tabella seguente illustra i campi specificati dall'utente:

 Campo | Descrizione
-------|-------------
StartFrame|Il fotogramma a partire dal quale deve iniziare l'elaborazione di Microsoft Hyperlapse.
NumFrames|Il numero di fotogrammi da elaborare
Velocità|Il fattore su cui impostare la velocità di riproduzione del video di input.

Di seguito è riportato l'esempio di un file di configurazione conforme in XML e JSON:

**Set di impostazioni XML:**

	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
		<Sources>
			<Source StartFrame="0" NumFrames="10000" />
		</Sources>
		<Options>
			<Speed>12</Speed>
		</Options>
	</Preset>

**Set di impostazioni JSON:**

	{
		"Version":1.0,
		"Sources": [
			{
				"StartFrame":0,
				"NumFrames":2147483647
			}
		],
		"Options": {
			"Speed":1,
			"Stabilize":false
		}
	}

###  <a id="sample_code"></a> Hyperlapse Microsoft con il SDK di .NET AMS

Il metodo seguente carica un file multimediale come asset e crea un processo mediante il processore di contenuti multimediali Azure Media Hyperlapse.

> [AZURE.NOTE]Nell'ambito dovrebbe essere già presente un CloudMediaContext con il nome "contesto" relativo al codice da usare. Per altre informazioni, leggere l'[articolo sulla gestione dei contenuti](media-services-manage-content.md).

> [AZURE.NOTE]L'argomento stringa "hyperConfig" deve essere un set di impostazioni di configurazione conforme scritto in JSON o XML, come descritto in precedenza.

	static bool RunHyperlapseJob(string input, string output, string hyperConfig)
	{
		// create asset with input file
		IAsset asset = context
					   .Assets
					   .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

		// grab instances of Azure Media Hyperlapse MP
		IMediaProcessor mp = context
							 .MediaProcessors
							 .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

		// create Job with Hyperlapse task
		IJob job = context
				   .Jobs
				   .Create(String.Format("Hyperlapse {0}", input));

		if (String.IsNullOrEmpty(hyperConfig))
		{
			// config cannot be empty
			return false;
		}

		hyperConfig = File.ReadAllText(hyperConfig);

		ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
												mp,
												hyperConfig,
												TaskOptions.None);
		hyperlapseTask.InputAssets.Add(asset);
		hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
											AssetCreationOptions.None);


		job.Submit();

		// Create progress printing and querying tasks
			Task progressPrintTask = new Task(() =>
			{

				IJob jobQuery = null;
				do
				{
					var progressContext = context;
					jobQuery = progressContext.Jobs
											  .Where(j => j.Id == job.Id)
											  .First();
					Console.WriteLine(string.Format("{0}\t{1}\t{2}",
									  DateTime.Now,
									  jobQuery.State,
									  jobQuery.Tasks[0].Progress));
					Thread.Sleep(10000);
				}
				while (jobQuery.State != JobState.Finished &&
					   jobQuery.State != JobState.Error &&
					   jobQuery.State != JobState.Canceled);
			});
			progressPrintTask.Start();

			Task progressJobTask = job.GetExecutionProgressTask(
												 CancellationToken.None);
			progressJobTask.Wait();

			// If job state is Error, the event handling
			// method for job progress should log errors.  Here we check
			// for error state and exit if needed.
			if (job.State == JobState.Error)
			{
				ErrorDetail error = job.Tasks.First().ErrorDetails.First();
				Console.WriteLine(string.Format("Error: {0}. {1}",
												error.Code,
												error.Message));  
				return false;                  
			}

		DownloadAsset(job.OutputMediaAssets.First(), output);
		return true;
	}

	static void DownloadAsset(IAsset asset, string outputDirectory)
	{
		foreach (IAssetFile file in asset.AssetFiles)
		{
			file.Download(Path.Combine(outputDirectory, file.Name));
		}
	}


	static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = context.Assets.Create(assetName, options);

	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	    assetFile.Upload(filePath);

	    return asset;
	}

	static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	    var processor = context.MediaProcessors
	    .Where(p => p.Name == mediaProcessorName)
	    .ToList()
	    .OrderBy(p => new Version(p.Version))
	    .LastOrDefault();

	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor",
	                                                   mediaProcessorName));

	    return processor;
	}

### <a id="file_types"></a>Tipi di file supportati

- MP4
- MOV
- WMV




## Percorsi di apprendimento di Media Services

È possibile visualizzare i percorsi di apprendimento AMS qui:

- [Flusso di lavoro AMS Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flusso di lavoro AMS Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)




<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=Nov15_HO2-->