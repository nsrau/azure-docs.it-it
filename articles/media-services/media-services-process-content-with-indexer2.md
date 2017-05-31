---
title: Indicizzazione dei file multimediali con Azure Media Indexer 2 Preview | Microsoft Docs
description: Azure Media Indexer consente di rendere disponibile per la ricerca il contenuto dei file multimediali e di generare una trascrizione full-text per i sottotitoli codificati e le parole chiave. Questo argomento illustra come usare Media Indexer 2 Preview.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 85d25525-a498-44eb-ae3a-2ca5ceb8e53d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 15/05/2017
ms.author: adsolank;juliako;
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 717a8ace8c469b0529ac3c98f22b8ee5a53b3d3b
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indicizzazione dei file multimediali con Azure Media Indexer 2 Preview
## <a name="overview"></a>Overview
Il processore di contenuti multimediali **Azure Media Indexer 2 Preview** consente di rendere disponibili per la ricerca file e contenuti multimediali, oltre a generare tracce e parole chiave per i sottotitoli codificati. Rispetto alla versione precedente di [Azure Media Indexer](media-services-index-content.md), **Azure Media Indexer 2 Preview** offre prestazioni di indicizzazione superiori e un supporto più ampio per le lingue. Le lingue supportate includono l'italiano, l'inglese, il francese, il tedesco, lo spagnolo, il portoghese, il giapponese, il cinese, mandarino semplificato, e l'arabo.

Il processore di contenuti multimediali **Azure Media Indexer 2 Preview** è attualmente disponibile in anteprima.

Questo argomento illustra come creare processi di indicizzazione con **Azure Media Indexer 2 Preview**.

> [!NOTE]
> Si applicano le considerazioni seguenti:
> 
> Indexer 2 non è supportato in Azure Cina e in Azure per enti pubblici.
> 
> Durante l'indicizzazione dei contenuti, assicurarsi di usare file multimediali con contenuto vocale molto chiaro (senza musica, rumore, effetti o fruscio del microfono). Alcuni esempi di contenuto appropriato includono riunioni registrate, lezioni o presentazioni. Il seguente contenuto potrebbe non essere adatto per l'indicizzazione: film, programmi televisivi, contenuto con una combinazione di audio ed effetti sonori e contenuto registrato di scarsa qualità che presenta rumori di fondo (fruscio).
> 
> 

Questo argomento contiene informazioni dettagliate su **Azure Media Indexer 2 Preview** e illustra come usare questo processore con Media Services .NET SDK

## <a name="input-and-output-files"></a>File di input e output
### <a name="input-files"></a>File di input
File audio o video

### <a name="output-files"></a>File di output
Un processo di indicizzazione può generare file di sottotitoli codificati nei formati seguenti:  

* **SAMI**
* **TTML**
* **WebVTT**

I file di sottotitoli codificati con questi formati possono essere utili per rendere i file audio e video accessibili alle persone con problemi uditivi.

## <a name="task-configuration-preset"></a>Configurazione delle attività (set di impostazioni)
Quando si crea un'attività di indicizzazione con **Azure Media Indexer 2 Preview**, è necessario specificare un set di impostazioni di configurazione.

Il codice JSON seguente imposta i parametri disponibili.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

## <a name="supported-languages"></a>Lingue supportate
Azure Media Indexer 2 Preview supporta il riconoscimento vocale per le lingue seguenti. Quando si specifica il nome della lingua nella configurazione dell'attività, usare il codice di 4 caratteri tra parentesi quadre, come illustrato di seguito:

* Inglese [EnUs]
* Spagnolo [EsEs]
* Cinese (mandarino, semplificato) [ZhCn]
* Francese [FrFr]
* Tedesco [DeDe]
* Italiano [ItIt]
* Portoghese [PtBr]
* Arabo (egiziano) [ArEg]
* Giapponese [JaJp]

## <a name="supported-file-types"></a>Tipi di file supportati

Per informazioni sui tipi di file supportati, vedere la sezione sui [codec/formati supportati](media-services-media-encoder-standard-formats.md#input-containerfile-formats).

## <a name="sample-code"></a>Codice di esempio

Il programma seguente illustra come:

1. Creare un asset e caricare un file multimediale nell'asset.
2. Creare un processo con un'attività di indicizzazione in base al file di configurazione che contiene il set di impostazioni JSON seguente.
   
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }
3. Scaricare i file di output. 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace IndexContent
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
   
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
   
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
   
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Indexing Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset to be indexed.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
   
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


