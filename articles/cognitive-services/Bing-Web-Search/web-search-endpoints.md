---
title: Endpoint di Ricerca Web
titleSuffix: Azure Cognitive Services
description: Riepilogo dell'endpoint dell'API Ricerca Web.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 11/14/2018
ms.author: v-gedod
ms.openlocfilehash: 2f7e6cd577b1eabbaabdfe87fca8ea0f036a062d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149391"
---
# <a name="web-search-endpoint"></a>Endpoint di Ricerca Web

L'**API Ricerca Web** restituisce pagine Web, notizie, immagini, video ed [entità](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Le entità contengono informazioni di riepilogo su una persona, un luogo o un argomento.

## <a name="endpoint"></a>Endpoint

Per ottenere risultati della ricerca Web tramite l'API Bing, inviare una richiesta `GET` all'endpoint seguente. Le intestazioni e i parametri URL definiscono ulteriori specifiche.

**Endpoint**: restituisce risultati Web attinenti alla query di ricerca dell'utente definita da `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Endpoint: per i dettagli relativi a intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere le informazioni di riferimento sull'[API Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

## <a name="response-json"></a>Risposta JSON

I risultati inclusi nella risposta a una richiesta di ricerca Web sono tutti costituiti da oggetti JSON. L'analisi dei risultati richiede procedure che gestiscano gli elementi di ogni tipo. Per alcuni esempi, vedere l'[esercitazione](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) e il [codice sorgente](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search).

## <a name="next-steps"></a>Passaggi successivi

Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti della risposta JSON.  Tutti gli endpoint supportano query che restituiscono una lingua e una posizione specifiche in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base eseguite con l'API Ricerca Web, vedere le [guide introduttive per la ricerca Web](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
