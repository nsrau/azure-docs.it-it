---
title: Creazione di filtri con il .NET SDK di Servizi multimediali di Azure
description: Questo argomento descrive come creare filtri che il client può usare per trasmettere in streaming sezioni specifiche di un flusso. Servizi multimediali crea manifesti dinamici per consentire questo streaming selettivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 0d85bad5328c9d28c8690b389fca18731ea17644
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002369"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Creare filtri con il .NET SDK di Servizi multimediali

Quando si distribuiscono contenuti ai clienti (flusso di eventi dal vivo o di video on demand) il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. Per altre informazioni, vedere [filtri](filters-concept.md) e [manifesti dinamici](filters-dynamic-manifest-overview.md).

Questo argomento illustra come usare il .NET SDK di Servizi multimediali per definire un filtro per un asset Video on demand e creare [Filtri account](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) e [Filtri asset](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Assicurarsi di esaminare [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Prerequisiti 

- Leggere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 
- Ottenere le informazioni necessarie per [accedere alle API](access-api-cli-how-to.md)
- Leggere [Eseguire il caricamento, la codifica e lo streaming con Servizi multimediali di Azure](stream-files-tutorial-with-api.md) per informazioni su come [iniziare a usare il .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Definire un filtro  

In .NET, configurare le selezioni di traccia con le classi [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) e [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet). 

Il codice seguente consente di definire un filtro che include tutti i brani audio EC-3 e tutte le tracce video con velocità in bit compresa nell’intervallo 0-1000000.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Creare filtri account

Il seguente codice spiega come usare .NET per creare un filtro account che includa tutte le selezioni di traccia [definite in precedenza](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Creare filtri asset

Il seguente codice spiega come usare .NET per creare un filtro asset che includa tutte le selezioni di traccia [definite in precedenza](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Associare i filtri localizzatore di Streaming

È possibile specificare un elenco di filtri di asset o account, si applica anche per il localizzatore di Streaming. Il [creazione dinamica dei pacchetti (Endpoint di Streaming)](dynamic-packaging-overview.md) si applica questo elenco di filtri insieme a quelli del client specifica l'URL. Questa combinazione genera una [manifesto dinamico](filters-dynamic-manifest-overview.md), basata su filtri nell'URL + filtri è specificare nel localizzatore di Streaming. È consigliabile usare questa funzionalità se si desidera applicare i filtri, ma non si desidera esporre i nomi dei filtri nell'URL.

Il seguente C# codice viene illustrato come creare un localizzatore di Streaming e specificare `StreamingLocator.Filters`. Si tratta di una proprietà facoltativa che accetta un `IList<string>` dei nomi di filtro.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Utilizzo dei filtri di Stream

Dopo aver definito i filtri, è possibile usarli nell'URL di streaming. È possibile applicare filtri ai protocolli di streaming a bitrate adattivo: Apple HLS (HTTP Live Streaming), MPEG-DASH e Smooth Streaming.

Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocol|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passaggi successivi

[Video in streaming](stream-files-tutorial-with-api.md) 


