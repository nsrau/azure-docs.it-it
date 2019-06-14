---
title: Uso dell'interfaccia della riga di comando per la creazione di filtri con Servizi multimediali di Azure | Microsoft Docs
description: In questo argomento illustra come usare l'interfaccia della riga di comando per creare filtri con Servizi multimediali.
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
ms.openlocfilehash: c6007b66e31996db5c6b043219470968a7b05031
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67114678"
---
# <a name="creating-filters-with-cli"></a>Creazione di filtri con l'interfaccia della riga di comando 

Quando si distribuiscono contenuti ai clienti (flusso di eventi dal vivo o di video on demand), il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. 

Per una descrizione dettagliata di questa funzionalità e scenari in cui viene usato, vedere [manifesti dinamici](filters-dynamic-manifest-overview.md) e [filtri](filters-concept.md).

Questo argomento illustra come configurare un filtro per un asset Video on demand e usare l'interfaccia della riga di comando per Servizi multimediali v3 per creare [filtri account](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [filtri asset](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Assicurarsi di esaminare [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Prerequisiti 

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definire un filtro 

Di seguito è riportato l'esempio che definisce le condizioni di selezione di traccia che vengono aggiunte al manifesto finale. Questo filtro include tracce audio EC-3 e tracce video con velocità in bit nell'intervallo 0-1000000.

> [!TIP]
> Se si intende definire **filtri** in REST, si noti che è necessario includere l'oggetto JSON wrapper "Properties".  

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

## <a name="create-account-filters"></a>Creare filtri account

Il comando [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) seguente crea un filtro account con le selezioni di traccia del filtro [definite in precedenza](#define-a-filter). 

Questo comando consente di superare il parametro `--tracks` facoltativo che include JSON per le selezioni di traccia.  Usare @{file} per caricare JSON da un file. Se si usa l’interfaccia della riga di comando di Azure in locale, specificare il percorso completo del file:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Creare filtri asset

Il comando [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) seguente crea un filtro asset con le selezioni di traccia del filtro [definite in precedenza](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="associate-filters-with-streaming-locator"></a>Associare i filtri localizzatore di Streaming

È possibile specificare un elenco di filtri di asset o account, si applica anche per il localizzatore di Streaming. Il [creazione dinamica dei pacchetti (Endpoint di Streaming)](dynamic-packaging-overview.md) si applica questo elenco di filtri insieme a quelli del client specifica l'URL. Questa combinazione genera una [manifesto dinamico](filters-dynamic-manifest-overview.md), basata su filtri nell'URL + filtri è specificare nel localizzatore di Streaming. È consigliabile usare questa funzionalità se si desidera applicare i filtri, ma non si desidera esporre i nomi dei filtri nell'URL.

Il codice dell'interfaccia della riga seguente viene illustrato come creare un localizzatore di Streaming e specificare `filters`. Si tratta di una proprietà facoltativa che accetta un elenco delimitato da spazi dei nomi dei filtri di asset e/o i nomi di account filtro.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Utilizzo dei filtri di Stream

Dopo aver definito i filtri, è possibile usarli nell'URL di streaming. È possibile applicare filtri ai protocolli di streaming a bitrate adattivo: Apple HLS (HTTP Live Streaming), MPEG-DASH e Smooth Streaming.

Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocol|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Passaggio successivo

[Video in streaming](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Vedere anche

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
