---
title: Usare Azure Media Encoder Standard per generare automaticamente un bitrate ladder | Microsoft Docs
description: "In questo argomento viene illustrato come usare Media Encoder Standard (MES) per generare automaticamente un bitrate ladder in base alla risoluzione di input e alla velocità in bit. La risoluzione di input e la velocità in bit non vengono mai superate. Ad esempio, se l'input è 720p a 3 Mbps, l'output resterà al massimo a 720p e inizierà a una velocità inferiore a 3 Mbps."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 63ed95da-1b82-44b0-b8ff-eebd535bc5c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: e4bc03c624c9930d7a9b0bef22d3179633de3365
ms.contentlocale: it-it
ms.lasthandoff: 07/24/2017

---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Usare Azure Media Encoder Standard per generare automaticamente un bitrate ladder

## <a name="overview"></a>Panoramica

In questo argomento viene illustrato come usare Media Encoder Standard (MES) per generare automaticamente un bitrate ladder (coppia risoluzione-velocità in bit) in base alla risoluzione di input e alla velocità in bit. Il set di impostazioni generate automaticamente non supererà mai la risoluzione di input e la velocità in bit. Ad esempio, se l'input è 720p a 3 Mbps, l'output resterà al massimo a 720p e inizierà a una velocità inferiore a 3 Mbps.

### <a name="encoding-for-streaming-only"></a>Codifica solo per lo streaming

Se si intende codificare il video di origine solo per lo streaming, è necessario usare il set di impostazioni "Flusso adattivo" quando si crea un'attività di codifica. Quando si usa il set di impostazioni **Flusso adattivo** il codificatore MES userà in modo intelligente un bitrate ladder. Tuttavia, non sarà possibile controllare i costi di codifica, poiché il servizio determina il numero di livelli da usare e la risoluzione. È possibile vedere esempi dei livelli di output prodotti da MES in seguito alla codifica con il set di impostazioni **Flusso adattivo** alla fine di questo argomento. L'asset di output conterrà i file MP4 in cui audio e video non sono di tipo Interleaved.

### <a name="encoding-for-streaming-and-progressive-download"></a>Codifica per streaming e download progressivo

Se si intende codificare un video di origine per lo streaming e per produrre file MP4 per il download progressivo, è necessario usare il set di impostazioni "Content Adaptive Multiple Bitrate MP4" durante la creazione di un'attività di codifica. Quando si usa il set di impostazioni **Content Adaptive Multiple Bitrate MP4**, il codificatore MES applicherà la stessa logica di codifica illustrata in precedenza, ma ora l'asset di output conterrà file MP4 in cui audio e video sono di tipo Interleaved. È possibile usare uno di questi file MP4 (ad esempio la versione con bitrate più elevato) come file di download progressivo.

## <a id="encoding_with_dotnet"></a>Codifica con l’SDK .NET dei servizi multimediali

Il seguente codice usa l'SDK .NET di Servizi multimediali per eseguire le seguenti attività: 

- Creare un processo di codifica.
- Ottenere un riferimento al codificatore Media Encoder Standard.
- Aggiungere un'attività di codifica al processo e specificare l'uso del set di impostazioni **Flusso adattivo**. 
- Creare un asset di output che conterrà l'asset codificato.
- Aggiungere un gestore eventi per controllare l'avanzamento del processo.
- Inviare il processo.

#### <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

Configurare l'ambiente di sviluppo e popolare il file app.config con le informazioni di connessione, come descritto in [Sviluppo di applicazioni di Servizi multimediali con .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Esempio

    using System;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;

    namespace AdaptiveStreamingMESPresest
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


