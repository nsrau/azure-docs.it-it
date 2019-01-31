---
title: 'Ricerca sul Web per video di tendenza: Ricerca video Bing'
titlesuffix: Azure Cognitive Services
description: Illustra come usare l'API Ricerca video Bing per cercare video di tendenza nel Web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 71dd888199e9e630835c4916d35f6308431bab62
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203556"
---
# <a name="get-trending-videos"></a>Ottenere video di tendenza  

Per ottenere i video di tendenza più recenti, inviare la richiesta GET seguente:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

I video di tendenza sono supportati dai mercati seguenti.  
 
-   en-AU (inglese, Australia)  
-   en-CA (inglese, Canada)  
-   en-GB (inglese, Gran Bretagna)  
-   en-ID (inglese, Indonesia)  
-   en-IE (inglese, Irlanda)  
-   en-IN (inglese, India)  
-   en-NZ (inglese, Nuova Zelanda)  
-   en-PH (inglese, Filippine)  
-   en-SG (inglese, Singapore)  
-   en-US (inglese, Stati Uniti)  
-   en-WW (inglese, codice di aggregazione Tutto il mondo)  
-   en-ZA (inglese, Sudafrica)  
-   zh-CN (cinese, Cina)

  
L'esempio seguente illustra una risposta contenente video di tendenza.  

```  
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```  
La risposta contiene un elenco di video per categoria e sottocategoria. Se ad esempio l'elenco delle categorie contiene una categoria Music Videos e una delle relative sottocategorie è Top, è possibile creare una categoria Top Music Videos all'interno dell'esperienza utente. È quindi possibile usare i campi `thumbnailUrl`, `displayText` e `webSearchUrl` per creare un riquadro selezionabile in ogni categoria (ad esempio, Top Music Videos). Quando l'utente fa clic sul riquadro, passa al browser dei video di Bing in cui il video viene riprodotto.

La risposta contiene anche video banner, i video di tendenza più diffusi. I video banner possono provenire da una o più categorie.  
  
