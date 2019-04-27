---
title: Come sfogliare i risultati della ricerca - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come sfogliare i risultati della ricerca nell'API Ricerca Web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: aahi
ms.openlocfilehash: 68b99e7f0763f46c705a1ff2ba3a0e90216429c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648857"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Come sfogliare i risultati nell'API Ricerca Web Bing

Quando si chiama l'API Ricerca Web, Bing restituisce un elenco di risultati. L'elenco è un subset del numero totale di risultati pertinenti alla query. Per ottenere il numero totale stimato di risultati disponibili, accedere al campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) dell'oggetto risposta.  

L'esempio seguente illustra il campo `totalEstimatedMatches` incluso in una risposta Web.  

```
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

Per sfogliare tutte le pagine Web disponibili, usare i parametri di query [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) e [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset).  

Il parametro `count` specifica il numero di risultati da restituire nella risposta. Il numero massimo di risultati che è possibile richiedere nella risposta è 50. Il valore predefinito è 10. Il numero effettivo restituito può essere inferiore a quello richiesto.

Il parametro `offset` specifica il numero di risultati da ignorare. `offset` è in base zero e deve essere inferiore a (`totalEstimatedMatches` - `count`).  

Per visualizzare 15 pagine Web per pagina, impostare `count` su 15 e `offset` su 0 per ottenere la prima pagina di risultati. Per ogni pagina successiva, aumentare `offset` di 15 (ad esempio, 15, 30).  

Di seguito viene illustrato un esempio che richiede 15 pagine Web che iniziano in corrispondenza dell'offset 45.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se il valore predefinito di `count` risulta appropriato per l'implementazione, è sufficiente specificare il parametro di query `offset`.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

L'API Ricerca Web restituisce risultati che includono pagine Web e possono includere immagini, video e notizie. Quando si visualizzano i risultati di ricerca, viene visualizzata la risposta [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) e non vengono visualizzate le altre risposte come ad esempio le immagini o le notizie. Ad esempio, se si imposta `count` su 50, vengono restituiti 50 risultati della pagina Web, ma la risposta potrebbe includere anche risultati per le altre risposte. Ad esempio, la risposta potrebbe includere 15 immagini e 4 articoli di notizie. È anche possibile che i risultati includano le notizie nella prima pagina ma non nella seconda o viceversa.   

Se si specifica il parametro di query `responseFilter` e non si includono le pagine Web nell'elenco di filtri, non usare i parametri `count` e `offset`. 

> [!NOTE]
> Il campo `TotalEstimatedAnswers` è una stima del numero totale di risultati della ricerca che è possibile recuperare per la query corrente.  Quando si impostano i parametri `count` e `offset`, il numero di `TotalEstimatedAnswers` può cambiare. 
