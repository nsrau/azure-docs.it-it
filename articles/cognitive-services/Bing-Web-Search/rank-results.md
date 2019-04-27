---
title: Come usare le classificazioni per visualizzare i risultati della ricerca - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la classificazione per visualizzare i risultati della ricerca nell'API Ricerca Web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 9a49c4af474d7f0618bf0cff1a093e5cbb62fe2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648823"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Come usare la classificazione per visualizzare i risultati dell'API Ricerca Web Bing  

La risposta di ogni ricerca include un riscontro [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse), che specifica come devono essere visualizzati i risultati della ricerca. La risposta di classificazione raggruppa i risultati per contenuto principale e contenuto della barra laterale per una pagina di risultati della ricerca tradizionale. Se i risultati non vengono visualizzati in un formato di contenuto principale e della barra laterale tradizionale, è necessario dare al contenuto principale una maggiore visibilità rispetto al contenuto della barra laterale.  

All'interno di ogni gruppo (principale o barra laterale) la matrice [Items](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifica l'ordine in cui deve essere visualizzato il contenuto. Ogni elemento offre i due modi seguenti per identificare il risultato all'interno di un riscontro.  

-   `answerType` e `resultIndex`: il campo `answerType` identifica il riscontro (ad esempio, Webpage o News) e `resultIndex` identifica un risultato all'interno del riscontro (ad esempio, un articolo di notizie). L'indice è in base zero.  

-   `value`: il campo `value` contiene un ID che corrisponde all'ID di un riscontro o un risultato all'interno del riscontro. L'ID è incluso nel riscontro o nei risultati, ma non in entrambi.  

L'ID è più semplice da usare perché è necessario solo che l'ID di classificazione corrisponda all'ID di un riscontro o a uno dei risultati. Se un oggetto riscontro include un campo `id`, tutti i risultati del riscontro vengono visualizzati insieme. Ad esempio, se l'oggetto `News` include il campo `id`, tutti gli articoli di notizie vengono visualizzati insieme. Se l'oggetto `News` non include il campo `id`, ogni articolo di notizie conterrà un campo `id` e la risposta di classificazione combinerà gli articoli di notizie con i risultati degli altri riscontri.  

L'uso di `answerType` e `resultIndex` è un po' più complesso. Usare `answerType` per identificare il riscontro contenente i risultati da visualizzare. Usare quindi `resultIndex` per indicizzare i risultati del riscontro e ottenere il risultato da visualizzare. Il valore `answerType` è il nome del campo nell'oggetto [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse). Se occorre visualizzare i risultati del riscontro tutti insieme, l'elemento della risposta di classificazione non include il campo `resultIndex`.  

## <a name="ranking-response-example"></a>Esempio di risposta di classificazione

Di seguito viene illustrato un esempio di [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). Poiché il riscontro Web non include un campo `id`, ogni singola pagina Web verrà visualizzata a seconda della classificazione. Ogni pagina Web include un campo `id`. Poiché invece i riscontri relativi a immagini, video e ricerche correlate includono il campo `id`, i risultati di ognuno di tali riscontri vengono visualizzati insieme in base alla classificazione.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

In base alla risposta di classificazione, il contenuto principale visualizzerà i risultati della ricerca seguenti:  

-   Il primo risultato della pagina Web
-   Tutte le immagini  
-   Il secondo e il terzo risultato della pagina Web  
-   Tutti i video  
-   Il quarto, quinto e sesto risultato della pagina Web  

La barra laterale visualizzerà invece i risultati della ricerca seguenti:  

-   Tutte le ricerche correlate  


## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'innalzamento di livello dei risultati non classificati, vedere [Innalzamento di livello dei riscontri non classificati](./filter-answers.md#promoting-answers-that-are-not-ranked).

Per informazioni sulla limitazione del numero di riscontri classificati nella risposta, vedere [Limitazione del numero di riscontri nella risposta](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Per un esempio in C# che usa la classificazione per visualizzare i risultati, vedere [C# ranking tutorial (Esercitazione sulla classificazione in C#)](./csharp-ranking-tutorial.md).
