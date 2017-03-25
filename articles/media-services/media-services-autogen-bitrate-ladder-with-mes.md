---
title: Usare Azure Media Encoder Standard per generare automaticamente un bitrate ladder | Microsoft Docs
description: "In questo argomento viene illustrato come usare Media Encoder Standard (MES) per generare automaticamente un bitrate ladder in base alla risoluzione di input e alla velocità in bit. La risoluzione di input e la velocità in bit non vengono mai superate. Ad esempio, se l&quot;input è 720p a 3 Mbps, l&quot;output resterà al massimo a 720p e inizierà a una velocità inferiore a 3 Mbps."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 63ed95da-1b82-44b0-b8ff-eebd535bc5c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 244413be8b094605883445bb3cbf675b538b704e
ms.lasthandoff: 03/10/2017


---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Usare Azure Media Encoder Standard per generare automaticamente un bitrate ladder

## <a name="overview"></a>Panoramica

In questo argomento viene illustrato come usare Media Encoder Standard (MES) per generare automaticamente un bitrate ladder (coppia risoluzione-velocità in bit) in base alla risoluzione di input e alla velocità in bit. Il set di impostazioni generate automaticamente non supererà mai la risoluzione di input e la velocità in bit. Ad esempio, se l'input è 720p a 3 Mbps, l'output resterà al massimo a 720p e inizierà a una velocità inferiore a 3 Mbps.

Per usare questa funzionalità, durante la creazione di un'attività di codifica è necessario specificare il set di impostazioni **Flusso adattivo**. Quando si usa il set di impostazioni **Flusso adattivo** il codificatore MES userà in modo intelligente un bitrate ladder. Tuttavia, non sarà possibile controllare i costi di codifica, poiché il servizio determina il numero di livelli da usare e la risoluzione. È possibile vedere esempi dei livelli di output prodotti da MES in seguito alla codifica con il set di impostazioni **Flusso adattivo** alla [fine](#output) di questo argomento.

## <a id="encoding_with_dotnet"></a>Codifica con l’SDK .NET dei servizi multimediali

Il seguente codice usa l'SDK .NET di Servizi multimediali per eseguire le seguenti attività: 

- Creare un processo di codifica.
- Ottenere un riferimento al codificatore Media Encoder Standard.
- Aggiungere un'attività di codifica al processo e specificare l'uso del set di impostazioni **Flusso adattivo**. 
- Creare un asset di output che conterrà l'asset codificato.
- Aggiungere un gestore eventi per controllare l'avanzamento del processo.
- Inviare il processo.

        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;

        namespace AdaptiveStreamingMESPresest
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
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Get an uploaded asset.
                var asset = _context.Assets.FirstOrDefault();

                // Encode and generate the output using the "Adaptive Streaming" preset.
                EncodeToAdaptiveBitrateMP4Set(asset);

                Console.ReadLine();
            }

            static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Media Encoder Standard Job");

                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                // Create a task
                ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                processor,
                "Adaptive Streaming",
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

            }

        }

## <a id="output"></a>Output

Questa sezione mostra tre esempi dei livelli di output prodotti da MES in seguito alla codifica con il set di impostazioni **Flusso adattivo**. 

### <a name="example-1"></a>Esempio 1
L'origine con altezza "1080" e una frequenza frame "29.970" produce 6 livelli video:

|Livello|Altezza:|Larghezza|Bitrate (Kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Esempio 2
L'origine con altezza "720" e una frequenza frame "23.970" produce 5 livelli video:

|Livello|Altezza:|Larghezza|Bitrate (Kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Esempio 3
L'origine con altezza "360" e una frequenza frame "29.970" produce 3 livelli video:

|Livello|Altezza:|Larghezza|Bitrate (Kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Vedere anche
[Panoramica sulla codifica dei servizi multimediali](media-services-encode-asset.md)


