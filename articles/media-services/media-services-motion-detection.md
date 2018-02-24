---
title: Rilevare i movimenti con Analisi Servizi multimediali di Azure | Microsoft Docs
description: Il processore di contenuti multimediali Rilevatore multimediale di movimento Azure consente di identificare in modo efficace le sezioni interessanti all'interno di un video altrimenti lungo e privo di eventi.
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: dd422308ed728ed4e8bc35daee3bd50f0f02aaac
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="detect-motions-with-azure-media-analytics"></a>Rilevare i movimenti con Analisi servizi multimediali di Azure
## <a name="overview"></a>Panoramica
Il processore di contenuti multimediali **Rilevatore multimediale di movimento Azure** consente di identificare in modo efficace le sezioni interessanti all'interno di un video altrimenti lungo e privo di eventi. Il rilevamento di movimento può essere usato nei filmati statici della videocamera per individuare le sezioni del video in cui si verificano movimenti. Viene generato un file JSON contenente i metadati con i timestamp e l'area di delimitazione in cui si è verificato l'evento.

Questa tecnologia, destinata alle trasmissioni video di sicurezza, è in grado di classificare i movimenti in eventi rilevanti e falsi positivi, ad esempio variazioni di luminosità e delle ombre. In questo modo è possibile generare avvisi di sicurezza dalle trasmissioni della videocamera senza che venga segnalata una serie infinita di eventi irrilevanti e al contempo estrarre i momenti di interesse da video di sorveglianza estremamente lunghi.

Attualmente il processore multimediale **Rilevatore multimediale di movimento Azure** è disponibile in Anteprima.

Questo articolo illustra dettagliatamente **Azure Media Motion Detector** e spiega come usare questa funzionalità con Media Services SDK per .NET

## <a name="motion-detector-input-files"></a>File di input di Rilevatore di movimento
File video. Attualmente sono supportati i formati seguenti: MP4, MOV e WMV.

## <a name="task-configuration-preset"></a>Configurazione delle attività (set di impostazioni)
Quando si crea un'attività con **Azure Media Motion Detector**è necessario specificare un set di impostazioni di configurazione. 

### <a name="parameters"></a>Parametri
È possibile usare i parametri seguenti:

| NOME | Opzioni | DESCRIZIONE | Predefinito |
| --- | --- | --- | --- |
| sensitivityLevel |Stringa:'low', 'medium', 'high' |Imposta il livello di sensibilità per la segnalazione dei movimenti. Modificarla per ridurre il numero di falsi positivi. |'medium' |
| frameSamplingValue |Intero positivo |Imposta la frequenza di esecuzione dell'algoritmo. 1 indica a ogni fotogramma, 2 a un fotogramma su due e così via. |1 |
| detectLightChange |Booleano: 'true', 'false' |Indica se vengono segnalate le variazioni di luce nei risultati |'False' |
| mergeTimeThreshold |Xs-time: Hh:mm:ss<br/>Esempio: 00:00:03 |Specifica l'intervallo di tempo tra eventi di movimento in cui 2 eventi verranno combinati e segnalati come 1 evento. |00:00:00 |
| detectionZones |Matrice di zone di rilevamento:<br/>- La zona di rilevamento è una matrice di 3 o più punti<br/>- Il punto è una coordinata x e y da 0 a 1. |Descrive l'elenco delle zone di rilevamento poligonali da usare.<br/>I risultati sono visualizzati con le zone come ID, dove la prima è 'id':0 |Singola zona che copre l'intero fotogramma. |

### <a name="json-example"></a>Esempio di JSON
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }


## <a name="motion-detector-output-files"></a>File di output di Rilevatore di movimento
Un processo di rilevamento del movimento restituirà un file JSON nell'asset di output che descrive gli avvisi di movimento e le relative categorie all'interno del video. Il file conterrà informazioni sull'ora e sulla durata dei movimenti rilevati nel video.

L'API Rilevatore di movimento fornisce indicatori se sono presenti oggetti in movimento in un video con sfondo fisso, ad esempio un video di sorveglianza. Rilevatore di movimento è in grado di ridurre i falsi allarmi, ad esempio variazioni di luminosità e di ombreggiatura. Le limitazioni correnti degli algoritmi includono video con visione notturna, oggetti semi-trasparenti e oggetti di piccole dimensioni.

### <a id="output_elements"></a>Elementi del file di output JSON
> [!NOTE]
> Nella versione più recente, il formato di output JSON è stato modificato e può rappresentare una modifica di rilievo per alcuni clienti.
> 
> 

La tabella seguente illustra gli elementi del file di output JSON.

| Elemento | DESCRIZIONE |
| --- | --- |
| Version |Indica la versione dell'API Video. La versione corrente è 2. |
| Scala cronologica |"Scatti" al secondo del video. |
| Offset |Differenza di orario dei timestamp in "scatti". Nella versione 1.0 delle API Video, questo valore è sempre 0. Negli scenari futuri supportati questo valore potrebbe cambiare. |
| Frequenza fotogrammi |Fotogrammi al secondo del video. |
| Larghezza, altezza |Indica la larghezza e l'altezza del video in pixel. |
| Inizia |Il timestamp di inizio in "scatti". |
| Duration |La lunghezza dell'evento in "scatti". |
| Interval |L'intervallo di ogni voce dell'evento in "scatti". |
| Eventi |Ogni frammento di evento contiene i movimenti rilevati nella durata specificata. |
| type |Nella versione corrente questo valore è sempre "2" per il movimento generico. Questa etichetta offre alle API Video la flessibilità necessaria per classificare i movimenti nelle versioni future. |
| RegionID |Come spiegato in precedenza, in questa versione questo valore è sempre 0. Questa etichetta offre alle API Video la flessibilità necessaria per individuare i movimenti in varie aree nelle versioni future. |
| Regioni |Si riferisce all'area del video in cui si presta particolare attenzione al movimento. <br/><br/>-"id" rappresenta l'area: in questa versione ne è presente una sola, ID 0. <br/>-"type" rappresenta la forma dell'area importante per il movimento. Sono attualmente supportati "rectangle" e "polygon".<br/> Se è stato specificato "rectangle", le dimensioni dell'area saranno X, Y, larghezza e altezza. Le coordinate X e Y rappresentano le coordinate XY in alto a sinistra nell'area in una scala normalizzata da 0,0 a 1,0. La larghezza e l'altezza rappresentano le dimensioni dell'area in una scala normalizzata da 0,0 a 1,0. Nella versione corrente, X, Y, larghezza e altezza sono sempre 0, 0 e 1, 1. <br/>Se è stato specificato "polygon", le dimensioni dell'area saranno in punti. <br/> |
| Frammenti |I metadati sono suddivisi in segmenti diversi, detti frammenti. Ogni frammento contiene un inizio, una durata, un numero di intervallo e uno o più eventi. Un frammento privo di eventi significa che non è stato rilevato alcun movimento in corrispondenza dell'ora di inizio e della durata. |
| Parentesi quadre [] |Ogni parentesi rappresenta un intervallo nell'evento. Le parentesi vuote in un intervallo indicano che è non stato rilevato alcun movimento. |
| locations |Questa nuova voce nell'elenco degli eventi indica le posizioni in cui si è verificato il movimento. È un dato più specifico delle zone di rilevamento. |

Di seguito è riportato un esempio di output JSON

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],

    …
## <a name="limitations"></a>Limitazioni
* I formati video di input supportati includono MP4, MOV e WMV.
* Il rilevamento di movimento è ottimizzato per i video a sfondo fisso. L'algoritmo mira alla riduzione dei falsi allarmi, ad esempio le variazioni di luce e ombra.
* È possibile che alcuni movimenti non vengano rilevati per problemi tecnici, ad esempio video con visione notturna, oggetti semi-trasparenti e oggetti di piccole dimensioni.

## <a name="net-sample-code"></a>Codice di esempio .NET

Il programma seguente illustra come:

1. Creare un asset e caricare un file multimediale nell'asset.
2. Creare un processo con un'attività di rilevamento movimento video in base al file di configurazione che contiene il set di impostazioni JSON seguente: 
   
        {
          "Version": "1.0",
          "Options": {
            "SensitivityLevel": "medium",
            "FrameSamplingValue": 1,
            "DetectLightChange": "False",
            "MergeTimeThreshold":
            "00:00:02",
            "DetectionZones": [
              [
                {"x": 0, "y": 0},
                {"x": 0.5, "y": 0},
                {"x": 0, "y": 1}
               ],
              [
                {"x": 0.3, "y": 0.3},
                {"x": 0.55, "y": 0.3},
                {"x": 0.8, "y": 0.3},
                {"x": 0.8, "y": 0.55},
                {"x": 0.8, "y": 0.8},
                {"x": 0.55, "y": 0.8},
                {"x": 0.3, "y": 0.8},
                {"x": 0.3, "y": 0.55}
              ]
            ]
          }
        }
3. Scaricare i file JSON di output. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

Configurare l'ambiente di sviluppo e popolare il file app.config con le informazioni di connessione, come descritto in [Sviluppo di applicazioni di Servizi multimediali con .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Esempio

```

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
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

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
```

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Collegamenti correlati
[Blog di Azure Media Motion Detector](https://azure.microsoft.com/blog/motion-detector-update/)

[Panoramica di Analisi servizi multimediali di Azure](media-services-analytics-overview.md)

[Demo di Analisi servizi multimediali di Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

