---
title: Moderazione video - Content Moderator
titlesuffix: Azure Cognitive Services
description: Usare la moderazione video per analizzare possibile contenuto audace e per adulti.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: 9b6240ef0883e1a523c50c9b641065deb2669d53
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226536"
---
# <a name="video-moderation"></a>Moderazione video

Gli utenti di Internet oggigiorno generano miliardi di visualizzazioni video in tutti i siti Web di social media popolari e locali e il numero di visualizzazioni continua a crescere. Grazie ai servizi basati su Machine Learning che sono in grado di prevenire potenziali contenuti audaci e per adulti, è possibile ridurre il costo delle attività di moderazione.

## <a name="sign-up-for-the-content-moderator-media-processor-preview"></a>Eseguire l'iscrizione al processore di contenuti multimediali Content Moderator (anteprima)

### <a name="create-a-free-azure-account"></a>Creare un account di Azure gratuito

Se non si dispone già di un account di Azure, [iniziare da qui](https://azure.microsoft.com/free/) per crearne uno gratuito.

### <a name="create-an-azure-media-services-account"></a>Creare un account di Servizi multimediali di Azure

La funzionalità video di Content Moderator è disponibile gratuitamente come **elaboratore di contenuti multimediali** di anteprima pubblica in Servizi multimediali di Microsoft Azure (AMS).

[Creare un account di Servizi multimediali di Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) nella sottoscrizione di Azure.

### <a name="get-azure-active-directory-credentials"></a>Ottenere le credenziali di Azure Active Directory

   1. Per informazioni su come usare il portale di Azure per ottenere le credenziali di autenticazione di Azure AD, leggere l'[articolo sul portale di Servizi multimediali di Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).
   1. Per informazioni su come usare le credenziali di Azure Active Directory con l'SDK .NET, leggere l'[articolo su Servizi multimediali di Azure per .NET](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad).

   > [!NOTE]
   > Il codice di esempio disponibile in questa guida rapida usa il metodo di **autenticazione dell'entità servizio** descritto in entrambi gli articoli.

Dopo avere ottenuto le credenziali AMS, sono disponibili due modi per provare il processore di contenuti multimediali Content Moderator.

## <a name="use-azure-media-services-explorer"></a>Usare Azure Media Services Explorer

Usare lo strumento interattivo [Azure Media Services (AMS) Explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) per cercare l'account AMS, caricare i video ed esaminarli con il processore di contenuti multimediali Content Moderator. [Scaricarlo e installarlo](https://github.com/Azure/Azure-Media-Services-Explorer/releases) da GitHub, ed [esaminare il codice sorgente](http://github.com/Azure/Azure-Media-Services-Explorer) per approfondire l'uso dell'SDK AMS.

![Azure Media Services Explorer con Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>Avvio rapido di .NET con Visual Studio e C#

1. Aggiungere un nuovo progetto **App console (.NET Framework)** alla soluzione.

   Nel codice di esempio denominare il progetto **VideoModeration**.

1. Selezionare questo progetto come progetto di avvio singolo per la soluzione.

### <a name="install-required-packages"></a>Installare i pacchetti necessari

Installare i pacchetti NuGet seguenti disponibili in [NuGet](https://www.nuget.org/).

- windowsazure.mediaservices
- windowsazure.mediaservices.extensions

### <a name="update-the-programs-using-statements"></a>Aggiornare le istruzioni using del programma

Modificare le istruzioni using del programma.

    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.IO;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using System.Collections.Generic;


### <a name="initialize-application-specific-settings"></a>Inizializzare le impostazioni specifiche dell'applicazione

Aggiungere alla classe **Program** i campi statici seguenti in Program.cs.

    // declare constants and globals
    private static CloudMediaContext _context = null;
    private static CloudStorageAccount _StorageAccount = null;

    // Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
    // a list of Blobs to be processed.
    static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
    static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
    static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

    private static StorageCredentials _StorageCredentials = null;

    // Azure Media Services authentication. See the quickstart for how to get these.
    private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
    private const string CLIENT_ID = "YOUR CLIENT ID"
    private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

    // REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
    private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

    // Content Moderator Media Processor Nam
    private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

    // Input and Output files in the current directory of the executable
    private const string INPUT_FILE = "VIDEO FILE NAME";
    private const string OUTPUT_FOLDER = "";

### <a name="create-a-preset-file-json"></a>Creare un file predefinito (json)

Creare un file JSON nella directory corrente con il numero di versione.

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>Aggiungere il codice seguente al metodo Main

Il metodo main crea prima di tutto un contesto di Servizi multimediali di Azure e quindi un contesto di Archiviazione di Azure nel caso in cui i video si trovino in un archivio BLOB.
Il codice restante analizza un video da una cartella locale, uno o più BLOB all'interno di un contenitore di archiviazione di Azure.
È possibile provare tutte le opzioni commentando le altre righe di codice.

    // Create Azure Media Context
    CreateMediaContext();

    // Create Storage Context
    CreateStorageContext();

    // Use a file as the input.
    // IAsset asset = CreateAssetfromFile();
    
    // -- OR ---
    
    // Or a blob as the input
    // IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

    // Then submit the asset to Content Moderator
    // RunContentModeratorJob(asset);

    //-- OR ----

    // Just run the content moderator on all blobs in a list (from a Blob Container)
    RunContentModeratorJobOnBlobs();

### <a name="add-the-code-to-create-an-azure-media-context"></a>Aggiungere il codice per creare un contesto di Servizi multimediali di Azure

    /// <summary>
    /// Creates a media context from azure credentials
    /// </summary>
    static void CreateMediaContext()
    {
        // Get Azure AD credentials
        var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
            new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
            AzureEnvironments.AzureCloudEnvironment);

        // Initialize an Azure AD token
        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        // Create a media context
        _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
    }

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Aggiungere il codice per creare un contesto di Archiviazione di Azure
Si usa il contesto di Archiviazione, creato con le credenziali di Archiviazione di Azure, per accedere all'archivio BLOB.

    /// <summary>
    /// Creates a storage context from the AMS associated storage name and key
    /// </summary>
    static void CreateStorageContext()
    {
        // Get a reference to the storage account associated with a Media Services account. 
        if (_StorageCredentials == null)
        {
            _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
        }
        _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
    }

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Aggiungere il codice per creare asset multimediali di Azure da BLOB e file locali
Il processore di contenuti multimediali Content Moderator esegue i processi su **asset** all'interno della piattaforma Servizi multimediali di Azure.
Questi metodi creano gli asset da un file locale o un BLOB associato.

    /// <summary>
    /// Creates an Azure Media Services Asset from the video file
    /// </summary>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromFile()
    {
        return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
    }

    /// <summary>
    /// Creates an Azure Media Services asset from your blog storage
    /// </summary>
    /// <param name="Blob"></param>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
    {
        // Create asset from the FIRST blob in the list and return it
        return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
    }

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Aggiungere il codice per analizzare una raccolta di video (come BLOB) in un contenitore

    /// <summary>
    /// Runs the Content Moderator Job on all Blobs in a given container name
    /// </summary>
    static void RunContentModeratorJobOnBlobs()
    {
        // Get the reference to the list of Blobs. See the following method.
        var blobList = GetBlobsList();

        // Iterate over the Blob list items or work on specific ones as needed
        foreach (var sourceBlob in blobList)
        {
            // Create an Asset
            IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                               _StorageCredentials, AssetCreationOptions.None);
            asset.Update();

            // Submit to Content Moderator
            RunContentModeratorJob(asset);
        }
    }

    /// <summary>
    /// Get all blobs in your container
    /// </summary>
    /// <returns></returns>
    static IEnumerable<IListBlobItem> GetBlobsList()
    {
        // Get a reference to the Container within the Storage Account
        // that has the files (blobs) for moderation
        CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
        CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

        // Get the reference to the list of Blobs 
        var blobList = MediaBlobContainer.ListBlobs();
        return blobList;
    }

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Aggiungere il metodo per eseguire il processo di Content Moderator

    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
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
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

### <a name="add-a-couple-of-helper-functions"></a>Aggiungere un paio di funzioni helper

Questi metodi scaricano il file di output (JSON) di Content Moderator dall'asset di Servizi multimediali di Azure e aiutano a tenere traccia dello stato del processo di moderazione in modo che il programma possa registrare uno stato di esecuzione nella console.

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    // event handler for Job State
    static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job finished.");
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
                Console.WriteLine("Job is canceled.\n");
                break;
            case JobState.Error:
                Console.WriteLine("Job failed.\n");
                break;
            default:
                break;
        }
    }

### <a name="run-the-program-and-review-the-output"></a>Eseguire il programma ed esaminare l'output

Dopo che il processo di moderazione del contenuto è completato, analizzare la risposta JSON. La risposta include questi elementi:

- Riepilogo delle informazioni sul video
- **Catture** come "**frammenti**"
- **Fotogrammi chiave** come "**eventi**" con un flag **reviewRecommended "(= true o false)"** in base ai punteggi di **Adult** e **Racy**
- **start**, **duration**, **totalDuration** e **timestamp** sono espressi in "tick". Dividere per **timescale** per ottenere il numero in secondi.
 
> [!NOTE]

> - `adultScore` rappresenta la potenziale presenza e il punteggio di previsione di contenuto che potrebbe essere considerato sessualmente esplicito o per adulti in determinate situazioni.
> - `racyScore` rappresenta la potenziale presenza e il punteggio di previsione di contenuto che potrebbe essere considerato sessualmente esplicito o per adulti in determinate situazioni.
> - `adultScore` e `racyScore` sono compresi tra 0 e 1. Maggiore è il punteggio, più alto è il modello che prevede che la categoria possa essere applicabile. Questa anteprima si basa su un modello statistico piuttosto che su risultati codificati manualmente. Si consiglia di eseguire i test con i propri contenuti per determinare come ogni categoria si allinei alle proprie esigenze.
> - `reviewRecommended` è true o false a seconda delle soglie interne del punteggio. I clienti devono valutare se usare questo valore o optare per le soglie personalizzate in base ai propri criteri relativi ai contenuti.
>

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come generare [revisioni di video](video-reviews-quickstart-dotnet.md) dall'output della moderazione.

Aggiungere la [moderazione della trascrizione](video-transcript-moderation-review-tutorial-dotnet.md) nelle revisioni dei video.

Fare riferimento all'esercitazione dettagliata su come sviluppare una [soluzione completa di moderazione della trascrizione del video](video-transcript-moderation-review-tutorial-dotnet.md).

[Scaricare la soluzione di Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) per questo e altri avvi rapidi di Content Moderator per .NET.
