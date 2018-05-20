---
title: Eseguire il caricamento, la codifica e lo streaming con Servizi multimediali di Azure | Microsoft Docs
description: Seguire i passaggi di questa esercitazione per caricare un file, codificare il video ed eseguire lo streaming dei contenuti con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: 1f0ce5599cce7fc830075e57af1bcba80d0e69e7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Esercitazione: Eseguire il caricamento, la codifica e lo streaming di video tramite API

Questa esercitazione illustra come eseguire il caricamento, la codifica e lo streaming di file video con Servizi multimediali di Azure. Se si vuole eseguire lo streaming di contenuti video nel formato MPEG DASH, CMAF o HLS di Apple, in modo da poterli riprodurre su una vasta gamma di browser e dispositivi, è necessario prima codificare il video e includerlo in un pacchetto in modo appropriato.

L'esercitazione illustra i passaggi da seguire per caricare un video, ma è anche possibile codificare i contenuti da rendere accessibili all'account di Servizi multimediali tramite un URL HTTPS.

![Riprodurre il video](./media/stream-files-tutorial-with-api/final-video.png)

Questa esercitazione illustra come:    

> [!div class="checklist"]
> * Avviare Azure Cloud Shell
> * Creare un account di Servizi multimediali
> * Accedere all'API di Servizi multimediali
> * Configurare l'app di esempio
> * Esaminare in dettaglio il codice
> * Eseguire l'app
> * Testare l'URL di streaming
> * Pulire le risorse

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Se Visual Studio non è installato, è possibile scaricare [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Scaricare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET di streaming usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code"></a>Esaminare il codice

Questa sezione esamina le funzioni definite nel file [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) del progetto *UploadEncodeAndStreamFiles*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Prima di tutto è necessario ottenere un token e quindi creare un oggetto **ClientCredential** dal token restituito. Nel codice che è stato clonato all'inizio dell'articolo, per ottenere il token viene usato l'oggetto **ArmClientCredential**.  

```csharp
private static IAzureMediaServicesClient CreateMediaServicesClient(ConfigWrapper config)
{
    ArmClientCredentials credentials = new ArmClientCredentials(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Creare un asset di input e caricare un file locale nell'asset 

La funzione **CreateInputAsset** crea un nuovo asset di input e carica al suo interno il file video locale specificato. Questo asset viene usato come input per il processo di codifica. In Servizi multimediali v3 l'input di un processo può essere costituito da un asset oppure da contenuti resi disponibili all'account di Servizi multimediali tramite URL HTTPS. Per informazioni su come codificare contenuti da un URL HTTPS, vedere [questo](job-input-from-http-how-to.md) articolo.  

In Servizi multimediali v3 si usano le API di Archiviazione di Azure per caricare i file. Il frammento di codice .NET seguente illustra come eseguire questa operazione.

La funzione esegue queste azioni:

* Crea un asset. 
* Ottiene un [URL di firma di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) scrivibile al [contenitore nel servizio di archiviazione](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) dell'asset.
* Carica il file nel contenitore nel servizio di archiviazione usando l'URL di firma di accesso condiviso.

```csharp
private static Asset CreateInputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName, string fileToUpload)
{
    // Check if an Asset already exists.
    Asset asset = client.Assets.Get(resourceGroupName, accountName, assetName);

    if (asset == null)
    {
        asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

        var response = client.Assets.ListContainerSas(
                resourceGroupName,
                accountName,
                assetName,
                permissions: AssetContainerPermission.ReadWrite,
                expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime()
            );

        var sasUri = new Uri(response.AssetContainerSasUrls.First());
        CloudBlobContainer container = new CloudBlobContainer(sasUri);
        var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
        blob.UploadFromFile(fileToUpload);
    }

    // In this sample method, we are going to assume that if an Asset already exists with the desired name, 
    // then we can go ahead an use it for encoding or analyzing.

    return asset;
}
```

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Creare un asset di output per archiviare il risultato di un processo 

L'asset di output archivia il risultato del processo di codifica. Il progetto definisce la funzione **DownloadResults** che scarica i risultati dall'asset di output nella cartella "output", consentendo così di visualizzare ciò che si è ottenuto.

```csharp
private static Asset CreateOutputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName)
{
    // Check if an Asset already exists
    Asset outputAsset = client.Assets.Get(resourceGroupName, accountName, assetName);
    Asset asset = new Asset();
    string outputAssetName = assetName;

    if (outputAsset != null)
    {
        // Name collision! In order to get the sample to work, let's just go ahead and create a unique asset name
        // Note that the returned Asset can have a different name than the one specified as an input parameter.
        // You may want to update this part to throw an Exception instead, and handle name collisions differently.
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        outputAssetName += uniqueness;
    }

    return client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, asset);
}
```

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Creare una trasformazione e un processo che codifica il file caricato
Quando si codificano o si elaborano contenuti in Servizi multimediali, è prassi comune configurare le impostazioni di codifica come una serie di istruzioni e quindi inviare un oggetto **Job**, ovvero il processo per applicare tali istruzioni a un video. Inviando nuovi processi per ogni nuovo video, si applicano le istruzioni a tutti i video nella libreria. In Servizi multimediali una serie di istruzioni di questo tipo è definita trasformazione ed è rappresentata dall'oggetto **Transform**. Per altre informazioni, vedere [Trasformazioni e processi](transform-concept.md). L'esempio illustrato in questa esercitazione definisce una serie di istruzioni che codifica il video per eseguirne lo streaming a diversi tipi di dispositivi iOS e Android. 

#### <a name="transform"></a>Trasformazione

Quando si crea una nuova istanza dell'oggetto **Transform**, è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto **TransformOutput**, come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. L'esempio descritto in questo articolo è basato su un set di impostazioni predefinito denominato **AdaptiveStreaming**. Il set di impostazioni codifica il video di input in una tabella di coppie velocità in bit-risoluzione generata automaticamente in base alla risoluzione e alla velocità in bit dell'input e genera file ISO MP4 con standard video H.264 e audio AAC corrispondente a ogni coppia velocità in bit-risoluzione. Per informazioni su questo set di impostazioni, vedere [Generazione automatica di una tabella di coppie velocità in bit-risoluzione](autogen-bitrate-ladder.md).

È possibile usare l'altro set di impostazioni predefinito EncoderNamedPreset oppure usare set di impostazioni personalizzati. 

Quando si crea un oggetto **Transform**, è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente.  In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

```csharp
private static Transform EnsureTransformExists(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Start by defining the desired outputs.
        TransformOutput[] outputs = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            }
        };

        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs);
    }

    return transform;
}
```

#### <a name="job"></a>Processo

Come indicato sopra, l'oggetto **Transform** è la serie di istruzioni, mentre l'oggetto **Job** è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. L'oggetto **Job** specifica informazioni come la posizione del video di input e quella dell'output.

In questo esempio il video di input è stato caricato dal computer locale. Per informazioni su come codificare contenuti da un URL HTTPS, vedere [questo](job-input-from-http-how-to.md) articolo.

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, 
    string resourceGroupName, 
    string accountName, 
    string transformName, 
    string jobName, 
    JobInput jobInput, 
    string outputAssetName)
{
    string uniqueJobName = jobName;
    Job job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

    if (job != null)
    {
        // Job already exists with the same name, so let's append a GUID
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        uniqueJobName += uniqueness;
    }

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
    };

    job = client.Jobs.Create(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job
        {
            Input = jobInput,
            Outputs = jobOutputs,
        });

    return job;
}
```

### <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

L'esempio di codice seguente illustra come eseguire il polling del servizio per determinare lo stato del processo. Il polling non è una procedura consigliata per le applicazioni di produzione poiché comporta rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi.

Griglia di eventi è un servizio progettato per garantire disponibilità elevata, coerenza nelle prestazioni e scalabilità dinamica. Con Griglia di eventi, le app possono rimanere in ascolto e reagire agli eventi praticamente da tutti i servizi di Azure, oltre che da origini personalizzate. Questo semplice servizio di gestione degli eventi, reattivo e basato su HTTP, consente di creare soluzioni efficienti tramite funzioni intelligenti di filtraggio e routing di eventi.  Vedere [Instradare gli eventi verso un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (lo stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Error**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Canceling** e, al termine, lo stato **Canceled**.

```csharp
private static Job WaitForJobToFinish(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName)
{
    int SleepInterval = 60 * 1000;

    Job job = null;

    while (true)
    {
        job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

        if (job.State == JobState.Finished || job.State == JobState.Error || job.State == JobState.Canceled)
        {
            break;
        }

        Console.WriteLine($"Job is {job.State}.");
        for (int i = 0; i < job.Outputs.Count; i++)
        {
            JobOutput output = job.Outputs[i];
            Console.Write($"\tJobOutput[{i}] is {output.State}.");
            if (output.State == JobState.Processing)
            {
                Console.Write($"  Progress: {output.Progress}");
            }
            Console.WriteLine();
        }
        System.Threading.Thread.Sleep(SleepInterval);
    }

    return job;
}
```

### <a name="get-a-streaminglocator"></a>Ottenere un oggetto StreamingLocator

Al termine della codifica, il passaggio successivo consiste nel rendere disponibile ai client il video presente nell'output per la riproduzione. È possibile eseguire questa operazione in due passaggi: creare prima un oggetto **StreamingLocator** e dopo gli URL di streaming che possono essere usati dai client. 

Il processo di creazione di un oggetto **StreamingLocator** è detto pubblicazione. Per impostazione predefinita, l'oggetto **StreamingLocator** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea un oggetto **StreamingLocator**, è necessario specificare il parametro **StreamingPolicyName** desiderato. In questo esempio si eseguirà lo streaming di contenuti in chiaro o non crittografati. È quindi possibile usare i criteri predefiniti per lo streaming non crittografato, **PredefinedStreamingPolicy.ClearStreamingOnly**.

> [!IMPORTANT]
> Quando si usa un oggetto StreamingPolicy personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riutilizzarli per gli oggetti StreamingLocator ogni volta che si devono usare gli stessi protocolli e opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di StreamingPolicy. Evitare quindi di creare un nuovo oggetto StreamingPolicy per ogni StreamingLocator.

Il codice seguente presuppone che la funzione venga chiamata con un parametro locatorName univoco.

```csharp
private static StreamingLocator CreateStreamingLocator(IAzureMediaServicesClient client,
                                                        string resourceGroup,
                                                        string accountName,
                                                        string assetName,
                                                        string locatorName)
{
    StreamingLocator locator =
        client.StreamingLocators.Create(resourceGroup,
        accountName,
        locatorName,
        new StreamingLocator()
        {
            AssetName = assetName,
            StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        });

    return locator;
}
```

Anche se l'esempio in questo argomento illustra un processo di streaming, è possibile usare la stessa chiamata per creare un oggetto StreamingLocator per la distribuzione di video tramite download progressivo.

### <a name="get-streaming-urls"></a>Ottenere URL di streaming

Ora che è stato creato un oggetto StreamingLocator, è possibile ottenere l'URL di streaming, come illustrato in **GetStreamingURLs**. Per creare un URL, è necessario concatenare il nome host **StreamingEndpoint** e il percorso **StreamingLocator**. In questo esempio, per **StreamingEndpoint** viene usato il valore *default*. Quando si crea un account di Servizi multimediali per la prima volta, l'oggetto **StreamingEndpoint** *default* è in stato arrestato ed è quindi necessario chiamare **Start**.

> [!NOTE]
> In questo metodo è necessario specificare il parametro locatorName usato per la creazione dell'oggetto **StreamingLocator** per l'asset di output.

```csharp
static IList<string> GetStreamingURLs(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    String locatorName)
{
    IList<string> streamingURLs = new List<string>();

    string streamingUrlPrefx = "";

    StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(resourceGroupName, accountName, "default");

    if (streamingEndpoint != null)
    {
        streamingUrlPrefx = streamingEndpoint.HostName;

        if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
            client.StreamingEndpoints.Start(resourceGroupName, accountName, "default");
    }

    foreach (var path in client.StreamingLocators.ListPaths(resourceGroupName, accountName, locatorName).StreamingPaths)
    {
        streamingURLs.Add("http://" + streamingUrlPrefx + path.Paths[0].ToString());
    }

    return streamingURLs;
}
```

### <a name="clean-up-resources-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano gli oggetti Transform e si salvano in modo permanente oggetti come StreamingLocator. Se dopo l'attività di sperimentazione si vuole pulire il proprio account, è necessario eliminare le risorse che non si prevede di riutilizzare.  Il codice seguente, ad esempio, elimina gli oggetti Job.

```csharp
static void CleanUp(IAzureMediaServicesClient client,
        string resourceGroupName,
        string accountName,
        string transformName)
{
    foreach (var job in client.Jobs.List(resourceGroupName, accountName, transformName))
    {
        client.Jobs.Delete(resourceGroupName, accountName, transformName, job.Name);
    }

    foreach (var asset in client.Assets.List(resourceGroupName, accountName))
    {
        client.Assets.Delete(resourceGroupName, accountName, asset.Name);
    }
}
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

1. Premere CTRL+F5 per eseguire l'applicazione *EncodeAndStreamFiles*.
2. Copiare uno degli URL di streaming dalla console.

Questo esempio visualizza gli URL che è possibile usare per riprodurre il video usando protocolli diversi:

![Output](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testare l'URL di streaming

Per testare lo streaming in questo articolo viene usato Azure Media Player. 

> [!NOTE]
> Se un lettore è ospitato in un sito https, assicurarsi di aggiornare l'URL impostandolo su "https".

1. Aprire un Web browser e passare a [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Nella casella **URL** incollare uno degli URL di streaming ottenuto quando si è eseguita l'applicazione. 
3. Scegliere il pulsante **Update Player** (Aggiorna il lettore).

Azure Media Player può essere usato a scopo di test ma non deve essere usato in un ambiente di produzione. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza. A tal fine è possibile usare lo strumento **CloudShell**.

In **CloudShell** eseguire il comando seguente:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Gli SDK di Servizi multimediali di Azure v3 non sono thread-safe. Quando si sviluppa un'applicazione multithreading, è necessario generare e usare un nuovo oggetto AzureMediaServicesClient per ogni thread.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come eseguire il caricamento, la codifica e lo streaming del video, vedere l'articolo seguente: 

> [!div class="nextstepaction"]
> [Analizzare i video](analyze-videos-tutorial-with-api.md)
