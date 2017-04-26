---
title: Sviluppare le Funzioni di Azure con Servizi multimediali
description: In questo argomento viene illustrato come avviare lo sviluppo di Funzioni di Azure con Servizi multimediali tramite il Portale di Azure.
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: ca7c89dc5f56f1bf4c300874111e65048e88abcf
ms.lasthandoff: 04/12/2017


---
#<a name="develop-azure-functions-with-media-services"></a>Sviluppare le Funzioni di Azure con Servizi multimediali

In questo argomento viene illustrato come avviare lo sviluppo di Funzioni di Azure con Servizi multimediali tramite il Portale di Azure. 

È inoltre possibile distribuire [Funzioni di Azure dei Servizi multimediali](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) esistenti premendo il pulsante **Distribuisci in Azure**. Questo repository contiene esempi di Funzioni di Azure che usano Servizi multimediali di Azure per visualizzare i flussi di lavoro correlati all'inserimento di contenuto direttamente dall'archiviazione BLOB, alla codifica e alla scrittura del contenuto nell'archiviazione BLOB. Include inoltre esempi su come monitorare le notifiche dei processi tramite i webhook e le code di Azure. È inoltre possibile sviluppare le funzioni in base agli esempi nel repository [Funzioni di Azure dei Servizi multimediali](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). 

In questo argomento viene illustrato come iniziare a creare le Funzioni di Azure che usano i Servizi multimediali. La funzione di Azure definita in questo argomento consente di monitorare un contenitore di account di archiviazione denominato **input** per i nuovi file MP4. Una volta rilasciato un file nel contenitore di archiviazione, il trigger BLOB eseguirà la funzione.

## <a name="prerequisites"></a>Prerequisiti

- Per poter creare la prima funzione, è necessario avere un account Azure attivo. Se non si possiede già un account Azure, [sono disponibili account gratuiti](https://azure.microsoft.com/free/).
- Se si intende creare le Funzioni di Azure per eseguire azioni sull'account dei Servizi multimediali di Azure o ascoltare gli eventi inviati dai Servizi multimediali, è necessario creare un account AMS, come descritto [qui](media-services-portal-create-account.md).
- Comprensione della [modalità d'uso delle funzioni di Azure](../azure-functions/functions-overview.md). Inoltre, esaminare:
    - [Associazioni HTTP e webhook in Funzioni di Azure](../azure-functions/functions-triggers-bindings.md)
    - [Come configurare le impostazioni dell'app per le funzioni di Azure](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
    
## <a name="considerations"></a>Considerazioni

-  Le funzioni di Azure in esecuzione con piano a consumo hanno un timeout di 5 minuti.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

1. Passare al [portale di Azure](http://portal.azure.com) e accedere con il proprio account Azure.
2. Creare un'app per le funzioni come descritto [qui](../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Un account di archiviazione specificato nella variabile di ambiente **StorageConnection** (vedere il passaggio successivo) deve essere nella stessa area dell'app.

## <a name="configure-function-app-settings"></a>Configurare le impostazioni dell'app per le funzioni

Quando si sviluppano le funzioni di Servizi multimediali, è utile aggiungere variabili di ambiente che verranno usati nelle funzioni. Per configurare le impostazioni dell'app, fare clic sul collegamento Configurare le impostazioni dell'app. Per altre informazioni, vedere [Come configurare le impostazioni dell'app per le funzioni di Azure](../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Ad esempio:

![Impostazioni](./media/media-services-azure-functions/media-services-azure-functions001.png)

Per la funzione definita in questo articolo si presuppongono le seguenti variabili di ambiente nelle impostazioni dell'app:

**AMSAccount**: *nome dell'account AMS* (ad esempio testams)

**AMSKey** : *chiave dell'account AMS* (ad esempio, IHOySnH+XX3LGPfraE5fKPl0EnzvEPKkOPKCr59aiMM=)

**MediaServicesStorageAccountName** : *nome dell'account di archiviazione* (ad esempio, testamsstorage)

**MediaServicesStorageAccountKey** : *chiave dell'account di archiviazione* (ad esempio, xx7RN7mvpcipkuXvn5g7jwxnKh5MwYQ/awZAzkSIxQA8tmCtn93rqobjgjt41Wb0zwTZWeWQHY5kSZF0XXXXXX==)

**StorageConnection** : *connessione di archiviazione* (ad esempio, DefaultEndpointsProtocol=https;AccountName=testamsstorage;AccountKey=xx7RN7mvpcipkuXvn5g7jwxnKh5MwYQ/awZAzkSIxQA8tmCtn93rqobjgjt41Wb0zwTZWeWQHY5kSZF0XXXXX==)

## <a name="create-a-function"></a>Creare una funzione

In seguito alla distribuzione dell'app per le funzioni, questa verrà visualizzata tra le Funzioni di Azure dei **Servizi app**.

1. Selezionare l'app per le funzioni e fare clic su **Nuova funzione**.
2. Scegliere il linguaggio **C#** e lo scenario **Elaborazione dati**.
3. Scegliere il modello **BlobTrigger**. Questa funzione verrà attivata ogni volta che viene caricato un BLOB nel contenitore di **input**. Il nome **input** è specificato nel **percorso**, nel passaggio successivo.

    ![input](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Dopo aver selezionato **BlobTrigger**, altri controlli verranno visualizzati nella pagina.

    ![input](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Fare clic su **Crea**. 


## <a name="files"></a>File

La funzione di Azure è associata al file del codice e ad altri file descritti in questa sezione. Per impostazione predefinita, una funzione è associata ai file **function.json** e **run.csx** (C#). Sarà necessario aggiungere un file **project.json**. La parte successiva di questa sezione illustra le definizioni per questi file.

![input](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Il file function.json definisce le associazioni di funzione e altre impostazioni di configurazione. Il runtime usa questo file per determinare gli eventi da monitorare e come passare i dati e restituirli dall'esecuzione di funzioni. Per altre informazioni rivedere [Associazioni HTTP e webhook in Funzioni di Azure](../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Impostare la proprietà **disattivato** su **vero** per impedire l'esecuzione della funzione. 


Di seguito è riportato un esempio di file **function.json**.

    {
    "bindings": [
      {
        "name": "myBlob",
        "type": "blobTrigger",
        "direction": "in",
        "path": "input/{fileName}.mp4",
        "connection": "StorageConnection"
      }
    ],
    "disabled": false
    }

### <a name="projectjson"></a>project.json

Il file project.json contiene dipendenze. Di seguito è riportato un esempio del file **project.json** che include i pacchetti di Servizi multimediali di Azure .NET da Nuget. Si noti che i numeri di versione cambieranno con gli aggiornamenti più recenti per i pacchetti, pertanto è consigliabile confermare le versioni più recenti. 

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

Questo è il codice C# per la funzione.  La funzione definita di seguito monitora un contenitore dell'account di archiviazione denominato **input** (cioè quello specificato nel percorso) per i nuovi file MP4. Una volta rilasciato un file nel contenitore di archiviazione, il trigger BLOB eseguirà la funzione.
    
L'esempio viene definito in questa sezione illustra 

1. come inserire un asset in un account di Servizi multimediali (copiando un BLOB in un asset AMS) e 
2. come inviare un processo di codifica che usa il set di impostazioni "Flusso adattivo" di Media Encoder Standard.

Nello scenario reale, è probabile che l'utente desideri tenere traccia dello stato dei processi e quindi pubblicare l'asset codificato. Per altre informazioni, vedere [Usare i webhook di Azure per monitorare le notifiche dei processi di Servizi multimediali con .NET](media-services-dotnet-check-job-progress-with-webhooks.md). Per altri esempi, vedere [Funzioni di Azure dei Servizi multimediali](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Al termine dell'operazione di definizione della funzione fare clic su **Salva ed esegui**.

    #r "Microsoft.WindowsAzure.Storage"
    #r "Newtonsoft.Json"
    #r "System.Web"

    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Auth;

    private static readonly string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    private static readonly string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");

    static string _storageAccountName = Environment.GetEnvironmentVariable("MediaServicesStorageAccountName");
    static string _storageAccountKey = Environment.GetEnvironmentVariable("MediaServicesStorageAccountKey");

    private static CloudStorageAccount _destinationStorageAccount = null;

    // Field for service context.
    private static CloudMediaContext _context = null;
    private static MediaServicesCredentials _cachedCredentials = null;

    public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
    {
        // NOTE that the variables {fileName} here come from the path setting in function.json
        // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
        // was dropped into the input container for the function. 

        // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
        // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

        log.Info($"C# Blob trigger function processed: {fileName}.mp4");
        log.Info($"Using Azure Media Services account : {_mediaServicesAccountName}");


        try
        {
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                _mediaServicesAccountName,
                _mediaServicesAccountKey);

        // Used the chached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);

        // Step 1:  Copy the Blob into a new Input Asset for the Job
        // ***NOTE: Ideally we would have a method to ingest a Blob directly here somehow. 
        // using code from this sample - https://azure.microsoft.com/en-us/documentation/articles/media-services-copying-existing-blob/

        StorageCredentials mediaServicesStorageCredentials =
            new StorageCredentials(_storageAccountName, _storageAccountKey);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

        }
        catch (Exception ex)
        {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
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


    public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
        IAsset newAsset = null;

        try{
            Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
            newAsset = await copyAssetTask;
            log.Info($"Asset Copied : {newAsset.Id}");
        }
        catch(Exception ex){
            log.Info("Copy Failed");
            log.Info($"ERROR : {ex.Message}");
            throw ex;
        }

        return newAsset;
    }

    /// <summary>
    /// Creates a new asset and copies blobs from the specifed storage account.
    /// </summary>
    /// <param name="blob">The specified blob.</param>
    /// <returns>The new asset.</returns>
    public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
    {
         //Get a reference to the storage account that is associated with the Media Services account. 
        StorageCredentials mediaServicesStorageCredentials =
        new StorageCredentials(_storageAccountName, _storageAccountKey);
        _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);

        // Create a new asset. 
        var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
        log.Info($"Created new asset {asset.Name}");

        IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
        TimeSpan.FromHours(4), AccessPermissions.Write);
        ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
        CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

        // Get the destination asset container reference
        string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
        CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

        try{
        assetContainer.CreateIfNotExists();
        }
        catch (Exception ex)
        {
        log.Error ("ERROR:" + ex.Message);
        }

        log.Info("Created asset.");

        // Get hold of the destination blob
        CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

        // Copy Blob
        try
        {
        using (var stream = await blob.OpenReadAsync()) 
        {            
            await destinationBlob.UploadFromStreamAsync(stream);          
        }

        log.Info("Copy Complete.");

        var assetFile = asset.AssetFiles.Create(blob.Name);
        assetFile.ContentFileSize = blob.Properties.Length;
        assetFile.IsPrimary = true;
        assetFile.Update();
        asset.Update();
        }
        catch (Exception ex)
        {
        log.Error(ex.Message);
        log.Info (ex.StackTrace);
        log.Info ("Copy Failed.");
        throw;
        }

        destinationLocator.Delete();
        writePolicy.Delete();

        return asset;
    }
##<a name="test-your-function"></a>Testare la funzione

Per testare la funzione, è necessario caricare un file MP4 nel contenitore **input** dell'account di archiviazione specificato nella stringa di connessione.  

## <a name="next-step"></a>Passaggio successivo

A questo punto, si è pronti per iniziare a sviluppare un'applicazione di Servizi multimediali. 
 
Per altri dettagli ed esempi o soluzioni complete di uso di Funzioni di Azure e delle App per la logica con Servizi multimediali di Azure per creare flussi di lavoro di creazione di contenuto personalizzato, vedere l'[Esempio di integrazione delle funzioni di Servizi multimediali .NET su GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Vedere anche [Usare i webhook di Azure per monitorare le notifiche dei processi di Servizi multimediali con .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


