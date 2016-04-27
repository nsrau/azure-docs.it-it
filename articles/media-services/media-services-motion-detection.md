<properties
	pageTitle="Rilevare i movimenti con Analisi servizi multimediali di Azure"
	description="Il processore di contenuti multimediali Rilevatore multimediale di movimento Azure consente di identificare in modo efficace le sezioni interessanti all'interno di un video altrimenti lungo e privo di eventi."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/08/2016"   
	ms.author="milanga;juliako;"/>

# Rilevare i movimenti con Analisi servizi multimediali di Azure

##Panoramica

Il processore di contenuti multimediali **Rilevatore multimediale di movimento Azure** consente di identificare in modo efficace le sezioni interessanti all'interno di un video altrimenti lungo e privo di eventi. Il rilevamento di movimento può essere usato nei filmati statici della videocamera per individuare le sezioni del video in cui si verificano movimenti. Viene generato un file JSON contenente i metadati con i timestamp e l'area di delimitazione in cui si è verificato l'evento.

Questa tecnologia, destinata alle trasmissioni video di sicurezza, è in grado di classificare i movimenti in eventi rilevanti e falsi positivi, ad esempio variazioni di luminosità e delle ombre. In questo modo è possibile generare avvisi di sicurezza dalle trasmissioni della videocamera senza che venga segnalata una serie infinita di eventi irrilevanti e al contempo estrarre i momenti di interesse da video di sorveglianza estremamente lunghi.

Attualmente il processore multimediale **Rilevatore multimediale di movimento Azure** è disponibile in Anteprima.

Questo argomento illustra dettagliatamente il **Rilevatore multimediale di movimento Azure** e spiega come usare questa funzionalità con Media Services SDK per .NET.

##File di input di Rilevatore di movimento

File video. Attualmente sono supportati i formati seguenti: MP4, MOV e WMV.

##File di output di Rilevatore di movimento

Un processo di rilevamento del movimento restituirà un file JSON nell'asset di output che descrive gli avvisi di movimento e le relative categorie all'interno del video. Il file conterrà informazioni sull'ora e sulla durata dei movimenti rilevati nel video.

Attualmente il rilevamento di movimento supporta soltanto la categoria di movimento generica, definita di ***tipo 2*** nell'output.

Le dimensioni e le coordinate X e Y vengono elencate con un valore mobile normalizzato compreso tra 0,0 e 1,0. Moltiplicare questo valore per la risoluzione di altezza e larghezza video per ottenere il rettangolo del contorno dell'area in cui è stato rilevato il movimento.

Ogni output è suddiviso in frammenti e ripartito in intervalli per definire i dati all'interno del video. I frammenti non devono essere di pari lunghezza e possono comprendere lunghe parti in cui non vengono rilevati movimenti.

L'API Rilevatore di movimento fornisce indicatori se sono presenti oggetti in movimento in un video con sfondo fisso, ad esempio un video di sorveglianza. Rilevatore di movimento è in grado di ridurre i falsi allarmi, ad esempio variazioni di luminosità e di ombreggiatura. Le limitazioni correnti degli algoritmi includono video con visione notturna, oggetti semi-trasparenti e oggetti di piccole dimensioni.

###<a id="output_elements"></a>Elementi del file di output JSON

La tabella seguente illustra gli elementi del file di output JSON.

Elemento|Descrizione
---|---
Versione|Indica la versione dell'API Video.
Scala cronologica|"Scatti" al secondo del video.
Offset|Differenza di orario dei timestamp in "scatti". Nella versione 1.0 delle API Video, questo valore è sempre 0. Negli scenari futuri supportati questo valore potrebbe cambiare.
Frequenza fotogrammi|Fotogrammi al secondo del video.
Larghezza, altezza|Indica la larghezza e l'altezza del video in pixel.
Inizia|Il timestamp di inizio in "scatti".
Durata|La lunghezza dell'evento in "scatti".
Interval|L'intervallo di ogni voce dell'evento in "scatti".
Eventi|Ogni frammento di evento contiene i movimenti rilevati nella durata specificata.
Tipo|Nella versione corrente questo valore è sempre "2" per il movimento generico. Questa etichetta offre alle API Video la flessibilità necessaria per classificare i movimenti nelle versioni future.
RegionID|Come spiegato in precedenza, in questa versione questo valore è sempre 0. Questa etichetta offre alle API Video la flessibilità necessaria per individuare i movimenti in varie aree nelle versioni future.
Regioni|Indica l'area del video in cui il movimento è rilevante. Nella versione corrente delle API Video non è possibile specificare un'area e l'intera superficie del video corrisponde all'area di movimento che verrà rilevata.<br/>-L'ID indica l'area della regione: in questa versione è disponibile un solo ID, l'ID 0. <br/>-Il rettangolo rappresenta la forma dell'area rilevante per il movimento. In questa versione, è sempre un rettangolo. <br/>-Le dimensioni dell'area sono X, Y, larghezza e altezza. Le coordinate X e Y rappresentano le coordinate XY in alto a sinistra nell'area in una scala normalizzata da 0,0 a 1,0. La larghezza e l'altezza rappresentano le dimensioni dell'area in una scala normalizzata da 0,0 a 1,0. Nella versione corrente i valori di X, Y, larghezza e altezza sono fissi su 0, 0 e 1, 1.<br/>-Frammenti I metadati sono suddivisi segmenti diversi, detti frammenti. Ogni frammento contiene un inizio, una durata, un numero di intervallo e uno o più eventi. Un frammento privo di eventi significa che non è stato rilevato alcun movimento in corrispondenza dell'ora di inizio e della durata.
Parentesi quadre|Ogni parentesi rappresenta un intervallo nell'evento. Le parentesi vuote in un intervallo indicano che è non stato rilevato alcun movimento.
 

##Configurazione delle attività (set di impostazioni)

Quando si crea un'attività con **Rilevatore multimediale di movimento Azure**, è necessario specificare un set di impostazioni di configurazione. Attualmente non è possibile impostare le opzioni nel set di impostazioni di Rilevatore multimediale di movimento Azure. Segue il set di impostazioni della configurazione minima da specificare.

	{"version":"1.0"}

##Video di esempio e output di Rilevatore di movimento

###Esempio con movimento effettivo

[Esempio con movimento effettivo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fd54876c6-89a5-41de-b1f4-f45b6e10a94f%2FGarage.ism%2Fmanifest)

###Output JSON

	 {
	 "version": "1.0",
	 "timescale": 60000,
	 "offset": 0,
	 "framerate": 30,
	 "width": 1920,
	 "height": 1080,
	 "regions": [
	   {
	     "id": 0,
	     "type": "rectangle",
	     "x": 0,
	     "y": 0,
	     "width": 1,
	     "height": 1
	   }
	 ],
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 68510
	   },
	   {
	     "start": 68510,
	     "duration": 969999,
	     "interval": 969999,
	     "events": [
	       [
	         {
	           "type": 2,
	           "regionId": 0
	         }
	       ]
	     ]
	   },
	   {
	     "start": 1038509,
	     "duration": 41489
	   }
	 ]
	}

###Esempio con falsi positivi

[Esempio con falsi positivi (variazioni di luce):](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Ffdc6656b-1c10-4f3f-aa7c-07ba073c1615%2FLivingRoomLight.ism%2Fmanifest&tech=flash)

###Output JSON

	{
	    "version": "1.0",
	    "timescale": 30000,
	    "offset": 0,
	    "framerate": 29.97,
	    "width": 1920,
	    "height": 1080,
	    "regions": [
	    {
	        "id": 0,
	        "type": "rectangle",
	        "x": 0,
	        "y": 0,
	        "width": 1,
	        "height": 1
	    }
	    ],
	    "fragments": [
	    {
	        "start": 0,
	        "duration": 320320
	    }
	    ]
	}


##Limitazioni

- I formati video di input supportati includono MP4, MOV e WMV.
- Il rilevamento di movimento è ottimizzato per i video a sfondo fisso. L'algoritmo mira alla riduzione dei falsi allarmi, ad esempio le variazioni di luce e ombra.
- È possibile che alcuni movimenti non vengano rilevati per problemi tecnici, ad esempio video con visione notturna, oggetti semi-trasparenti e oggetti di piccole dimensioni.


## Codice di esempio

Il programma seguente illustra come:

1. Creare un asset e caricare un file multimediale nell'asset.
1. Creare un processo con un'attività di rilevamento movimento video in base al file di configurazione che contiene il set di impostazioni JSON seguente. 
					
		{
		    "version": "1.0"
		}

1. Scaricare i file di output JSON.
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace VideoMotionDetection
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        static void Main(string[] args)
		        {
		
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Run the VideoMotionDetection job.
		            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\VideoMotionDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
		        }
		
		        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Video Motion Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Video Motion Detection Job");
		
		            // Get a reference to Azure Media Motion Detector.
		            string MediaProcessorName = "Azure Media Motion Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
		
		            // Use the following event handler to check job progress.  
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
		
		            // Launch the job.
		            job.Submit();
		
		            // Check job execution and wait for job to finish.
		            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
		
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
		                return null;
		            }
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
		        {
		            IAsset asset = _context.Assets.Create(assetName, options);
		
		            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
		            assetFile.Upload(filePath);
		
		            return asset;
		        }
		
		        static void DownloadAsset(IAsset asset, string outputDirectory)
		        {
		            foreach (IAssetFile file in asset.AssetFiles)
		            {
		                file.Download(Path.Combine(outputDirectory, file.Name));
		            }
		        }
		
		        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors
		                .Where(p => p.Name == mediaProcessorName)
		                .ToList()
		                .OrderBy(p => new Version(p.Version))
		                .LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor",
		                                                           mediaProcessorName));
		
		            return processor;
		        }
		
		        static private void StateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished.");
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
		                    // LogJobStop(job.Id);
		                    break;
		                default:
		                    break;
		            }
		        }
		
		    }
        }


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Collegamenti correlati

[Panoramica di Analisi servizi multimediali di Azure](media-services-analytics-overview.md)

<!---HONumber=AcomDC_0413_2016-->