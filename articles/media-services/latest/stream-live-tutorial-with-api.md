---
title: Streaming live con Servizi multimediali di Azure v3 usando .NET Core | Microsoft Docs
description: Questa esercitazione illustra i passaggi per lo streaming live con Servizi multimediali v3 usando .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: f02820f8382268dfedeee8d07de2438660e5e33e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236883"
---
# <a name="stream-live-with-azure-media-services-v3-using-net-core"></a>Streaming live con Servizi multimediali di Azure v3 usando .NET Core

In Servizi multimediali le entità [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents)sono responsabili dell'elaborazione dei contenuti in streaming live. Un'entità LiveEvent fornisce un endpoint di input (URL di inserimento) che può essere a sua volta fornito al codificatore live. L'entità LiveEvent riceve flussi di input live dal codificatore live e li rende disponibili per lo streaming mediante uno o più [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints). Le entità LiveEvent forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito. Questa esercitazione illustra come usare .NET Core per creare un evento live di tipo **pass-through**. 

> [!NOTE]
> Assicurarsi di leggere [Live streaming with Azure Media Services v3](live-streaming-overview.md) (Streaming live con Servizi multimediali di Azure v3) prima di procedere. 

L'esercitazione illustra come:    

> [!div class="checklist"]
> * Creare un account di Servizi multimediali
> * Accedere all'API di Servizi multimediali
> * Configurare l'app di esempio
> * Esaminare il codice che esegue lo streaming live
> * Guardare l'evento con [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) in http://ampdemo.azureedge.net
> * Pulire le risorse

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione è necessario quanto segue.

* Installare Visual Studio Code o Visual Studio
* Una fotocamera o un dispositivo (ad esempio un laptop) usato per trasmettere un evento.
* Un codificatore live locale in grado di convertire i segnali provenienti dalla fotocamera in flussi inviati al servizio di streaming live Servizi multimediali. Il flusso deve essere in formato **RTMP** oppure **Smooth Streaming**.

## <a name="download-the-sample"></a>Scaricare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET di streaming usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

L'esempio di streaming live è disponibile nella cartella [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

> [!IMPORTANT]
> Questo esempio usa un suffisso univoco per ogni risorsa. Se si annulla il debug o si termina l'app senza eseguirla per intero, per l'account risulteranno disponibili più LiveEvent. <br/>
> Assicurarsi di arrestare i LiveEvent in esecuzione. In caso contrario, verranno **fatturati**.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Esaminare il codice che esegue lo streaming live

Questa sezione esamina le funzioni definite nel file [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) del progetto *MediaV3LiveApp*.

L'esempio crea un suffisso univoco per ogni risorsa in modo che non si verifichino conflitti di nomi se si esegue l'esempio più volte senza ripulire.

> [!IMPORTANT]
> Questo esempio usa un suffisso univoco per ogni risorsa. Se si annulla il debug o si termina l'app senza eseguirla per intero, per l'account risulteranno disponibili più LiveEvent. <br/>
> Assicurarsi di arrestare i LiveEvent in esecuzione. In caso contrario, verranno **fatturati**.
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale.  

```csharp
private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
{
    var credentials = await GetCredentialsAsync(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-a-live-event"></a>Creare un evento live

Questa sezione mostra come creare un LiveEvent di tipo **pass-through** (LiveEventEncodingType impostato su None). Se si vuole creare un LiveEvent abilitato per la codifica live, impostare LiveEventEncodingType su Basic. 

Altre caratteristiche che è possibile specificare durante la creazione dell'evento live sono:

* Account di Servizi multimediali 
* Protocollo di streaming per l'evento live (attualmente, sono supportati i protocolli RTMP e Smooth Streaming)
       
    Non è possibile modificare l'opzione relativa al protocollo durante l'esecuzione del LiveEvent o dei LiveOutput associati. Se sono necessari protocolli diversi, è necessario creare LiveEvent separati per ogni protocollo di streaming.  
* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP autorizzati a inserire video in questo LiveEvent. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').
    
    Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.

Quando si crea l'evento, è possibile impostarne l'avvio automatico. 

```csharp
Console.WriteLine($"Creating a live event named {liveEventName}");
Console.WriteLine();

LiveEventPreview liveEventPreview = new LiveEventPreview
{
    AccessControl = new LiveEventPreviewAccessControl(
        ip: new IPAccessControl(
            allow: new IPRange[]
            {
                new IPRange (
                    name: "AllowAll",
                    address: "0.0.0.0",
                    subnetPrefixLength: 0
                )
            }
        )
    )
};

// This can sometimes take awhile. Be patient.
LiveEvent liveEvent = new LiveEvent(
    location: mediaService.Location, 
    description:"Sample LiveEvent for testing",
    vanityUrl:false,
    encoding: new LiveEventEncoding(
                // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                encodingType:LiveEventEncodingType.None, 
                presetName:null
            ),
    input: new LiveEventInput(LiveEventInputProtocol.RTMP), 
    preview: liveEventPreview,
    streamOptions: new List<StreamOptionsFlag?>()
    {
        // Set this to Default or Low Latency.
        // Low latency reduces the amount of buffering Media Services does.
        // Low latency can also reduce the stability of the live stream. 
        StreamOptionsFlag.Default
    }
);

Console.WriteLine($"Creating the LiveEvent, be patient this can take time...");
liveEvent = client.LiveEvents.Create(config.ResourceGroup, config.AccountName, liveEventName, liveEvent, autoStart:true);
```

### <a name="get-ingest-urls"></a>Ottenere gli URL di inserimento

Dopo avere creato il canale, è possibile ottenere gli URL di inserimento da fornire al codificatore live. Questi URL vengono usati dal codificatore per inserire un flusso live.


```csharp
// Get the input endpoint to configure the on premise encoder with
string ingestUrl = liveEvent.Input.Endpoints.First().Url;
Console.WriteLine($"The ingest url to configure the on premise encoder with is:");
Console.WriteLine($"\t{ingestUrl}");
Console.WriteLine();
```

### <a name="get-the-preview-url"></a>Ottenere l'URL di anteprima

Usare previewEndpoint per visualizzare in anteprima e verificare che l'input dal codificatore venga effettivamente ricevuto.

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

```sharp
string previewEndpoint = liveEvent.Preview.Endpoints.First().Url;
Console.WriteLine($"The preview url is:");
Console.WriteLine($"\t{previewEndpoint}");
Console.WriteLine();

Console.WriteLine($"Open the live preview in your browser and use the Azure Media Player to monitor the preview playback:");
Console.WriteLine($"\thttps://ampdemo.azureedge.net/?url={previewEndpoint}");
Console.WriteLine();
```

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>Creare e gestire LiveEvent e LiveOutput

Dopo l'avvio del flusso nel LiveEvent, è possibile iniziare l'evento di streaming creando Asset, LiveOutput e StreamingLocator. In questo modo il flusso viene archiviato e reso disponibile agli utenti tramite StreamingEndpoint. 

#### <a name="create-an-asset"></a>Creare un asset

Creare un asset utilizzabile da LiveOutput.

```csharp
string assetName = "archiveAsset" + uniqueness;
Console.WriteLine($"Creating an asset named {assetName}");
Console.WriteLine();
Asset asset = client.Assets.CreateOrUpdate(config.ResourceGroup, config.AccountName, assetName, new Asset());
```

#### <a name="create-a-liveoutput"></a>Creare un LiveOutput

```csharp
string manifestName = "output";
string liveOutputName = "liveOutput" + uniqueness;
Console.WriteLine($"Creating a live output named {liveOutputName}");
Console.WriteLine();

LiveOutput liveOutput = new LiveOutput(assetName: asset.Name, manifestName: manifestName, archiveWindowLength: TimeSpan.FromMinutes(10));
liveOutput = client.LiveOutputs.Create(config.ResourceGroup, config.AccountName, liveEventName, liveOutputName, liveOutput);
```

#### <a name="create-a-streaminglocator"></a>Creare un StreamingLocator

> [!NOTE]
> Quando l'account di Servizi multimediali viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 


```csharp
StreamingLocator locator = new StreamingLocator(assetName: assetName, streamingPolicyName: PredefinedStreamingPolicy.ClearStreamingOnly);
locator = client.StreamingLocators.Create(config.ResourceGroup, config.AccountName, streamingLocatorName, locator);

// Get the default Streaming Endpoint on the account
StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(config.ResourceGroup, config.AccountName, "default");

// If it's not running, Start it. 
if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
{
    Console.WriteLine("Streaming Endpoint was Stopped, restarting now..");
    client.StreamingEndpoints.Start(config.ResourceGroup, config.AccountName, "default");
}

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Pulizia delle risorse nell'account di Servizi multimediali

Se al termine dello streaming degli eventi si vuole eliminare le risorse di cui in precedenza è stato effettuato il provisioning, attenersi alla procedura seguente.

* Interrompere il push del flusso dal codificatore.
* Arrestare il LiveEvent. Dopo l'arresto, il LiveEvent non è soggetto ad alcun addebito. Quando occorrerà riavviarlo, avrà lo stesso URL di inserimento, per cui non sarà necessario riconfigurare il codificatore.
* È possibile arrestare StreamingEndpoint, a meno che non si voglia continuare a fornire l'archivio dell'evento live come flusso su richiesta. Se LiveEvent è arrestato, non è soggetto ad alcun addebito.

```csharp
private static void CleanupLiveEventAndOutput(IAzureMediaServicesClient client, string resourceGroup, string accountName, string liveEventName, string liveOutputName)
{
    // Delete the LiveOutput
    client.LiveOutputs.Delete(resourceGroup, accountName, liveEventName, liveOutputName);

    // Stop and delete the LiveEvent
    client.LiveEvents.Stop(resourceGroup, accountName, liveEventName);
    client.LiveEvents.Delete(resourceGroup, accountName, liveEventName);
}

private static void CleanupLocatorAssetAndStreamingEndpoint(IAzureMediaServicesClient client, string resourceGroup, string accountName, string streamingLocatorName, string assetName)
{
    // Delete the Streaming Locator
    client.StreamingLocators.Delete(resourceGroup, accountName, streamingLocatorName);

    // Delete the Archive Asset
    client.Assets.Delete(resourceGroup, accountName, assetName);
}

private static void CleanupAccount(IAzureMediaServicesClient client, string resourceGroup, string accountName)
{
    try{
        Console.WriteLine("Cleaning up the resources used, stopping the LiveEvent. This can take a few minutes to complete.");
        Console.WriteLine();

        var events = client.LiveEvents.List(resourceGroup, accountName);
        
        foreach (LiveEvent l in events)
        {
            if (l.Name == liveEventName){
                var outputs = client.LiveOutputs.List(resourceGroup, accountName, l.Name);

                foreach (LiveOutput o in outputs)
                {
                    client.LiveOutputs.Delete(resourceGroup, accountName, l.Name, o.Name);
                    Console.WriteLine($"LiveOutput: {o.Name} deleted from LiveEvent {l.Name}. The archived Asset and Streaming URLs are still retained for on-demand viewing.");
                }

                if (l.ResourceState == LiveEventResourceState.Running){
                    client.LiveEvents.Stop(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Stopped.");
                    client.LiveEvents.Delete(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Deleted.");
                    Console.WriteLine();
                }
            }
        }
    } 
    catch(ApiErrorException e)
    {
        Console.WriteLine("Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();

    }
}
```        

## <a name="watch-the-event"></a>Guardare l'evento

Per controllare l'evento, copiare l'URL di streaming ottenuto durante l'esecuzione del codice descritto in [Creare un StreamingLocator](#create-a-streaminglocator) e usare un lettore di propria scelta. È possibile usare [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) per testare il flusso all'indirizzo http://ampdemo.azureedge.net. 

Quando viene arrestato, l'evento live converte automaticamente gli eventi in contenuto su richiesta. Anche dopo l'arresto e l'eliminazione dell'evento, gli utenti saranno in grado di riprodurre in streaming il contenuto archiviato sotto forma di video on demand, finché non si elimina l'asset. Un asset non può essere eliminato se è usato da un evento. Per farlo, eliminare prima l'evento. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza. A tal fine è possibile usare lo strumento **CloudShell**.

In **CloudShell** eseguire il comando seguente:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Lasciare LiveEvent in esecuzione comporta costi di fatturazione. Tenere presente, in caso di arresto anomalo o chiusura del programma/progetto, che LiveEvent potrebbe rimanere in esecuzione in stato di fatturazione.

## <a name="next-steps"></a>Passaggi successivi

[Streaming di file](stream-files-tutorial-with-api.md)

