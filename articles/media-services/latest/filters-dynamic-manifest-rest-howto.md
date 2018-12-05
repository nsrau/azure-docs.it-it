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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 6b0ef646ba9ea535038f181ebfff5bf7639afdf8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633623"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Creazione di filtri con l'API REST di Servizi multimediali

Quando si distribuiscono contenuti ai clienti (flusso di Eventi dal vivo o Video on demand) il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. Per altre informazioni, vedere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).

Questo argomento illustra come definire un filtro per un asset Video on demand e usare le API REST per creare [Filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) e [Filtri asset](https://docs.microsoft.com/rest/api/media/assetfilters). 

## <a name="prerequisites"></a>Prerequisiti 

Per completare i passaggi descritti in questo argomento, è necessario:

- Rivedere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md)/

## <a name="define-a-filter"></a>Definire un filtro  

Di seguito è riportato l'esempio del **Corpo della richiesta** che definisce le condizioni di selezione brano che vengono aggiunte al manifesto. Questo filtro include i brani audio in lingua inglese con EC-3 e i brani video con velocità in bit nell’intervallo 0-1000000.

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
                        "property": "Language",
                        "value": "en",
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
     }
}
```

## <a name="create-account-filters"></a>Crea filtri account

Nella raccolta di Postman che è stata scaricata, selezionare **Filtri account**->**Crea o aggiorna un filtro account**.

Il metodo di richiesta HTTP **PUT** è simile a:

PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01

Selezionare la scheda **Corpo** e incollare il codice json [definito in precedenza](#define-a-filter).

Selezionare **Send** (Invia). 

Il filtro è stato creato.

Per altre informazioni, vedere [Creare o aggiornare](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Creare filtri asset  

Nella raccolta Postman "Servizi multimediali v3" scaricata, selezionare **Asset**->**Crea o aggiorna filtro asset.

Il metodo di richiesta HTTP **PUT** è simile a:

PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01

Selezionare la scheda **Corpo** e incollare il codice json [definito in precedenza](#define-a-filter).

Selezionare **Send** (Invia). 

Il filtro asset è stato creato.

Per informazioni dettagliate su come creare o aggiornare i filtri asset, vedere [Crea o aggiorna](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Passaggi successivi

[Video in streaming](stream-files-tutorial-with-rest.md) 
