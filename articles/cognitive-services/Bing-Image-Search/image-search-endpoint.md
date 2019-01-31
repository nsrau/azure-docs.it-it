---
title: Endpoint per l'API Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
description: Elenco degli endpoint disponibili per l'API Ricerca immagini Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: 7208f1b3f1658c23a6b38c661a37b050c92ca891
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55204083"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Endpoint per l'API Ricerca immagini Bing

L'**API Ricerca immagini** include tre endpoint.  L'endpoint 1 restituisce immagini dal Web in base a una query. L'endpoint 2 restituisce [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse).  L'endpoint 3 restituisce immagini di tendenza.
## <a name="endpoints"></a>Endpoint
Per ottenere risultati immagini tramite l'API Bing, inviare una richiesta a uno degli endpoint seguenti. Usare le intestazioni e i parametri URL per definire altre specifiche.

**Endpoint 1:** restituisce immagini attinenti alla query di ricerca dell'utente definita da `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Endpoint 2:** restituisce informazioni dettagliate su un'immagine usando `GET` o `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Una richiesta GET restituisce informazioni dettagliate su un'immagine, ad esempio le pagine Web che includono l'immagine. Includere il parametro [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) con una richiesta `GET`.

In alternativa, è possibile includere un'immagine binaria nel corpo di una richiesta `POST` e impostare il parametro [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) su `RecognizedEntities`. Viene restituito un parametro [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken) da usare in una richiesta `GET` successiva che restituirà le informazioni sulle persone nell'immagine.  Impostare il valore `modules` su `All` per ottenere tutte le informazioni dettagliate tranne `RecognizedEntities` nei risultati di `POST` senza eseguire un'altra chiamata usando il parametro `insightsToken`.


**Endpoint 3:** restituisce immagini di tendenza in base alle richieste di ricerca di altri utenti. Le immagini sono suddivise in diverse categorie, ad esempio in base a eventi o persone importanti.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Per un elenco dei mercati che supportano le immagini di tendenza, vedere [Immagini di tendenza](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Per maggiori dettagli su intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere le informazioni di riferimento per l'[API Ricerca immagini Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
## <a name="response-json"></a>Risposta JSON
La risposta a una richiesta di ricerca di immagini include i risultati come oggetti JSON. Per esempi di analisi dei risultati, vedere l'[esercitazione](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) e il [codice sorgente](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Passaggi successivi
Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti della risposta JSON.  Tutti gli endpoint supportano query che restituiscono una posizione e/o una lingua specifica in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base eseguite con l'API Ricerca immagini, vedere le [guide introduttive per Ricerca immagini](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
