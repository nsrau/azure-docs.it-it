---
title: Creazione di filtri con l'API REST di servizi multimediali di Azure V3
description: Questo argomento descrive come creare filtri che il client può usare per trasmettere in streaming sezioni specifiche di un flusso. L'API REST di servizi multimediali V3 crea manifesti dinamici per ottenere questo flusso selettivo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c1d7bf933b487c40d571f1912341b5ef771e4e67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527327"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Creazione di filtri con l'API REST di Servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Quando si distribuiscono contenuti ai clienti (flusso di eventi dal vivo o di video on demand) il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. 

Per una descrizione dettagliata di questa funzionalità e degli scenari in cui viene usata, vedere [manifesti](filters-dynamic-manifest-overview.md) e [filtri](filters-concept.md)dinamici.

Questo argomento illustra come definire un filtro per un asset Video on demand e usare le API REST per creare [Filtri account](/rest/api/media/accountfilters) e [Filtri asset](/rest/api/media/assetfilters). 

> [!NOTE]
> Assicurarsi di esaminare [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Prerequisiti 

Per completare i passaggi descritti in questo argomento, è necessario:

- Leggere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md).

    Assicurarsi di seguire l'ultimo passaggio nell'argomento [Ottenere Token in Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definire un filtro  

Di seguito è riportato l'esempio del **Corpo della richiesta** che definisce le condizioni di selezione brano che vengono aggiunte al manifesto. Questo filtro include tracce audio EC-3 e tracce video con velocità in bit nell'intervallo 0-1000000.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Creare filtri account

Nella raccolta dei post che è stata scaricata selezionare **filtri account** -> **Crea o aggiorna un filtro account**.

Il metodo di richiesta HTTP **PUT** è simile a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selezionare la scheda **Corpo** e incollare il codice json [definito in precedenza](#define-a-filter).

Selezionare **Send** (Invia). 

Il filtro è stato creato.

Per altre informazioni, vedere [Creare o aggiornare](/rest/api/media/accountfilters/createorupdate). Vedere anche [Esempi JSON per i filtri](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Creare filtri asset  

Nella raccolta di post di "Media Services V3" scaricata selezionare **Asset** -> **Crea o Aggiorna filtro asset**.

Il metodo di richiesta HTTP **PUT** è simile a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selezionare la scheda **Corpo** e incollare il codice json [definito in precedenza](#define-a-filter).

Selezionare **Send** (Invia). 

Il filtro asset è stato creato.

Per informazioni dettagliate su come creare o aggiornare i filtri asset, vedere [Crea o aggiorna](/rest/api/media/assetfilters/createorupdate). Vedere anche [Esempi JSON per i filtri](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associare filtri a un localizzatore di streaming

È possibile specificare un elenco di filtri asset o account applicabili al localizzatore di streaming. Il [Packager dinamico (endpoint di streaming)](dynamic-packaging-overview.md) applica questo elenco di filtri insieme a quelli specificati dal client nell'URL. Questa combinazione genera un [manifesto dinamico](filters-dynamic-manifest-overview.md), basato sui filtri presenti nell'URL e nei filtri specificati nel localizzatore di streaming. Si consiglia di utilizzare questa funzionalità se si desidera applicare filtri, ma non si desidera esporre i nomi dei filtri nell'URL.

Per creare e associare filtri a un localizzatore di streaming usando REST, usare i [localizzatori di streaming-create](/rest/api/media/streaminglocators/create) API e specificare `properties.filters` nel [corpo della richiesta](/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Flusso con filtri

Dopo aver definito i filtri, è possibile usarli nell'URL di streaming. È possibile applicare filtri ai protocolli di streaming a bitrate adattivo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocollo|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passaggi successivi

[Video in streaming](stream-files-tutorial-with-rest.md) 
