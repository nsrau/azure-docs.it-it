---
title: Come sfogliare gli articoli di notizie disponibili | Microsoft Docs
description: Illustra come sfogliare tutti gli articoli di notizie che possono essere restituiti da Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EA388F72-FA43-493B-967C-9560B3243C62
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2c90d468536f0864d7deac073667e29e9a54692f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372849"
---
# <a name="paging-news"></a>Sfogliare le notizie

Quando si chiama l'API Ricerca notizie, Bing restituisce un elenco di risultati. L'elenco è un subset del numero totale di risultati pertinenti alla query. Per ottenere il numero totale stimato di risultati disponibili, accedere al campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) dell'oggetto risposta.  
  
L'esempio seguente illustra il campo `totalEstimatedMatches` incluso in una risposta News.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Per sfogliare tutti gli articoli disponibili, usare i parametri di query [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) e [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset).  
  
Il parametro `count` specifica il numero di risultati da restituire nella risposta. Il numero massimo di risultati che è possibile richiedere nella risposta è 100. Il valore predefinito è 10. Il numero effettivo restituito può essere inferiore a quello richiesto.

Il parametro `offset` specifica il numero di risultati da ignorare. `offset` è in base zero e deve essere inferiore a (`totalEstimatedMatches` - `count`).  

Per visualizzare 20 articoli per pagina, impostare `count` su 20 e `offset` su 0 per ottenere la prima pagina di risultati. Per ogni pagina successiva, aumentare `offset` di 20 (ad esempio, 20, 40).  
  
Di seguito viene illustrato un esempio che richiede 20 articoli di notizie che iniziano in corrispondenza dell'offset 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Se il valore predefinito di `count` risulta appropriato per l'implementazione, è sufficiente specificare il parametro di query `offset`, come illustrato nell'esempio seguente:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Il paging si applica solo alla ricerca di notizie (/news/search) e non agli argomenti di tendenza (/news/trendingtopics) o alle categorie di notizie (/news).