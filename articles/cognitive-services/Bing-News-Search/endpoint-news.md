---
title: Endpoint di Ricerca notizie Bing
titlesuffix: Azure Cognitive Services
description: Riepilogo degli endpoint dell'API Ricerca notizie.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: v-gedod
ms.openlocfilehash: b4a1ba7536f0e899ef845bbac54b551125d35fb7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868544"
---
# <a name="bing-news-search-api-endpoints"></a>Endpoint di API Ricerca notizie Bing

L'**API Ricerca notizie** restituisce articoli, pagine Web, immagini, video ed [entità](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Le entità contengono informazioni di riepilogo su una persona, un luogo o un argomento.

## <a name="endpoints"></a>Endpoint

Per ottenere risultati di Ricerca notizie tramite l'API Ricerca notizie Bing, inviare una richiesta `GET` a uno degli endpoint seguenti. Le intestazioni e i parametri URL definiscono ulteriori specifiche.

### <a name="news-items-by-search-query"></a>Elementi di notizie da query di ricerca

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Restituisce gli elementi di notizie basati su una query di ricerca. Se la query di ricerca è vuota, l'API restituirà gli articoli di notizie principali dal categorie diverse. Inviare una query per url codificando il termine di ricerca e aggiungerlo al parametro `q=""`. Per la disponibilità, vedere [paesi/aree geografiche e mercati supportati](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Elementi di notizie principali per categoria

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Restituisce le notizie principali per categoria. È possibile richiedere in modo specifico gli articoli principali di affari, sport o intrattenimento usando `category=business`, `category=sports` o `category=entertainment`.  Il parametro `category` può essere usato solo con l'URL `/news`. Esistono alcuni requisiti formali per specificare le categorie. Fare riferimento a `category` nella documentazione relativa al [parametro di query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Inviare una query per url codificando il termine di ricerca e aggiungerlo al parametro `q=""`. Per la disponibilità, vedere [paesi/aree geografiche e mercati supportati](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Argomenti delle notizie di tendenza 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Restituisce argomenti delle notizie di tendenza nei social network. Quando viene inclusa l'opzione `/trendingtopics`, la ricerca Bing ignora altri parametri, quali `freshness` e `?q=""`. Per la disponibilità, vedere [paesi/aree geografiche e mercati supportati](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Passaggi successivi

Per maggiori dettagli su intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere il riferimento per l'[API Ricerca notizie Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base effettuate con l'API Ricerca notizie, vedere le [guide introduttive per Ricerca notizie di Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
