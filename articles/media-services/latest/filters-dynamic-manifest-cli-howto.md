---
title: Uso dell'interfaccia della riga di comando per la creazione di filtri con Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra come usare l'interfaccia della riga di comando per creare filtri con servizi multimediali di Azure V3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 74516aa921e45917f327a193a1c972b021c9c8ff
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896064"
---
# <a name="creating-filters-with-cli"></a>Creazione di filtri con l'interfaccia della riga di comando 

Quando si distribuiscono contenuti ai clienti (flusso di eventi dal vivo o di video on demand), il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. 

Per una descrizione dettagliata di questa funzionalità e degli scenari in cui viene usata, vedere [manifesti](filters-dynamic-manifest-overview.md) e [filtri](filters-concept.md)dinamici.

Questo argomento illustra come configurare un filtro per un asset Video on demand e usare l'interfaccia della riga di comando per Servizi multimediali v3 per creare [filtri account](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [filtri asset](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Assicurarsi di esaminare [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Prerequisiti 

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definire un filtro 

Di seguito è riportato l'esempio che definisce le condizioni di selezione di traccia che vengono aggiunte al manifesto finale. Questo filtro include tracce audio EC-3 e tracce video con velocità in bit nell'intervallo 0-1000000.

> [!TIP]
> Se si prevede di definire i **filtri** in REST, si noti che è necessario includere l'oggetto JSON wrapper "Properties".  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Crea filtri account

Il comando [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) seguente crea un filtro account con le selezioni di traccia del filtro [definite in precedenza](#define-a-filter). 

Questo comando consente di superare il parametro `--tracks` facoltativo che include JSON per le selezioni di traccia.  Usare @{file} per caricare JSON da un file. Se si usa l’interfaccia della riga di comando di Azure in locale, specificare il percorso completo del file:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Creare filtri asset

Il comando [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) seguente crea un filtro asset con le selezioni di traccia del filtro [definite in precedenza](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Associare filtri a un localizzatore di streaming

È possibile specificare un elenco di filtri asset o account applicabili al localizzatore di streaming. Il [Packager dinamico (endpoint di streaming)](dynamic-packaging-overview.md) applica questo elenco di filtri insieme a quelli specificati dal client nell'URL. Questa combinazione genera un [manifesto dinamico](filters-dynamic-manifest-overview.md), basato sui filtri presenti nell'URL e nei filtri specificati nel localizzatore di streaming. Si consiglia di utilizzare questa funzionalità se si desidera applicare filtri, ma non si desidera esporre i nomi dei filtri nell'URL.

Il codice CLI seguente mostra come creare un localizzatore di streaming e specificare `filters`. Si tratta di una proprietà facoltativa che accetta un elenco separato da spazi di nomi di filtri di asset e/o nomi di filtri account.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Flusso con filtri

Dopo aver definito i filtri, è possibile usarli nell'URL di streaming. È possibile applicare filtri ai protocolli di streaming a bitrate adattivo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocol|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Passaggio successivo

[Streaming di video](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Vedi anche

[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
