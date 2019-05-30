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
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392917"
---
# <a name="streaming-policies"></a>Criteri di streaming

In Servizi multimediali di Azure v3, i [criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) consentono di definire i protocolli di streaming e le opzioni di crittografia per [StreamingLocators](streaming-locators-concept.md). Servizi multimediali v3 offre che alcuni predefiniti i criteri di Streaming in modo che è possibile usarli direttamente per la versione di valutazione o di produzione. 

Attualmente disponibili Streaming criteri predefiniti:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

Il seguente albero"Decision" consente di scegliere un criterio per lo scenario di Streaming predefinito.

> [!IMPORTANT]
> * Le proprietà dei **criteri di streaming** di tipo Datetime sono sempre in formato UTC.
> * È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming tutte le volte che si devono usare le stesse opzioni. Per altre informazioni, vedere [Quote e limitazioni](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Albero delle decisioni

Fare clic sull'immagine per visualizzarla a schermo intero.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Se crittografare i contenuti, è necessario creare un [Content Key Policy](content-key-policy-concept.md), il **Content Key Policy** non è necessaria per streaming o il download non crittografato. 

Se si hanno requisiti speciali (ad esempio, se si desidera specificare protocolli diversi, è necessario utilizzare un servizio di distribuzione delle chiavi personalizzato o necessario usare una traccia audio), è possibile [creare](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) un criterio personalizzato di Streaming. 

## <a name="get-a-streaming-policy-definition"></a>Ottenere una definizione di criteri di Streaming  

Se si desidera visualizzare la definizione di un criterio di Streaming, usare [ottenere](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) e specificare il nome del criterio. Ad esempio: 

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
* [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM](protect-with-drm.md)
