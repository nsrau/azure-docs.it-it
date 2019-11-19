---
title: Endpoint di Ricerca Web
titleSuffix: Azure Cognitive Services
description: Per ottenere i risultati della ricerca Web, inviare una richiesta di `GET` al seguente endpoint. Le intestazioni e i parametri URL definiscono ulteriori specifiche.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111346"
---
# <a name="web-search-endpoint"></a>Endpoint di Ricerca Web

L'**API Ricerca Web** restituisce pagine Web, notizie, immagini, video ed [entità](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Le entità contengono informazioni di riepilogo su una persona, un luogo o un argomento.

## <a name="endpoint"></a>Endpoint

Per ottenere risultati della ricerca Web tramite l'API Bing, inviare una richiesta `GET` all'endpoint seguente. Le intestazioni e i parametri URL definiscono ulteriori specifiche.

**Endpoint:** restituisce risultati Web attinenti alla query di ricerca dell'utente definita da `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Endpoint: per i dettagli relativi a intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere [Web Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) (Informazioni di riferimento sull'API Ricerca Web versione 7).

## <a name="response-json"></a>Risposta JSON

I risultati inclusi nella risposta a una richiesta di ricerca Web sono tutti costituiti da oggetti JSON. L'analisi dei risultati richiede procedure che gestiscano gli elementi di ogni tipo. Per alcuni esempi, vedere l'[esercitazione](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) e il [codice sorgente](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search).

## <a name="next-steps"></a>Passaggi successivi

Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al loro tipo. Tutti gli endpoint di ricerca restituiscono i risultati come oggetti risposta JSON.  Tutti gli endpoint supportano le query che restituiscono una lingua e una posizione specifiche per longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base eseguite con l'API Ricerca Web, vedere le [guide introduttive per la ricerca Web](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
