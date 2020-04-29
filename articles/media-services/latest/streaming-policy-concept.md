---
title: Criteri di streaming in Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra le caratteristiche dei criteri di streaming e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: 9c80056fd62173ff1e5a6ed3979adba71b7706cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582767"
---
# <a name="streaming-policies"></a>Criteri di streaming

In Servizi multimediali di Azure v3, i [criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) consentono di definire i protocolli di streaming e le opzioni di crittografia per [StreamingLocators](streaming-locators-concept.md). Media Services V3 fornisce alcuni criteri di streaming predefiniti che consentono di usarli direttamente per la valutazione o la produzione. 

Criteri di streaming predefiniti attualmente disponibili:<br/>
* ' Predefined_DownloadOnly '
* ' Predefined_ClearStreamingOnly '
* ' Predefined_DownloadAndClearStreaming '
* ' Predefined_ClearKey '
* ' Predefined_MultiDrmCencStreaming ' 
* ' Predefined_MultiDrmStreaming '

Il seguente "albero delle decisioni" consente di scegliere un criterio di streaming predefinito per lo scenario.

> [!IMPORTANT]
> * Le proprietà dei **criteri di streaming** di tipo Datetime sono sempre in formato UTC.
> * È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming tutte le volte che si devono usare le stesse opzioni. Per altre informazioni, vedere [quote e limiti](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Albero delle decisioni

Fare clic sull'immagine per visualizzarla a schermo intero.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Se si crittografa il contenuto, è necessario creare un [criterio della chiave](content-key-policy-concept.md)simmetrica. i **criteri della chiave** simmetrica non sono necessari per cancellare lo streaming o il download. 

Se si dispone di requisiti speciali (ad esempio, se si desidera specificare protocolli diversi, è necessario utilizzare un servizio di distribuzione delle chiavi personalizzato oppure è necessario utilizzare una traccia audio non crittografata), è possibile [creare](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) un criterio di flusso personalizzato. 

## <a name="get-a-streaming-policy-definition"></a>Ottenere una definizione di criteri di flusso  

Se si vuole visualizzare la definizione di un criterio di flusso, usare [Get](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) e specificare il nome del criterio. Ad esempio:

### <a name="rest"></a>REST

Richiesta:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Risposta:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
* [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi](protect-with-aes128.md)
* [Usare la crittografia dinamica DRM e il servizio di distribuzione delle licenze](protect-with-drm.md)
