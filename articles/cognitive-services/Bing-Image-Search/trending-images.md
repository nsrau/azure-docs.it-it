---
title: Ottenere immagini di tendenza con l'API Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
description: Cercare le attuali immagini di tendenza nel Web con l'API Ricerca immagini Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 024e007a34f71256d5cd148720412a0c57bb74ed
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342225"
---
# <a name="get-trending-images-from-the-web"></a>Ottenere immagini di tendenza dal Web

Per ottenere le immagini di tendenza più recenti, inviare la richiesta GET seguente:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

L'API delle immagini di tendenza supporta attualmente solo i mercati seguenti:  

- en-US (inglese, Stati Uniti)  
- en-CA (inglese, Canada)  
- en-AU (inglese, Australia)  
- zh-CN (cinese, Cina)

La risposta contiene un oggetto [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) che elenca immagini per categoria. Usare la proprietà `title` della categoria per raggruppare le immagini nell'esperienza utente. Le categorie possono cambiare ogni giorno.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Ogni riquadro contiene un'immagine e alcune opzioni che consentono di ottenere immagini correlate. Per ottenere le immagini correlate, è possibile usare `text` della query per chiamare l'[API Ricerca immagini](./search-the-web.md) e visualizzare personalmente le immagini correlate. In alternativa, è possibile usare l'URL in `webSearchUrl` per far passare l'utente alla pagina di Bing dei risultati della ricerca di immagini, contenente le immagini correlate.

Se si chiama l'API Ricerca immagini per ottenere le immagini correlate, impostare il parametro [id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) della query sull'ID presente nel campo `id`. L'indicazione dell'ID assicura che la risposta contenga l'immagine (la prima immagine nella risposta) e le relative immagini correlate. Impostare poi il parametro di query [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) sul testo nel campo `text` dell'oggetto `query`.

L'esempio seguente illustra come usare l'ID dell'immagine per ottenere immagini correlate al signor Smith nella risposta precedente dell'API delle immagini di tendenza.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
