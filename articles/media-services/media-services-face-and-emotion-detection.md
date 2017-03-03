---
title: Rilevare volti ed emozioni con Analisi Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento illustra come rilevare volti ed emozioni con Analisi Servizi multimediali di Azure.
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/09/2017
ms.author: milanga;juliako;
translationtype: Human Translation
ms.sourcegitcommit: adaf2a71e022d6d29493ab0a679bd593ea40195e
ms.openlocfilehash: acb3b4d4a14ea546e94ccc38806251460e21a6bc
ms.lasthandoff: 02/10/2017


---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Rilevare volti ed emozioni con Analisi servizi multimediali di Azure
## <a name="overview"></a>Overview
Il processore di contenuti multimediali **Rilevamento multimediale volti di Azure** consente di contare, monitorare i movimenti e persino di valutare la partecipazione e le reazioni del pubblico in base alle espressioni del volto. Questo servizio contiene due funzionalità: 

* **Rilevamento volti**
  
    Il Rilevamento volti rileva e monitora i volti umani all'interno di un video. È possibile rilevare e monitorare diversi volti mentre le persone si muovono; i metadati relativi a ora e luogo vengono restituiti in un file JSON. Durante il monitoraggio, il sistema tenterà di assegnare sempre lo stesso ID al volto mentre la persona si muove sullo schermo, anche se è nascosta o se esce brevemente dall'inquadratura.
  
  > [!NOTE]
  > Questo servizio non esegue il riconoscimento facciale. Se una persona esce dall'inquadratura o viene nascosta troppo a lungo, al suo ritorno le verrà assegnato un nuovo ID.
  > 
  > 
* **Rilevamento emozioni**
  
    Il Rilevamento emozioni è un componente facoltativo del Processore multimediale di rilevamento volti che restituisce un'analisi di vari attributi emotivi dei volti rilevati, inclusi la felicità, la paura, la tristezza, la rabbia e altre emozioni. 

Attualmente il processore multimediale **Rilevamento multimediale volti di Azure** è disponibile in Anteprima.

Questo argomento contiene informazioni dettagliate su **Azure Media Face Detector** e illustra come usare questa funzionalità con Media Services .NET SDK.

## <a name="face-detector-input-files"></a>File di input di Rilevamento volti
File video. Attualmente sono supportati i formati seguenti: MP4, MOV e WMV.

## <a name="face-detector-output-files"></a>File di output di Rilevamento volti
L'API per il rilevamento e monitoraggio volti offre il rilevamento e il monitoraggio volti ad alta precisione ed è in grado di rilevare fino a 64 volti umani in un video. Le riprese anteriori producono i risultati migliori, mentre i profili e i volti piccoli (inferiori o uguali a 24x24 pixel) non garantiscono altrettanta precisione.

I volti rilevati e monitorati vengono restituiti con coordinate (sinistra, superiore, larghezza e altezza) indicanti la posizione dei volti nell'immagine in pixel e un codice ID del volto che indica il monitoraggio della persona. I codici ID del volto sono soggetti a ripristino quando le riprese non sono frontali o sono sovrapposte nel fotogramma, causando l'assegnazione di diversi ID alla stessa persona.

### <a name="a-idoutputelementsaelements-of-the-output-json-file"></a><a id="output_elements"></a>Elementi del file di output JSON
Per le operazioni di rilevamento e monitoraggio dei volti, il risultato restituito contiene i metadati ricavati dai volti all'interno del file specificato in formato JSON.

Il file JSON sul rilevamento e monitoraggio dei volti include gli attributi seguenti:

| Elemento | Descrizione |
| --- | --- |
| Versione |Indica la versione dell'API Video. |
| Scala cronologica |"Scatti" al secondo del video. |
| Offset |Differenza di orario dei timestamp. Nella versione 1.0 delle API Video, questo valore è sempre 0. Negli scenari futuri supportati questo valore potrebbe cambiare. |
| Frequenza fotogrammi |Fotogrammi al secondo del video. |
| Frammenti |I metadati sono suddivisi in segmenti diversi, detti frammenti. Ogni frammento contiene un inizio, una durata, un numero di intervallo e uno o più eventi. |
| Inizia |L'ora di inizio del primo evento in "scatti". |
| Durata |La lunghezza del frammento in "scatti". |
| Interval |L'intervallo di ogni voce di evento all'interno del frammento in "scatti". |
| Eventi |Ogni evento contiene i volti rilevati e monitorati nel periodo specificato. È una matrice di eventi. La matrice esterna rappresenta un intervallo di tempo. La matrice interna è costituita da 0 o più eventi che si sono verificati in un determinato momento. Le parentesi quadre vuote [] indicano che non sono stati rilevati volti. |
| ID |L'ID del volto monitorato. Questo numero potrebbe cambiare inavvertitamente se un volto non viene rilevato. Una determinata persona dovrebbe avere lo stesso ID in tutto il video, ma non è possibile garantire sempre lo stesso ID a causa delle limitazioni nell'algoritmo di rilevamento (occlusione e così via) |
| X, Y |Le coordinate X e Y in alto a sinistra del riquadro del contorno del volto in una scala normalizzata da 0,0 a 1,0. <br/>Le coordinate X e Y si riferiscono sempre all'orientamento orizzontale, pertanto se il video è in formato verticale o se è capovolto, nel caso di iOS, sarà necessario trasporre le coordinate di conseguenza. |
| Larghezza, altezza |La larghezza e altezza del riquadro del contorno del volto in una scala normalizzata da 0,0 a 1,0. |
| facesDetected |Questo valore viene riportato alla fine dei risultati del file JSON e riepiloga il numero di volti rilevati dall'algoritmo durante il video. Poiché gli ID possono essere reimpostati inavvertitamente se un volto non viene rilevato, ad esempio quando il volto esce dall'inquadratura o la persona si volta, questo numero potrebbe non corrispondere sempre al vero numero di volti del video. |

Il rilevatore di volti usa tecniche di frammentazione, che consentono di suddividere i metadati in blocchi basati sul tempo e di scaricare solo gli elementi necessari, e di segmentazione, che consentono di suddividere gli eventi se diventano troppo grandi. Alcuni semplici calcoli consentono di trasformare i dati. Se ad esempio un evento è iniziato in corrispondenza di 6300 (scatti), con una scala cronologica di 2997 (scatti al secondo) e una frequenza di fotogrammi di 29,97 (fotogrammi al secondo):

* Inizio/Scala cronologica = 2,1 secondi
* Secondi x frequenza di fotogrammi = 63 fotogrammi

## <a name="face-detection-input-and-output-example"></a>Esempio di input e output di rilevamento volti
### <a name="input-video"></a>Video di input
[Video di input](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configurazione delle attività (set di impostazioni)
Quando si crea un'attività con **Rilevamento multimediale volti di Azure**, è necessario specificare un set di impostazioni di configurazione. Il set di impostazioni di configurazione seguente serve unicamente per il rilevamento volti.

    {
      "version":"1.0"
      "options":{
          "TrackingMode": "Faster"
      }
    }

#### <a name="attribute-descriptions"></a>Descrizioni degli attributi
| Nome attributo | Descrizione |
| --- | --- |
| Mode |Faster: velocità di elaborazione maggiore, ma meno accurata (impostazione predefinita). <br/>Quality: migliore accuratezza di rilevamento, ma tempi più lunghi. |


### <a name="json-output"></a>Output JSON
L'esempio seguente di output JSON è stato troncato.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

## <a name="emotion-detection-input-and-output-example"></a>Esempio di input e output di Rilevamento emozioni
### <a name="input-video"></a>Video di input
[Video di input](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configurazione delle attività (set di impostazioni)
Quando si crea un'attività con **Rilevamento multimediale volti di Azure**, è necessario specificare un set di impostazioni di configurazione. Il set di impostazioni di configurazione seguente specifica la creazione del file JSON in base al Rilevamento emozioni.

    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


#### <a name="attribute-descriptions"></a>Descrizioni degli attributi
| Nome attributo | Descrizione |
| --- | --- |
| Mode |Faces: solo rilevamento viso.<br/>PerFaceEmotion: restituisce un'emozione in modo indipendente per ogni rilevamento viso.<br/>AggregateEmotion: restituzione dei valori medi delle emozioni per tutti i volti nel fotogramma. |
| AggregateEmotionWindowMs |Va usato se è selezionata la modalità AggregateEmotion. Specifica la lunghezza del video usato per produrre ogni risultato aggregato, in millisecondi. |
| AggregateEmotionIntervalMs |Va usato se è selezionata la modalità AggregateEmotion. Specifica con quale frequenza produrre risultati aggregati. |

#### <a name="aggregate-defaults"></a>Impostazioni predefinite degli aggregati
Di seguito sono specificati i valori consigliati per la finestra di aggregazione e le impostazioni di intervallo. Il valore di AggregateEmotionWindowMs non deve essere maggiore del valore di AggregateEmotionIntervalMs.

|| Impostazioni predefinite | Min(s) | Max(s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0,25|
| AggregateEmotionIntervalMs |0,5 |1 |0,25|

### <a name="json-output"></a>Output JSON
Output JSON per l'emozione aggregata (troncato):

    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


## <a name="limitations"></a>Limitazioni
* I formati video di input supportati includono MP4, MOV e WMV.
* L'intervallo delle dimensioni rilevabili del volto è da 24x24 a 2048x2048 pixel. I volti al di fuori di questo intervallo non vengono rilevati.
* Il numero massimo di volti restituiti per ogni video è 64.
* È possibile che alcuni volti non vengano rilevati per problemi tecnici, ad esempio angoli del volto molto grandi (durante le pose) e grandi occlusioni. Le riprese frontali e quasi frontali producono i risultati migliori.

## <a name="sample-code"></a>Codice di esempio
Il programma seguente illustra come:

1. Creare un asset e caricare un file multimediale nell'asset.
2. Creare un processo con un'attività di rilevamento viso in base al file di configurazione che contiene il set di impostazioni JSON seguente. 
   
        {
            "version": "1.0"
        }
3. Scaricare i file JSON di output. 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace FaceDetection
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
   
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
   
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
   
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
   
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

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Collegamenti correlati
[Panoramica di Analisi servizi multimediali di Azure](media-services-analytics-overview.md)

[Demo di Analisi servizi multimediali di Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)


