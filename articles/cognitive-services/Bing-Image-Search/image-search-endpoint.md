---
title: Endpoint per l'API Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
description: L'API Ricerca immagini include tre endpoint. L'endpoint 1 restituisce le immagini dal Web. L'endpoint 2 restituisce ImageInsights. L'endpoint 3 restituisce immagini di tendenza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593519"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Endpoint per l'API Ricerca immagini Bing

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

L' **API Ricerca immagini** include tre endpoint.  L'endpoint 1 restituisce immagini dal Web in base a una query. L'endpoint 2 restituisce [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  L'endpoint 3 restituisce immagini di tendenza.

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
Una richiesta GET restituisce informazioni dettagliate su un'immagine, ad esempio le pagine Web che includono l'immagine. Includere il parametro [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) con una richiesta `GET`.

In alternativa, è possibile includere un'immagine binaria nel corpo di una richiesta `POST` e impostare il parametro [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) su `RecognizedEntities`. Viene restituito un parametro [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) da usare in una richiesta `GET` successiva che restituirà le informazioni sulle persone nell'immagine.  Impostare il valore `modules` su `All` per ottenere tutte le informazioni dettagliate tranne `RecognizedEntities` nei risultati di `POST` senza eseguire un'altra chiamata usando il parametro `insightsToken`.


**Endpoint 3:** restituisce immagini di tendenza in base alle richieste di ricerca di altri utenti. Le immagini sono suddivise in diverse categorie, ad esempio in base a eventi o persone importanti.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Per un elenco dei mercati che supportano le immagini di tendenza, vedere [Immagini di tendenza](./trending-images.md).

Per maggiori dettagli su intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere le informazioni di riferimento per l'[API Ricerca immagini Bing v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
## <a name="response-json"></a>Risposta JSON
La risposta a una richiesta di ricerca di immagini include i risultati come oggetti JSON. Per esempi di analisi dei risultati, vedere l'[esercitazione](./tutorial-bing-image-search-single-page-app.md) e il [codice sorgente](./tutorial-bing-image-search-single-page-app.md).

## <a name="next-steps"></a>Passaggi successivi
Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti della risposta JSON.  Tutti gli endpoint supportano query che restituiscono una posizione e/o una lingua specifica in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base eseguite con l'API Ricerca immagini, vedere le [guide introduttive per Ricerca immagini](./overview.md).