---
title: Sviluppare le Funzioni di Azure con Servizi multimediali
description: In questo argomento viene illustrato come avviare lo sviluppo di Funzioni di Azure con Servizi multimediali tramite il Portale di Azure.
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: f99fe340b6cfebaafb04af9dba8abf9cb0f09a2b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="develop-azure-functions-with-media-services"></a>Sviluppare le Funzioni di Azure con Servizi multimediali

Questo articolo illustra come iniziare con la creazione di funzioni di Azure che utilizzano i servizi di supporto. La funzione di Azure definito in questo articolo consente di monitorare un contenitore di account di archiviazione denominato **input** per i nuovi file MP4. Una volta che viene rilasciato un file nel contenitore di archiviazione, la funzione viene eseguito il trigger di blob. Per le funzioni di Azure, vedere [Panoramica](../azure-functions/functions-overview.md) e altri argomenti della sezione **Funzioni di Azure**.

Se si vuole esplorare e distribuire le Funzioni di Azure esistenti che usano i Servizi multimediali di Azure, estrarre [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) (Funzioni di Azure di Servizi multimediali). Questo repository contiene esempi che usano Servizi multimediali per visualizzare i flussi di lavoro correlati all'inserimento di contenuto direttamente dall'archiviazione BLOB, alla codifica e alla scrittura del contenuto nell'archiviazione BLOB. Include inoltre esempi su come monitorare le notifiche dei processi tramite i webhook e le code di Azure. È inoltre possibile sviluppare le funzioni in base agli esempi nel repository [Funzioni di Azure dei Servizi multimediali](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Per distribuire le funzioni, premere il pulsante **Distribuisci in Azure**.

## <a name="prerequisites"></a>Prerequisiti

- Per poter creare la prima funzione, è necessario avere un account Azure attivo. Se non si possiede già un account Azure, [sono disponibili account gratuiti](https://azure.microsoft.com/free/).
- Se si intende creare le Funzioni di Azure per eseguire azioni sull'account dei Servizi multimediali di Azure o ascoltare gli eventi inviati dai Servizi multimediali, è necessario creare un account AMS, come descritto [qui](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Creare un'app per le funzioni

1. Passare al [portale di Azure](http://portal.azure.com) e accedere con il proprio account Azure.
2. Creare un'app per le funzioni come descritto [qui](../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Un account di archiviazione specificato nella variabile di ambiente **StorageConnection** (vedere il passaggio successivo) deve essere nella stessa area dell'app.

## <a name="configure-function-app-settings"></a>Configurare le impostazioni dell'app per le funzioni

Quando si sviluppano le funzioni di Servizi multimediali, è utile aggiungere variabili di ambiente che verranno usati nelle funzioni. Per configurare le impostazioni dell'app, fare clic sul collegamento Configurare le impostazioni dell'app. Per altre informazioni, vedere [Come configurare le impostazioni dell'app per le funzioni di Azure](../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Per la funzione definita in questo articolo si presuppongono le seguenti variabili di ambiente nelle impostazioni dell'app:

**AMSAADTenantDomain**: endpoint tenant di Azure AD. Per altre informazioni sulla connessione alle API di Servizi multimediali di Azure, vedere [questo](media-services-use-aad-auth-to-access-ams-api.md) articolo.

**AMSRESTAPIEndpoint**: URI che rappresenta l'endpoint dell'API REST. 

**AMSClientId**: ID client dell'applicazione di Azure AD.

**AMSClientSecret**: segreto client dell'applicazione Azure AD.

**StorageConnection**: connessione di archiviazione dell'account associato all'account di servizi multimediali. Questo valore è usato nei file **function.json** e **run.csx** (descritti di seguito).

## <a name="create-a-function"></a>Creare una funzione

In seguito alla distribuzione dell'app per le funzioni, questa verrà visualizzata tra le Funzioni di Azure dei **Servizi app**.

1. Selezionare l'app per le funzioni e fare clic su **Nuova funzione**.
2. Scegliere il linguaggio **C#** e lo scenario **Elaborazione dati**.
3. Scegliere il modello **BlobTrigger**. Questa funzione viene attivata ogni volta che un blob viene caricato nel **input** contenitore. Il nome **input** è specificato nel **percorso**, nel passaggio successivo.

    ![input](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Dopo aver selezionato **BlobTrigger**, alcuni ulteriori controlli vengono visualizzati nella pagina.

    ![input](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Fare clic su **Crea**. 

## <a name="files"></a>File

La funzione di Azure è associata al file del codice e ad altri file descritti in questa sezione. Quando si usa il portale di Azure per creare una funzione, **function.json** e **run.csx** vengono creati automaticamente. Sarà necessario aggiungere o caricare un file **project.json**. Il resto di questa sezione fornisce una breve spiegazione di ogni file e mostra le relative definizioni.

![input](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Il file function.json definisce le associazioni di funzione e altre impostazioni di configurazione. Il runtime usa questo file per determinare gli eventi da monitorare e come passare i dati e restituirli dall'esecuzione di funzioni. Per altre informazioni rivedere [Associazioni HTTP e webhook in Funzioni di Azure](../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Impostare la proprietà **disattivato** su **vero** per impedire l'esecuzione della funzione. 

Sostituire il contenuto del file .json della funzione esistente con il codice seguente:

```
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

Il file project.json contiene dipendenze. Di seguito è riportato un esempio del file **project.json** che include i pacchetti di Servizi multimediali di Azure .NET da Nuget. Si noti che i numeri di versione modificare con gli aggiornamenti più recenti per i pacchetti, pertanto è necessario verificare le versioni più recenti. 

Aggiungere la seguente definizione .json. 

```
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx

Questo è il codice C# per la funzione.  La funzione definita di seguito monitora un contenitore dell'account di archiviazione denominato **input** (cioè quello specificato nel percorso) per i nuovi file MP4. Una volta che viene rilasciato un file nel contenitore di archiviazione, la funzione viene eseguito il trigger di blob.
    
L'esempio viene definito in questa sezione illustra 

1. come inserire un asset in un account di Servizi multimediali (copiando un BLOB in un asset AMS) e 
2. come inviare un processo di codifica che usa il set di impostazioni "Flusso adattivo" di Media Encoder Standard.

Nello scenario reale, è probabile che l'utente desideri tenere traccia dello stato dei processi e quindi pubblicare l'asset codificato. Per altre informazioni, vedere [Usare i webhook di Azure per monitorare le notifiche dei processi di Servizi multimediali con .NET](media-services-dotnet-check-job-progress-with-webhooks.md). Per altri esempi, vedere [Funzioni di Azure dei Servizi multimediali](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Sostituire il contenuto del file run.csx esistente con il codice seguente: dopo aver eseguito la definizione di una funzione fare clic su **salvare ed eseguire**.

```
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

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
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

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
```

##<a name="test-your-function"></a>Testare la funzione

Per testare la funzione, è necessario caricare un file MP4 nel contenitore **input** dell'account di archiviazione specificato nella stringa di connessione.  

1. Selezionare l'account di archiviazione specificato nella variabile di ambiente **StorageConnection**.
2. Fare clic su **Blob**.
3. Fare clic su **+ Contenitore**. Denominare il contenitore **input**.
4. Premere **caricare** e selezionare un file MP4 che si desidera caricare.

>[!NOTE]
> Quando si usa un trigger di tipo BLOB in un piano a consumo, è possibile che si verifichi un ritardo di un massimo di 10 minuti per l'elaborazione di nuovi BLOB in caso di inattività di un'app per le funzioni. Quando l'app per le funzioni è in esecuzione, i BLOB vengono elaborati immediatamente. Per altre informazioni, vedere [Trigger e associazioni di archiviazione BLOB](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob#blob-storage-triggers-and-bindings).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, si è pronti per iniziare a sviluppare un'applicazione di Servizi multimediali. 
 
Per altri dettagli ed esempi o soluzioni complete di uso di Funzioni di Azure e delle App per la logica con Servizi multimediali di Azure per creare flussi di lavoro di creazione di contenuto personalizzato, vedere l'[Esempio di integrazione delle funzioni di Servizi multimediali .NET su GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Vedere anche [Usare i webhook di Azure per monitorare le notifiche dei processi di Servizi multimediali con .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

