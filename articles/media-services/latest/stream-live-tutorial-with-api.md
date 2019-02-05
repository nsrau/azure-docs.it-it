---
title: Streaming live con Servizi multimediali di Azure v3 usando .NET | Microsoft Docs
description: Questa esercitazione illustra i passaggi per lo streaming live con Servizi multimediali v3 usando .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 49598eb8579e20dd20ca63d11529ba106a510102
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170522"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-net"></a>Esercitazione: Eseguire lo streaming live con Servizi multimediali v3 usando .NET

In Servizi multimediali di Azure le entità [Eventi live](https://docs.microsoft.com/rest/api/media/liveevents) sono responsabili dell'elaborazione dei contenuti in streaming live. Un'entità evento live fornisce un endpoint di input (URL di inserimento) che può essere a sua volta fornito al codificatore live. L'entità evento live riceve flussi di input live dal codificatore live e li rende disponibili per lo streaming con uno o più [Endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints). Gli eventi live forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito. Questa esercitazione illustra come usare .NET Core per creare un evento live di tipo **pass-through**. 

> [!NOTE]
> Assicurarsi di leggere [Live streaming with Azure Media Services v3](live-streaming-overview.md) (Streaming live con Servizi multimediali di Azure v3) prima di procedere. 

L'esercitazione illustra come:    

> [!div class="checklist"]
> * Scaricare l'app di esempio descritta nell'argomento
> * Esaminare il codice che esegue lo streaming live
> * Guardare l'evento con [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) in http://ampdemo.azureedge.net
> * Pulire le risorse

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue.

- Installare Visual Studio Code o Visual Studio.
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).<br/>Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.
- Seguire la procedura descritta in [Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure](access-api-cli-how-to.md) e salvare le credenziali. Sarà necessario usarle per accedere all'API.
- Una fotocamera o un dispositivo (ad esempio un laptop) usato per trasmettere un evento.
- Un codificatore live locale in grado di convertire i segnali provenienti dalla fotocamera in flussi inviati al servizio di streaming live Servizi multimediali. Il flusso deve essere in formato **RTMP** oppure **Smooth Streaming**.

## <a name="download-and-configure-the-sample"></a>Scaricare e configurare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET di streaming usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

L'esempio di streaming live è disponibile nella cartella [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Aprire [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) nel progetto scaricato. Sostituire i valori con le credenziali ottenute dall'[accesso alle API](access-api-cli-how-to.md).

> [!IMPORTANT]
> Questo esempio usa un suffisso univoco per ogni risorsa. Se si annulla il debug o si termina l'app senza eseguirla per intero, per l'account risulteranno disponibili più eventi live. <br/>Assicurarsi di arrestare gli eventi live in esecuzione. In caso contrario, verranno **fatturati**.

## <a name="examine-the-code-that-performs-live-streaming"></a>Esaminare il codice che esegue lo streaming live

Questa sezione esamina le funzioni definite nel file [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) del progetto *MediaV3LiveApp*.

L'esempio crea un suffisso univoco per ogni risorsa in modo che non si verifichino conflitti di nomi se si esegue l'esempio più volte senza ripulire.

> [!IMPORTANT]
> Questo esempio usa un suffisso univoco per ogni risorsa. Se si annulla il debug o si termina l'app senza eseguirla per intero, per l'account risulteranno disponibili più eventi live. <br/>
> Assicurarsi di arrestare gli eventi live in esecuzione. In caso contrario, verranno **fatturati**.
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Creare un evento live

Questa sezione mostra come creare un evento live di tipo **pass-through** (LiveEventEncodingType impostato su None). Se si vuole creare un evento live abilitato per la codifica live, impostare LiveEventEncodingType su **Standard**. 

Altre caratteristiche che è possibile specificare durante la creazione dell'evento live sono:

* Account di Servizi multimediali 
* Protocollo di streaming per l'evento live (attualmente, sono supportati i protocolli RTMP e Smooth Streaming).<br/>Non è possibile modificare l'opzione relativa al protocollo durante l'esecuzione dell'evento live o degli output live associati. Se sono necessari protocolli diversi, è necessario creare eventi live separati per ogni protocollo di streaming.  
* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP autorizzati a inserire video in questo evento live. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').<br/>Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.<br/>Gli indirizzi IP devono essere in uno dei formati seguenti: Indirizzo IpV4 con 4 numeri, intervallo di indirizzi CIDR.
* Quando si crea l'evento, è possibile impostarne l'avvio automatico. <br/>Quando l'avvio automatico è impostato su true, l'evento live verrà avviato dopo la creazione. Ciò significa che la fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. Per altre informazioni, vedere la sezione [Stati e fatturazione dell'evento live](live-event-states-billing.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Ottenere gli URL di inserimento

Al termine della creazione dell'evento live, è possibile ottenere gli URL di inserimento che verranno passati al codificatore live. Questi URL vengono usati dal codificatore per inserire un flusso live.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Ottenere l'URL di anteprima

Usare previewEndpoint per visualizzare in anteprima e verificare che l'input dal codificatore venga effettivamente ricevuto.

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Creare e gestire eventi live e output live

Dopo l'avvio del flusso nell'evento live, è possibile iniziare l'evento di streaming creando un asset, un output live e un localizzatore di streaming. In questo modo il flusso viene archiviato e reso disponibile agli utenti tramite l'endpoint di streaming. 

#### <a name="create-an-asset"></a>Creare un asset

Creare un asset utilizzabile dall'output live.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Creare un output live

Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. Quando si elimina l'output live, non si elimina l'asset sottostante e il contenuto dell'asset.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Creare un localizzatore di streaming

> [!NOTE]
> Quando l'account di Servizi multimediali viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 

Quando l'asset output live è stato pubblicato usando un localizzatore di streaming, l'evento live (fino alla lunghezza dell'intervallo DVR) continuerà a essere visualizzabile fino alla scadenza o all'eliminazione del localizzatore di streaming, a seconda del valore raggiunto per primo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

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
* Arrestare l'evento live. Dopo l'arresto, l'evento live non è soggetto ad alcun addebito. Quando occorrerà riavviarlo, avrà lo stesso URL di inserimento, per cui non sarà necessario riconfigurare il codificatore.
* È possibile arrestare l'endpoint di streaming, a meno che non si voglia continuare a fornire l'archivio dell'evento live come flusso su richiesta. Se l'evento live è stato arrestato, non è soggetto ad alcun addebito.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

Il codice seguente mostra come pulire l'account da tutti gli eventi live:

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Guardare l'evento

Per controllare l'evento, copiare l'URL di streaming ottenuto durante l'esecuzione del codice descritto in [Creare un localizzatore di streaming](#create-a-streaminglocator) e usare un lettore di propria scelta. È possibile usare [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) per testare il flusso all'indirizzo http://ampdemo.azureedge.net. 

Quando viene arrestato, l'evento live converte automaticamente gli eventi in contenuto su richiesta. Anche dopo l'arresto e l'eliminazione dell'evento, gli utenti saranno in grado di riprodurre in streaming il contenuto archiviato sotto forma di video on demand, finché non si elimina l'asset. Un asset non può essere eliminato se è usato da un evento. Per farlo, eliminare prima l'evento. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza.

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Lasciare l'evento live in esecuzione comporta costi di fatturazione. Tenere presente, in caso di arresto anomalo o chiusura del programma/progetto, che l'evento live potrebbe rimanere in esecuzione in stato di fatturazione.

## <a name="next-steps"></a>Passaggi successivi

[Streaming di file](stream-files-tutorial-with-api.md)
 
