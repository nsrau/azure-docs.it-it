---
title: Creazione di filtri con servizi multimediali di Azure V3 .NET SDK
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
ms.date: 08/11/2020
ms.author: juliako
ms.openlocfilehash: 0d8a60d6c4b3fb66b46742fbd99e3a73a63fff45
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136083"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Creare filtri con il .NET SDK di Servizi multimediali

Quando si distribuiscono contenuti ai clienti (flusso di eventi dal vivo o di video on demand) il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. 

Per una descrizione dettagliata di questa funzionalità e degli scenari in cui viene usata, vedere [manifesti](filters-dynamic-manifest-overview.md) e [filtri](filters-concept.md)dinamici.

Questo argomento illustra come usare il .NET SDK di Servizi multimediali per definire un filtro per un asset Video on demand e creare [Filtri account](/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) e [Filtri asset](/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Assicurarsi di esaminare [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Prerequisiti 

- Leggere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 
- Ottenere le informazioni necessarie per [accedere alle API](./access-api-howto.md)
- Leggere [Eseguire il caricamento, la codifica e lo streaming con Servizi multimediali di Azure](stream-files-tutorial-with-api.md) per informazioni su come [iniziare a usare il .NET SDK](stream-files-tutorial-with-api.md#start-using-media-services-apis-with-net-sdk)

## <a name="define-a-filter"></a>Definire un filtro  

In .NET, configurare le selezioni di traccia con le classi [FilterTrackSelection](/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) e [FilterTrackPropertyCondition](/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet). 

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

## <a name="associate-filters-with-streaming-locator"></a>Associare filtri a un localizzatore di streaming

È possibile specificare un elenco di filtri asset o account applicabili al localizzatore di streaming. Il [Packager dinamico (endpoint di streaming)](dynamic-packaging-overview.md) applica questo elenco di filtri insieme a quelli specificati dal client nell'URL. Questa combinazione genera un [manifesto dinamico](filters-dynamic-manifest-overview.md), basato sui filtri presenti nell'URL e nei filtri specificati nel localizzatore di streaming. Si consiglia di utilizzare questa funzionalità se si desidera applicare filtri, ma non si desidera esporre i nomi dei filtri nell'URL.

Il codice C# seguente illustra come creare un localizzatore di streaming e specificare `StreamingLocator.Filters` . Si tratta di una proprietà facoltativa che accetta `IList<string>` i nomi dei filtri.

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
      
## <a name="stream-using-filters"></a>Flusso con filtri

Dopo aver definito i filtri, è possibile usarli nell'URL di streaming. È possibile applicare filtri ai protocolli di streaming a bitrate adattivo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocollo|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passaggi successivi

[Video in streaming](stream-files-tutorial-with-api.md) 
