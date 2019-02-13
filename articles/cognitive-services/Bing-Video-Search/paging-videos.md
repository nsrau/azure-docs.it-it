---
title: Come restituire in più pagine i video disponibili - Ricerca video Bing
titlesuffix: Azure Cognitive Services
description: Informazioni su come restituire in più pagine tutti i video trovati dall'API Ricerca video Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 5fd11088c84327325040a05de9616f19c3455b01
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564902"
---
# <a name="paging-through-video-search-results"></a>Restituire in più pagine i risultati della ricerca di video

L'API Ricerca video Bing restituisce un subset di tutti i risultati trovati per una query. Se si restituiscono questi risultati in più pagine, con conseguenti chiamate all'API, è possibile mostrarli nella propria applicazione.

> [!NOTE]
> Questa funzione si applica solo alla ricerca di video (/videos/search) e non alle informazioni dettagliate sui video (/videos/details) o ai video di tendenza (/videos/trending).

## <a name="total-estimated-matches"></a>Ottenere la stima totale delle corrispondenze

Per ottenere il numero stimato dei risultati della ricerca trovati, usare il campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) nella risposta JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Restituire in più pagine i video

Per restituire in più pagine i video disponibili, usare i parametri di query [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) e [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) quando si invia la richiesta.  
  

|Parametro  |Descrizione  |
|---------|---------|
|`count`     | Specifica il numero di risultati da restituire nella risposta. Il numero massimo di risultati che è possibile richiedere nella risposta è 100. Il valore predefinito è 10. Il numero effettivo restituito può essere inferiore a quello richiesto.        |
|`offset`     | Specifica il numero di risultati da ignorare. `offset` è in base zero e deve essere inferiore a (`totalEstimatedMatches` - `count`).          |

Ad esempio, per visualizzare 20 articoli per pagina, impostare `count` su 20 e `offset` su 0 per ottenere la prima pagina di risultati. Per ogni pagina successiva, aumentare `offset` di 20 (ad esempio, 20, 40).  
  
L'esempio seguente richiede 20 video a partire dall'offset 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Se si usa il valore predefinito per [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count), è sufficiente specificare il parametro di query `offset`, come nell'esempio seguente.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Per restituire pagine di 35 video alla volta, si imposta il parametro di query `offset` su 0 nella prima richiesta e quindi si incrementa `offset` di 35 in ogni richiesta successiva. Alcuni risultati nella risposta successiva possono tuttavia contenere video duplicati della risposta precedente. Ad esempio, i primi due video in una risposta possono corrispondere a quelli che compaiono per ultimi nella risposta precedente.

Per eliminare i risultati duplicati, usare il campo [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) dell'oggetto `Videos`.

Se ad esempio si vogliono restituire pagine di 30 video alla volta, impostare `count` su 30 e `offset` su 0 nella prima richiesta. Nella richiesta successiva sarà sufficiente impostare il parametro di query `offset` sul valore `nextOffset`.

> [!NOTE]
> Il campo `TotalEstimatedAnswers` è una stima del numero totale di risultati della ricerca che è possibile recuperare per la query corrente.  Quando si impostano i parametri `count` e `offset`, il numero di `TotalEstimatedAnswers` può cambiare. 
  
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
[Ottenere informazioni dettagliate su un video](video-insights.md)
