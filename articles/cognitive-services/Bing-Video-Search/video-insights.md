---
title: Ottenere informazioni dettagliate sui video - Ricerca video Bing
titlesuffix: Azure Cognitive Services
description: Spiega come usare l'API Ricerca video Bing per ottenere altre informazioni su un video.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: af6056507cb70ab33bee5955ddf7bd0f77fd04eb
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219992"
---
# <a name="get-insights-about-a-video"></a>Ottenere informazioni dettagliate su un video

Ogni video include un ID video che è possibile usare per ottenere altre informazioni sul video, ad esempio i video correlati.  
  
Per ottenere informazioni dettagliate su un video, acquisire il relativo token [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) nella risposta. 

```json
    "value" : [
        {
            . . .
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear...",
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHBZ--g",
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63",
            . . .
        }
    ],
```

Successivamente, inviare la richiesta GET seguente all'endpoint dei dettagli video. Impostare il parametro di query [id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#id) sul token `videoId`. Per specificare le informazioni dettagliate che si vuole ottenere, impostare il parametro di query [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested). Per ottenere tutte le informazioni dettagliate, impostare `modules` su All. La risposta include tutte le informazioni dettagliate richieste, se disponibili.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 

## <a name="getting-related-videos-insights"></a>Ottenere informazioni dettagliate sui video correlati  

Per ottenere video correlati al video specificato, impostare il parametro di query [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested) su RelatedVideos.
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=RelatedVideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
  
Di seguito è indicata la risposta alla richiesta precedente. L'oggetto di massimo livello è un oggetto [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails) anziché un oggetto [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos).  
  
```  
{
    "_type" : "Api.VideoDetails.VideoDetails",
    "relatedVideos" : {
        "value" : [
            {
                "name" : "How to sail - Reefing a Sail",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OVP.zt...",
                "datePublished" : "2014-03-04T16:11:09",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?...",
                "duration" : "PT4M56S",
                "motionThumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OM...",
                "allowHttpsEmbed" : true,
                "viewCount" : 21756,
                "videoId" : "AC1A157A4DDB571D03D6AC157A4DDB571D03D6",
                "allowMobileEmbed" : false,
                "isSuperfresh" : false
            },
            . . .
        ]
    }
}
```