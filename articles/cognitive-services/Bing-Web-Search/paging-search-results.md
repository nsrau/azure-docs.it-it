---
title: Procedura di pagina nei risultati della ricerca-API di ricerca Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la pagina dei risultati della ricerca dall'API di ricerca Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: aahi
ms.openlocfilehash: e2149c7db20939a739380dc2df0e21b6a62ed916
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955360"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Come eseguire il paging dei risultati dalla API di ricerca Bing

Quando si invia una chiamata a Bing Web, Custom, image, News o Ricerca video API, Bing restituisce un subset del numero totale di risultati che possono essere rilevanti per la query. Per ottenere il numero totale stimato dei risultati disponibili, accedere al campo `totalEstimatedMatches` dell'oggetto risposta. 

Esempio: 

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

Per eseguire il paging dei risultati disponibili, usare i parametri di query `count` e `offset` durante l'invio della richiesta.  

> [!NOTE]
>
> * Il paging con le API video, immagini e notizie Bing si applica solo alle ricerche video generali (`/video/search`), News (`/news/search`) e Image (`/image/search`). Il paging degli argomenti e delle categorie di tendenza non è supportato.  
> * Il campo `TotalEstimatedMatches` è una stima del numero totale di risultati della ricerca per la query corrente. Quando si impostano i parametri `count` e `offset`, la stima potrebbe cambiare.

| Parametro | Descrizione                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Specifica il numero di risultati da restituire nella risposta. Si noti che il valore predefinito di `count` e il numero massimo di risultati che è possibile richiedere variano in base all'API. È possibile trovare questi valori nella documentazione di riferimento in [passaggi successivi](#next-steps). |
| `offset`  | Specifica il numero di risultati da ignorare. `offset` è in base zero e deve essere inferiore a (`totalEstimatedMatches` - `count`).                                           |

Se ad esempio si desidera visualizzare 15 risultati per pagina, impostare `count` su 15 e `offset` su 0 per ottenere la prima pagina di risultati. Per ogni chiamata API successiva, è necessario incrementare `offset` per 15. Di seguito viene illustrato un esempio che richiede 15 pagine Web che iniziano in corrispondenza dell'offset 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se si usa il valore predefinito `count`, è sufficiente specificare il parametro di query `offset` nelle chiamate API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Il API Ricerca Web Bing restituisce i risultati della ricerca che possono includere pagine Web, immagini, video e notizie. Quando si passano i risultati della ricerca dalla API Ricerca Web Bing, si esegue il paging solo di [pagine Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)e non altri tipi di risposte, ad esempio immagini o notizie. I risultati della ricerca in oggetti `WebPage` possono includere i risultati visualizzati anche in altri tipi di risposta.
>
> Se si usa il parametro di query `responseFilter` senza specificare alcun valore di filtro, non usare i parametri `count` e `offset`. 

## <a name="next-steps"></a>Passaggi successivi

* [Quali sono le API di Ricerca Web Bing?](bing-api-comparison.md)
* [Informazioni di riferimento per l'API Ricerca Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-referenc)
* [Riferimento API Ricerca personalizzata Bing V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Riferimento API Ricerca notizie Bing V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Riferimento API Ricerca video Bing V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Riferimento API Ricerca immagini Bing V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)