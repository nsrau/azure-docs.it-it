---
title: Come scorrere i risultati della ricerca - API di Ricerca Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come scorrere i risultati della ricerca dalle API di Ricerca Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481737"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Come scorrere i risultati delle API di Ricerca Bing

Quando si invia una chiamata alle API Bing Web, Custom, Image, News o Video Search, Bing restituisce un sottoinsieme del numero totale di risultati che potrebbero essere rilevanti per la query. Per ottenere il numero totale stimato di risultati `totalEstimatedMatches` disponibili, accedere al campo dell'oggetto risposta. 

Ad esempio: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Paging dei risultati della ricerca

Per scorrere i risultati disponibili, utilizzare i `count` parametri e `offset` di query quando si invia la richiesta.  

> [!NOTE]
>
> * Il paging con le API Bing Video, Image e`/video/search`News si`/news/search`applica solo`/image/search`alle ricerche di video generali ( ), notizie ( ) e immagine ( ). Il paging tra argomenti e categorie di tendenza non è supportato.  
> * Il `TotalEstimatedMatches` campo è una stima del numero totale di risultati della ricerca per la query corrente. Quando si `count` impostano i parametri e `offset` , questa stima può cambiare.

| Parametro | Descrizione                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Specifica il numero di risultati da restituire nella risposta. Si noti che `count`il valore predefinito di , e il numero massimo di risultati che è possibile richiedere variano in base all'API. Questi valori sono disponibili nella documentazione di riferimento in [Passaggi successivi](#next-steps). |
| `offset`  | Specifica il numero di risultati da ignorare. `offset` è in base zero e deve essere inferiore a (`totalEstimatedMatches` - `count`).                                           |

Ad esempio, se si desidera visualizzare 15 risultati `count` per pagina, `offset` è necessario impostare su 15 e su 0 per ottenere la prima pagina di risultati. Per ogni chiamata API successiva, è necessario incrementare di 15.For each subsequent API call, you would increment `offset` by 15. Di seguito viene illustrato un esempio che richiede 15 pagine Web che iniziano in corrispondenza dell'offset 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se si utilizza `count` il valore predefinito, `offset` è sufficiente specificare il parametro di query nelle chiamate API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Quando si usano le API Bing Image e `nextOffset` Video, è possibile usare il valore per evitare risultati di ricerca duplicati. Ottenere il valore `Images` `Videos` dagli oggetti risposta o e usarlo nelle richieste con il `offset` parametro .  

> [!NOTE]
> L'API Ricerca Web Bing restituisce risultati di ricerca che possono includere pagine Web, immagini, video e notizie. Quando si esegue il paging dei risultati della ricerca dall'API Ricerca Web Bing, si esegue il paging solo [di pagine Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)e non altri tipi di risposta, ad esempio immagini o notizie. I risultati `WebPage` della ricerca negli oggetti possono includere risultati visualizzati anche in altri tipi di risposta.
>
> Se si `responseFilter` utilizza il parametro query senza specificare `count` alcun `offset` valore di filtro, non utilizzare i parametri e . 

## <a name="next-steps"></a>Passaggi successivi

* [Che cosa sono le API di Bing Web Search?](bing-api-comparison.md)
* [Informazioni di riferimento per l'API Ricerca Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Riferimento all'API di ricerca personalizzata Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Riferimento all'API Ricerca di notizie Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Riferimento all'API di ricerca video Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Riferimento all'API Ricerca immagini Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
