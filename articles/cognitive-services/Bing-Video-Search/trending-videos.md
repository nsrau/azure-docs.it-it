---
title: Cercare i video di tendenza sul Web con l'API Ricerca video Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API Ricerca video Bing per cercare i video di tendenza sul Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500604"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Cercare i video di tendenza con l'API Ricerca video Bing 

L'API Ricerca video Bing consente di trovare i video di tendenza più recenti sul Web ed elencarli in categorie diverse. 

## <a name="get-request"></a>Richiesta GET

Per ottenere i video di tendenza più recenti con l'API Ricerca video Bing, inviare la richiesta GET seguente:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Mercati in cui la funzionalità è supportata

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

## <a name="example-json-response"></a>Risposta JSON di esempio  

L'esempio seguente mostra una risposta dell'API che contiene i video di tendenza, elencati per categoria e sottocategoria. La risposta contiene anche video banner, ovvero i video di tendenza più diffusi, che possono provenire da una o più categorie.  

```json
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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere informazioni dettagliate su un video](video-insights.md)