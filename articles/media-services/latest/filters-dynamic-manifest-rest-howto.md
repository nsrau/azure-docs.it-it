---
title: Creazione di filtri con l'API REST di Servizi multimediali di Azure | Microsoft Docs
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
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 69399513291a47f7109003e825052314f447125a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002332"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Creazione di filtri con l'API REST di Servizi multimediali

Quando si distribuiscono contenuti ai clienti (flusso di Eventi dal vivo o Video on demand) il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. Per altre informazioni, vedere [filtri](filters-concept.md) e [manifesti dinamici](filters-dynamic-manifest-overview.md).

Questo argomento illustra come definire un filtro per un asset Video on demand e usare le API REST per creare [Filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) e [Filtri asset](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Assicurarsi di esaminare [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Prerequisiti 

Per completare i passaggi descritti in questo argomento, è necessario:

- Leggere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md).

    Assicurarsi di seguire l'ultimo passaggio nell'argomento [Ottieni Token in Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

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

Nella raccolta di Postman che è stata scaricata, selezionare **Filtri account**->**Crea o aggiorna un filtro account**.

Il metodo di richiesta HTTP **PUT** è simile a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selezionare la scheda **Corpo** e incollare il codice json [definito in precedenza](#define-a-filter).

Selezionare **Send** (Invia). 

Il filtro è stato creato.

Per altre informazioni, vedere [Creare o aggiornare](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Creare filtri asset  

Nella raccolta Postman "Servizi multimediali v3" scaricata, selezionare **Asset**->**Crea o aggiorna filtro asset**.

Il metodo di richiesta HTTP **PUT** è simile a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selezionare la scheda **Corpo** e incollare il codice json [definito in precedenza](#define-a-filter).

Selezionare **Send** (Invia). 

Il filtro asset è stato creato.

Per informazioni dettagliate su come creare o aggiornare i filtri asset, vedere [Crea o aggiorna](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Passaggi successivi

[Video in streaming](stream-files-tutorial-with-rest.md) 
