---
title: Endpoint di Ricerca notizie Bing | Microsoft Docs
description: Riepilogo degli endpoint dell'API Ricerca notizie.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: ab892e947566adf025499382b213a52ed3e96e35
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433685"
---
# <a name="news-search-endpoints"></a>Endpoint di Ricerca notizie
L'**API Ricerca notizie** restituisce articoli, pagine Web, immagini, video ed [entità](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Le entità contengono informazioni di riepilogo su una persona, un luogo o un argomento.
## <a name="endpoints"></a>Endpoint
Per ottenere risultati di Ricerca notizie tramite l'API Bing, inviare una richiesta `GET` a uno degli endpoint seguenti. Le intestazioni e i parametri URL definiscono ulteriori specifiche.

**Endpoint 1:** restituisce notizie in base alla query di ricerca dell'utente. Se la query di ricerca è vuota, la chiamata restituisce gli articoli con le notizie principali. È possibile usare l'opzione query `?q=""` anche con l'URL `/news`. Per la disponibilità, vedere [Paesi e mercati supportati](language-support.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

**Endpoint 2:** restituisce le notizie principali per categoria. È possibile richiedere in modo specifico gli articoli principali di affari, sport o intrattenimento usando `category=business`, `category=sports` o `category=entertainment`.  Il parametro `category` può essere usato solo con l'URL `/news`. Esistono alcuni requisiti formali per specificare le categorie. Fare riferimento a `category` nella documentazione relativa al [parametro di query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Per la disponibilità, vedere [Paesi e mercati supportati](language-support.md#supported-markets-for-news-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Endpoint 3:** restituisce argomenti delle notizie di tendenza nei social network. Quando viene inclusa l'opzione `/trendingtopics`, la ricerca Bing ignora altri parametri, quali `freshness` e `?q=""`. Per la disponibilità, vedere [Paesi e mercati supportati](language-support.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Per maggiori dettagli su intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere il riferimento per l'[API Ricerca notizie Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).
## <a name="response-json"></a>Risposta JSON
La risposta a una richiesta di ricerca notizie include i risultati come oggetti JSON. L'analisi dei risultati richiede procedure che gestiscono gli elementi di ogni tipo. Per alcuni esempi, vedere l'[esercitazione](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) e il [codice sorgente](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source).

## <a name="next-steps"></a>Passaggi successivi
Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti della risposta JSON.  Tutti gli endpoint supportano query che restituiscono una posizione e/o una lingua specifica in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base effettuate con l'API Ricerca notizie, vedere le [guide introduttive per Ricerca notizie di Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
