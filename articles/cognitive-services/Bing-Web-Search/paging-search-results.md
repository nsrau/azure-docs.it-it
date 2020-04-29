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
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73481737"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Come eseguire il paging dei risultati dalla API di ricerca Bing

Quando si invia una chiamata a Bing Web, Custom, image, News o Ricerca video API, Bing restituisce un subset del numero totale di risultati che possono essere rilevanti per la query. Per ottenere il numero totale stimato di risultati disponibili, accedere al `totalEstimatedMatches` campo dell'oggetto risposta. 

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

Per eseguire il paging dei risultati disponibili, usare `count` i `offset` parametri di query e quando si invia la richiesta.  

> [!NOTE]
>
> * Il paging con le API video, immagini e notizie Bing si applica solo alle ricerche video`/video/search`(), News`/news/search`() e image`/image/search`() generali. Il paging degli argomenti e delle categorie di tendenza non è supportato.  
> * Il `TotalEstimatedMatches` campo è una stima del numero totale di risultati della ricerca per la query corrente. Quando si `count` impostano `offset` i parametri e, la stima potrebbe cambiare.

| Parametro | Descrizione                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Specifica il numero di risultati da restituire nella risposta. Si noti che il valore predefinito `count`di e il numero massimo di risultati che è possibile richiedere variano in base all'API. È possibile trovare questi valori nella documentazione di riferimento in [passaggi successivi](#next-steps). |
| `offset`  | Specifica il numero di risultati da ignorare. `offset` è in base zero e deve essere inferiore a (`totalEstimatedMatches` - `count`).                                           |

Se ad esempio si desidera visualizzare 15 risultati per pagina, impostare `count` su 15 e `offset` su 0 per ottenere la prima pagina di risultati. Per ogni successiva chiamata API, viene incrementato `offset` di 15. Di seguito viene illustrato un esempio che richiede 15 pagine Web che iniziano in corrispondenza dell'offset 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se si usa il valore `count` predefinito, è sufficiente specificare il `offset` parametro di query nelle chiamate API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Quando si usano le API di immagine e video di Bing, è `nextOffset` possibile usare il valore per evitare risultati di ricerca duplicati. Ottenere il valore dagli oggetti `Images` di `Videos` risposta o e usarlo nelle richieste con il `offset` parametro.  

> [!NOTE]
> Il API Ricerca Web Bing restituisce i risultati della ricerca che possono includere pagine Web, immagini, video e notizie. Quando si passano i risultati della ricerca dalla API Ricerca Web Bing, si esegue il paging solo di [pagine Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)e non altri tipi di risposte, ad esempio immagini o notizie. I risultati della `WebPage` ricerca negli oggetti possono includere i risultati visualizzati anche in altri tipi di risposta.
>
> Se si usa il `responseFilter` parametro di query senza specificare alcun valore di filtro, non `count` usare `offset` i parametri e. 

## <a name="next-steps"></a>Passaggi successivi

* [Quali sono le API di Ricerca Web Bing?](bing-api-comparison.md)
* [Informazioni di riferimento per l'API Ricerca Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Riferimento API Ricerca personalizzata Bing V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Riferimento API Ricerca notizie Bing V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Riferimento API Ricerca video Bing V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Riferimento API Ricerca immagini Bing V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
