---
title: Codifica avanzata con il flusso di lavoro Premium del codificatore multimediale | Microsoft Docs
description: Informazioni su come codificare con il flusso di lavoro Premium del codificatore multimediale. Negli esempi di codice, scritti in C#, viene usato Media Services SDK per .NET.
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: c5c9571b0a94fe353e37d27303cc1cd7c5373abc
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Codifica avanzata con il flusso di lavoro Premium del codificatore multimediale
> [!NOTE]
> Il processore di contenuti multimediali del flusso di lavoro Premium del codificatore multimediale descritto in questo articolo non è disponibile in Cina.
>
>

Per domande relative al codificatore Premium, inviare un messaggio di posta elettronica a mepd@microsoft.com.

## <a name="overview"></a>Panoramica
Servizi multimediali di Microsoft Azure offre il processore di contenuti multimediali **Flusso di lavoro Premium del codificatore multimediale** . Questo processore offre funzionalità di codifica avanzata per i flussi di lavoro Premium su richiesta.

Gli argomenti seguenti includono informazioni dettagliate sul **flusso di lavoro Premium del codificatore multimediale**:

* [Formati supportati da Flusso di lavoro Premium del codificatore multimediale](media-services-premium-workflow-encoder-formats.md) : vengono illustrati formati file e codec supportati da **Flusso di lavoro Premium del codificatore multimediale**.
* [Panoramica e confronto dei codificatori multimediali su richiesta di Azure](media-services-encode-asset.md) mette a confronto le funzionalità di codifica del **flusso di lavoro Premium del codificatore multimediale** e di **Media Encoder Standard**.

Questo articolo illustra come codificare con il **flusso di lavoro Premium del codificatore multimediale** mediante .NET.

Le attività di codifica per **Flusso di lavoro Premium del codificatore multimediale** richiedono un file di configurazione separato, denominato file del flusso di lavoro. Questi file con estensione workflow vengono creati mediante lo strumento [Progettazione flussi di lavoro](media-services-workflow-designer.md) .

I file del flusso di lavoro predefiniti sono disponibili anche [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Nella cartella è presente anche una descrizione dei file.

I file del flusso di lavoro devono essere caricati come asset nel proprio account di Servizi multimediali e questo asset deve essere passato all'attività di codifica.

## <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

Configurare l'ambiente di sviluppo e popolare il file app.config con le informazioni di connessione, come descritto in [Sviluppo di applicazioni di Servizi multimediali con .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Esempio di codifica

Il seguente esempio dimostra come codificare con **Flusso di lavoro Premium del codificatore multimediale**.

Vengono eseguiti questi passaggi:

1. Creare un asset e caricare un file del flusso di lavoro.
2. Creare un asset e caricare un file multimediale di origine.
3. Ottenere il processore di contenuti multimediali "Flusso di lavoro Premium del codificatore multimediale".
4. Creare un processo e un'attività.

    Nella maggior parte dei casi, la stringa di configurazione per l'attività è vuota (come nell'esempio seguente). Esistono alcuni scenari avanzati in cui è necessario impostare dinamicamente le proprietà di runtime. In questo caso, specificare una stringa XML nell'attività di codifica. Esempi di tali scenari sono: creazione di un overlay, unione sequenziale o parallela di supporti e aggiunta di sottotitoli.
5. Aggiungere due asset di input all'attività.

    1. In primo luogo, l'asset del flusso di lavoro.
    2. In secondo luogo, l'asset video.

    >[!NOTE]
    >La risorsa del flusso di lavoro deve essere aggiunta all'attività prima della risorsa del file multimediale.
   La stringa di configurazione per questa attività deve essere vuota.
   
6. Inviare il processo di codifica.

```
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
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
```

Per domande relative al codificatore Premium, inviare un messaggio di posta elettronica a mepd@microsoft.com.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
