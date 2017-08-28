---
title: Offuscare i volti con Analisi Servizi multimediali di Azure | Documentazione Microsoft
description: Questo argomento illustra come offuscare i volti con Analisi Servizi multimediali di Azure.
services: media-services
documentationcenter: 
author: juliako
manager: SyntaxC4
editor: 
ms.assetid: 5b6d8b8c-5f4d-4fef-b3d6-dc22c6b5a0f5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako;
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 74f38ae61d4a360cabe8a9fbd33d30a6b0751654
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---
# <a name="redact-faces-with-azure-media-analytics"></a>Offuscare i volti con Analisi Servizi multimediali di Azure
## <a name="overview"></a>Panoramica
**Azure Media Redactor** è un processore di contenuti multimediali di [Analisi Servizi multimediali di Azure](media-services-analytics-overview.md) che offre funzionalità scalabili di offuscamento dei volti nel cloud. L'offuscamento dei volti consente di modificare un video per sfocare i volti di persone selezionate. Può essere opportuno usare tale servizio in scenari di pubblica sicurezza e notizie giornalistiche. Offuscare manualmente alcuni minuti di filmato contenenti più volti può richiedere ore, ma con questo servizio il processo di offuscamento dei volti richiederà pochi semplici passaggi. Per altre informazioni, vedere [questo](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Questo argomento contiene informazioni dettagliate su **Azure Media Redactor** e illustra come usare questa funzionalità con Media Services SDK per .NET.

Il processore di contenuti multimediali **Azure Media Redactor** è attualmente in anteprima. È disponibile in tutte le aree di Azure pubbliche, nonché nei data center cinesi e del governo degli USA. Al momento questa versione di anteprima è disponibile gratuitamente. 

## <a name="face-redaction-modes"></a>Modalità per l'offuscamento dei volti
La funzionalità di offuscamento dei volti rileva i volti in ogni fotogramma del video e monitora l'oggetto volto avanti e indietro nel tempo in modo da consentire la sfocatura della stessa persona anche da altre angolazioni. Il processo di offuscamento automatizzato è molto complesso e non sempre produce al 100% l'output desiderato. Per tale motivo, Analisi Servizi multimediali offre alcuni modi per modificare l'output finale.

In aggiunta a una modalità interamente automatica, esiste un flusso di lavoro in due passaggi che consente di selezionare/deselezionare i volti trovati tramite un elenco di ID. Per apportare modifiche arbitrarie per singolo fotogramma, inoltre, il processore di contenuti multimediali usa un file di metadati in formato JSON. Il flusso di lavoro è suddiviso nelle modalità **analisi** e **offuscamento**. È possibile combinare le due modalità in un singolo passaggio che esegue entrambe le attività in un unico processo. Questa modalità è detta **combinata**.

### <a name="combined-mode"></a>Modalità combinata
Questa modalità produce automaticamente un file mp4 offuscato senza alcun input manuale.

| Fase | File Name | Note |
| --- | --- | --- |
| Asset di input |foo.bar |Video in formato WMV, MOV o MP4 |
| Configurazione di input |Set di impostazioni di configurazione del processo |{'version':'1.0', 'options': {'mode':'combined'}} |
| Asset di output |foo_redacted.mp4 |Video con sfocatura applicata |

#### <a name="input-example"></a>Esempio di input:
[Guardare il video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Esempio di output:
[Guardare il video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Modalità analisi
Nel flusso di lavoro in due passaggi, il passaggio dell' **analisi** usa un input video e produce un file JSON di posizioni di volti e immagini jpg di ogni volto rilevato.

| Fase | File Name | Note |
| --- | --- | --- |
| Asset di input |foo.bar |Video in formato WMV, MPV o MP4 |
| Configurazione di input |Set di impostazioni di configurazione del processo |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Asset di output |foo_annotations.json |Dati di annotazione delle posizioni dei volti in formato JSON, modificabili dall'utente per modificare i rettangoli di selezione della sfocatura. Vedere l'esempio di seguito. |
| Asset di output |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |File jpg ritagliato di ogni volto rilevato, in cui il numero indica l'ID etichetta del volto |

#### <a name="output-example"></a>Esempio di output:

    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated

### <a name="redact-mode"></a>Modalità offuscamento
Il secondo passaggio del flusso di lavoro usa un numero superiore di input che devono essere combinati in un singolo asset.

Gli input includono un elenco di ID da sfocare, il video originale e il file JSON delle annotazioni. Questa modalità usa le annotazioni per applicare la sfocatura nel video di input.

L'output del passaggio dell'analisi non include il video originale. Il video deve essere caricato nell'asset di input per l'attività della modalità offuscamento ed essere selezionato come file primario.

| Fase | File Name | Note |
| --- | --- | --- |
| Asset di input |foo.bar |Video in formato WMV, MPV o MP4. Stesso video del passaggio 1. |
| Asset di input |foo_annotations.json |File di metadati delle annotazioni della prima fase, con modifiche facoltative. |
| Asset di input |foo_IDList.txt (facoltativo) |Elenco facoltativo separato da caratteri di nuova riga di ID volto da offuscare. Se viene lasciato vuoto, vengono sfocati tutti i volti. |
| Configurazione di input |Set di impostazioni di configurazione del processo |{'version':'1.0', 'options': {'mode':'redact'}} |
| Asset di output |foo_redacted.mp4 |Video con sfocatura applicata in base alle annotazioni. |

#### <a name="example-output"></a>Output di esempio
Questo output viene ottenuto da un elenco di ID con un ID selezionato.

[Guardare il video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Esempio foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Tipi di sfocature

Nella modalità **Combined** o **Redact**, sono disponibili 5 modalità di sfocatura diverse tra cui scegliere tramite la configurazione di input JSON: **Low**, **Med**, **High**, **Debug** e **Black**. Per impostazione predefinita, viene usata **Med**.

Di seguito sono riportati alcuni esempi dei tipi di sfocature.

### <a name="example-json"></a>JSON di esempio:

    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}

#### <a name="low"></a>Basso

![Basso](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Alto

![Alto](./media/media-services-face-redaction/blur3.png)

#### <a name="debug"></a>Debug

![Debug](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Nero

![Nero](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementi del file di output JSON

Il processore di contenuti multimediali per l'offuscamento offre funzionalità di rilevamento della posizione e monitoraggio dei volti ad alta precisione che possono rilevare fino a 64 volti umani in un fotogramma video. Le riprese anteriori producono risultati ottimali, mentre profili e volti di piccole dimensioni (inferiori o uguali a 24x24 pixel) presentano alcune problematiche.

[!INCLUDE [media-services-analytics-output-json](../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Codice di esempio .NET

Il programma seguente illustra come:

1. Creare un asset e caricare un file multimediale nell'asset.
2. Creare un processo con un'attività di offuscamento dei volti in base a un file di configurazione contenente il set di impostazioni JSON seguente. 
   
        {'version':'1.0', 'options': {'mode':'combined'}}
3. Scaricare i file JSON di output. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

Configurare l'ambiente di sviluppo e popolare il file app.config con le informazioni di connessione, come descritto in [Sviluppo di applicazioni di Servizi multimediali con .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Esempio

    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace FaceRedaction
    {
        class Program
        {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Collegamenti correlati
[Panoramica di Analisi servizi multimediali di Azure](media-services-analytics-overview.md)

[Demo di Analisi servizi multimediali di Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)


