---
title: Codificare un asset con Media Encoder Standard mediante .NET | Microsoft Docs
description: Questo argomento illustra come usare .NET per codificare un asset con Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako;anilmur

---
# Codificare un asset con Media Encoder Standard mediante .NET
I processi di codifica sono tra le operazioni di elaborazione più frequenti in Servizi multimediali. Questi processi vengono creati per convertire i file multimediali da una codifica all'altra. Durante la codifica è possibile usare il codificatore multimediale incorporato in Servizi multimediali. È inoltre possibile usare un codificatore fornito da un partner di Servizi multimediali. I codificatori di terze parti sono disponibili tramite Azure Marketplace.

Questo argomento illustra come usare .NET per codificare gli asset con Media Encoder Standard (MES). Media Encoder Standard viene configurato mediante un set di impostazioni descritto [qui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

È consigliabile codificare sempre i file in formato intermedio con un set MP4 a velocità in bit adattiva e quindi convertire il set nel formato desiderato mediante la [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md). Per avvalersi della creazione dinamica dei pacchetti, è necessario ottenere prima almeno un'unità di streaming on demand per l'endpoint di streaming da cui si intende distribuire il contenuto. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md).

Se l'asset di output è protetto con crittografia di archiviazione, è necessario configurare i criteri di distribuzione degli asset. Per altre informazioni, vedere [Procedura: Configurare i criteri di distribuzione degli asset](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES produce un file di output con un nome contenente i primi 32 caratteri del nome del file di input. Il nome è basato su quanto specificato nel file preimpostato. Ad esempio, "NomeFile": "{Basename}\_{Index}{Extension}". {Basename} viene sostituito dai primi 32 caratteri del nome del file di input.
> 
> 

### Formati MES
[Codec e formati](media-services-media-encoder-standard-formats.md)

### Impostazioni predefinite MES
Media Encoder Standard viene configurato mediante un set di impostazioni descritto [qui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### Metadati di input e output
Quando si codifica un asset di input (o asset) tramite MES, al completamento dell'attività di codifica si ottiene un asset di output. L'asset di output contiene video, audio, anteprime, manifesto e così via a seconda del set di impostazioni di codifica che si usa.

L'asset di output include anche un file contenente i metadati dell'asset di input. Il nome del file XML dei metadati ha il seguente formato: <asset\_id>\_metadata.xml (ad esempio, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4\_metadata.xml), dove <asset\_id> è il valore AssetId dell'asset di input. Lo schema di questo file XML di metadati di input è descritto [qui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

L'asset di output include anche un file contenente i metadati dell'asset di output. Il nome del file XML dei metadati ha il seguente formato: <source_file_name>\_manifest.xml (ad esempio BigBuckBunny\_manifest.xml). Lo schema del file XML dei metadati di output viene descritto [qui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Se si desidera esaminare uno dei due file di metadati, è possibile creare un localizzatore SAS e scaricare il file nel computer locale. È possibile trovare un esempio su come creare un localizzatore SAS e scaricare un file tramite le estensioni dell'SDK .NET di Servizi multimediali.

## Scaricare un esempio
È possibile ottenere ed eseguire un esempio [qui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## Esempio
Il seguente codice usa l'SDK .NET di Servizi multimediali per eseguire le seguenti attività:

* Creare un processo di codifica.
* Ottenere un riferimento al codificatore Media Encoder Standard.
* Specificare l'utilizzo del set di impostazioni "H264 più velocità in bit 720p". È possibile visualizzare tutti i set di impostazioni [qui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). È inoltre possibile esaminare lo schema al quale questi set predefiniti devono essere conformi [qui](https://msdn.microsoft.com/library/mt269962.aspx).
* Aggiungere una singola attività di codifica al processo.
* Specificare l’asset di input da codificare.
* Creare un asset di output che conterrà l'asset codificato.
* Aggiungere un gestore eventi per controllare l'avanzamento del processo.
* Inviare il processo.
  
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
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


## Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## Vedere anche
[Come generare l'anteprima mediante Media Encoder Standard con .NET](media-services-dotnet-generate-thumbnail-with-mes.md) [Panoramica della codifica dei servizi multimediali](media-services-encode-asset.md)

<!---HONumber=AcomDC_0921_2016-->