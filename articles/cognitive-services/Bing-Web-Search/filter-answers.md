---
title: Come filtrare i risultati della ricerca - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come filtrare e visualizzare i risultati della ricerca dall'API Ricerca Web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: a89d73b63680415aa8e516926b8e1d6c59ffbbad
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626011"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrare i riscontri inclusi nella risposta della ricerca  

Quando si eseguono query nel Web, Bing restituisce tutto il contenuto rilevante che trova per la ricerca. Ad esempio, se la query di ricerca è "sailing+dinghies" (derive), la risposta potrebbe contenere i riscontri seguenti:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Parametri di query

Per filtrare le risposte restituite da Bing, usare i parametri di query quando si chiamano le API seguenti.  

### <a name="responsefilter"></a>ResponseFilter

È possibile filtrare i tipi di risposte di Bing è include nella risposta (ad esempio immagini, video e notizie) usando il [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) parametro di query, ovvero un elenco delimitato da virgole di risposte. Una risposta verrà inclusi nella risposta se Bing rileva contenuti rilevanti per tale. 

Per escludere le risposte specifiche dalla risposta, ad esempio immagini, anteporre una `-` carattere per il tipo di risposta. Ad esempio:

```
&responseFilter=-images,-videos
```

L'esempio seguente mostra come usare `responseFilter` per richiedere immagini, video e notizie relative alle derive. Per la codifica di questa stringa di query, le virgole diventano %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Di seguito è riportata la risposta alla query precedente. Poiché Bing non ha trovato video e notizie rilevanti come risultati, la risposta non li include.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Anche se Bing non ha restituito risultati per video e notizie nella risposta precedente, non significa che non esiste contenuto video e di notizie. Indica semplicemente che la pagina non ha incluso questo contenuto. Tuttavia, se si scorrono altre [pagine](./paging-webpages.md) per visualizzare più risultati, è probabile che le pagine successive includano questo tipo di contenuto. Inoltre, se si chiamano direttamente gli endpoint [API Ricerca video](../bing-video-search/search-the-web.md) e [API Ricerca notizie](../bing-news-search/search-the-web.md), la risposta conterrà probabilmente risultati.

È sconsigliato l'uso di `responseFilter` per ottenere risultati da una singola API. Se si vuole ottenere contenuto da una singola API di Bing, chiamare direttamente tale API. Ad esempio, per ricevere solo le immagini, inviare una richiesta all'endpoint API Ricerca immagini `https://api.cognitive.microsoft.com/bing/v7.0/images/search` o uno degli altri endpoint [Immagini](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints). Chiamare l'API specifica è importante non solo per motivi di prestazioni, ma anche perché le API per contenuto specifico offrono risultati migliori. Ad esempio, è possibile usare filtri che non sono disponibili per l'API Ricerca Web per filtrare i risultati.  

### <a name="site"></a>Sito

Per ottenere i risultati della ricerca da un dominio specifico, includere il `site:` parametro nella stringa di query di query.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> A seconda della query, se si usa l'operatore di query `site:`, la risposta potrebbe includere contenuto per adulti indipendentemente dall'impostazione di [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch). È consigliabile usare `site:` solo se si conosce il contenuto del sito e lo scenario prevede la possibilità di contenuto per adulti.

### <a name="freshness"></a>Aggiornamento

Per limitare i risultati di risposta web in pagine Web di Bing individuati durante un periodo specifico, impostare il [freshness](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) parametro a uno dei valori tra maiuscole e minuscole seguenti query:

* `Day` : Restituisce le pagine Web Bing individuati all'interno delle ultime 24 ore
* `Week` Ovvero restituire pagine Web di Bing individuati negli ultimi 7 giorni
* `Month` : Restituisce le pagine Web che individuati negli ultimi 30 giorni

È anche possibile impostare questo parametro per un intervallo di date personalizzato nel modulo, `YYYY-MM-DD..YYYY-MM-DD`. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Per limitare i risultati in una singola data, impostare il parametro di validità su una data specifica:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

I risultati possono includere le pagine Web che non rientrano nel periodo specificato, se il numero di pagine Web di Bing corrisponde ai criteri di filtro è inferiore al numero di pagine Web richiesto (o il numero predefinito di Bing restituisce).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limitazione del numero di riscontri nella risposta

Bing può restituire più tipi di risposta nella risposta JSON. Ad esempio, se si esegue una query *sailing + dinghies*, Bing potrebbe restituire `webpages`, `images`, `videos`, e `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Per limitare il numero di riscontri restituiti da Bing ai primi due (pagine Web e immagini), impostare il parametro di query [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) su 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

La risposta include solo `webPages` e `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Se si aggiunge il parametro di query `responseFilter` alla query precedente e si imposta il filtro per pagine Web e notizie, la risposta contiene solo pagine Web perché le notizie non sono classificate.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Alzare di livello i riscontri non classificati

Se i riscontri con classificazione migliore restituiti da Bing per una query sono pagine Web, immagini, video e ricerche correlate, la risposta includerà questi riscontri. Se si imposta [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) su due (2), Bing restituisce i primi due riscontri nella classificazione, ovvero pagine Web e immagini. Se si vuole che Bing includa immagini e video nella risposta, specificare il parametro di query [promote](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) e impostarlo su images e videos.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Di seguito è indicata la risposta alla richiesta precedente. Bing restituisce i primi due riscontri, ovvero pagine Web e immagini, quindi alza di livello i video nel riscontro.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Se si imposta `promote` su news, la risposta non include riscontri di tipo notizie perché non è un riscontro classificato ed è possibile alzare di livello solo i riscontri classificati.

I riscontri che si vogliono alzare di livello non vengono inclusi incluso nel calcolo del limite di `answerCount`. Ad esempio, se i riscontri classificati sono notizie, immagini e video e si imposta `answerCount` su 1 e `promote` su news, la risposta contiene notizie e immagini. In alternativa, se i riscontri classificati sono video, immagini e notizie, la risposta contiene video e notizie.

È possibile usare `promote` solo se si specifica il parametro di query `answerCount`.
