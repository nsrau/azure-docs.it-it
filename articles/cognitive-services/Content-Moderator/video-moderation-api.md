---
title: Analizzare contenuto video per individuare materiale inappropriato in C# - Content Moderator
titlesuffix: Azure Cognitive Services
description: Come analizzare contenuto video per individuare materiale inappropriato di tipo vario usando Content Moderator SDK per .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7e987c1249360b14fddf8af57c61fdd1a46ee6c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605337"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analizzare contenuto video per individuare materiale inappropriato in C#

Questo articolo offre informazioni ed esempi di codice per iniziare a usare [Content Moderator SDK per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) per analizzare contenuti video con lo scopo di individuare contenuti per adulti o spinti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).

## <a name="set-up-azure-resources"></a>Configurare le risorse di Azure

La funzionalità di moderazione dei video di Content Moderator è disponibile gratuitamente come **elaboratore di contenuti multimediali** di anteprima pubblica in Servizi multimediali di Microsoft Azure (AMS). Servizi multimediali di Azure è un servizio di Azure specializzato per l'archiviazione e lo streaming di contenuti video. 

### <a name="create-an-azure-media-services-account"></a>Creare un account di Servizi multimediali di Azure

Seguire le istruzioni in [Creare un account di Servizi multimediali di Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) per abbonarsi a AMS e creare un account di archiviazione di Azure associato. In tale account di archiviazione creare un nuovo contenitore di archiviazione BLOB.

### <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Passare alla nuova sottoscrizione AMS di AZURE nel portale di Azure e selezionare **Accesso all'API** nel menu laterale. Selezionare **Connettersi all'API Servizi multimediali di Azure con l'entità servizio**. Annotare il valore nel campo **Endpoint API REST**; sarà necessario in seguito.

Nella sezione **App Azure AD** selezionare **Crea nuovo** e assegnare un nome alla nuova registrazione dell'applicazione di Azure AD, ad esempio "VideoModADApp". Fare clic su **Salva** e attendere qualche minuto mentre l'applicazione viene configurata. Quindi si dovrebbe vedere la nuova registrazione dell'app nella sezione **App di Azure AD** della pagina.

Selezionare la registrazione dell'app e fare clic sul pulsante **Gestisci applicazione** sottostante. Annotare il valore nel campo **ID applicazione** che sarà necessario in seguito. Selezionare **Impostazioni** > **Chiavi** e immettere una descrizione per una nuova chiave, ad esempio "VideoModKey". Fare clic su **Salva** e quindi annotare il nuovo valore della chiave. Copiare questa stringa e conservarla in un luogo sicuro.

Per una procedura dettagliata più completa del processo precedente, vedere [Introduzione all'autenticazione di Azure AD](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

Dopo aver fatto questo, è possibile usare il processore di contenuti multimediali per la moderazione dei video in due modi diversi.

## <a name="use-azure-media-services-explorer"></a>Usare Azure Media Services Explorer

Azure Media Services Explorer è un semplice front-end per AMS. Usarlo per esplorare l'account AMS, caricare video ed esaminare contenuti con il processore di contenuti multimediali Content Moderator. Scaricarlo e installarlo da [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases) o vedere il [post di blog su Azure Media Services Explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) per altre informazioni.

![Azure Media Services Explorer con Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

1. In Visual Studio creare un nuovo progetto **App console (.NET Framework)** e denominarlo **VideoModeration**. 
1. Se la soluzione contiene anche altri progetti, selezionare questo come progetto di avvio singolo.
1. Ottenere i pacchetti NuGet necessari. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**, quindi trovare e installare i pacchetti seguenti:
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Aggiungere codice di moderazione video

Copiare e incollare nel progetto il codice contenuto in questa guida per implementare uno scenario di base di moderazione del contenuto.

### <a name="update-the-programs-using-statements"></a>Aggiornare le istruzioni using del programma

Aggiungere le istruzioni `using` seguenti all'inizio del file _Program.cs_.

```csharp
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
```

### <a name="set-up-resource-references"></a>Impostare riferimenti alle risorse

Aggiungere i campi statici seguenti alla classe **Program** in _Program.cs_. Questi campi contengono le informazioni necessarie per la connessione alla sottoscrizione AMS. Riempirli con i valori ottenuti nei passaggi precedenti. Si noti che `CLIENT_ID` è il valore **ID applicazione** dell'app Azure AD e `CLIENT_SECRET` è il valore della "VideoModKey" creata per l'app.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Se si desidera usare un file video locale, il caso più semplice, aggiungerlo al progetto e immettere il percorso come valore `INPUT_FILE`; i percorsi relativi sono relativi alla directory di esecuzione.

È necessario anche creare il file _preset.json_ nella directory corrente e usarlo per specificare un numero di versione. Ad esempio:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Caricare i video di input

Il metodo **Main** della classe **Program** crea un contesto di Servizi multimediali di Azure e quindi un contesto di Archiviazione di Azure nel caso in cui i video si trovino in un archivio BLOB. Il codice restante analizza un video da una cartella locale, uno o più BLOB all'interno di un contenitore di archiviazione di Azure. È possibile provare tutte le opzioni commentando le altre righe di codice.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Creare un contesto di Servizi multimediali di Azure

Aggiungere il metodo seguente alla classe **Program**. Usa le credenziali AMS per consentire la comunicazione con AMS.

```csharp
// Creates a media context from azure credentials
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
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Aggiungere il codice per creare un contesto di Archiviazione di Azure

Aggiungere il metodo seguente alla classe **Program**. Per accedere all'archivio BLOB si usa il contesto di Archiviazione creato con le credenziali di Archiviazione di Azure.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Aggiungere il codice per creare asset multimediali di Azure da BLOB e file locali

Il processore di contenuti multimediali Content Moderator esegue i processi su **asset** all'interno della piattaforma Servizi multimediali di Azure.
Questi metodi creano gli asset da un file locale o un BLOB associato.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Aggiungere il codice per analizzare una raccolta di video (come BLOB) in un contenitore

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
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

// Get all blobs in your container
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
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Aggiungere il metodo per eseguire il processo di Content Moderator

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
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
```

### <a name="add-helper-functions"></a>Aggiungere funzioni helper

Questi metodi scaricano il file di output (JSON) di Content Moderator dall'asset di Servizi multimediali di Azure e aiutano a tenere traccia dello stato del processo di moderazione in modo che il programma possa registrare uno stato di esecuzione nella console.

```csharp
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
```

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

```json
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
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come generare [revisioni di video](video-reviews-quickstart-dotnet.md) dall'output della moderazione.

Aggiungere la [moderazione della trascrizione](video-transcript-moderation-review-tutorial-dotnet.md) nelle revisioni dei video.

Fare riferimento all'esercitazione dettagliata su come sviluppare una [soluzione completa di moderazione della trascrizione del video](video-transcript-moderation-review-tutorial-dotnet.md).

[Scaricare la soluzione di Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) per questo e altri avvi rapidi di Content Moderator per .NET.
