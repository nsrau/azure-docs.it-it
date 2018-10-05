---
title: Endpoint di Ricerca video - Ricerca video Bing
titlesuffix: Azure Cognitive Services
description: Riepilogo degli endpoint dell'API Ricerca video Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: rosh
ms.openlocfilehash: c153f577f76944d22f9a1b0fb4b24d332d2a02c8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220772"
---
# <a name="video-search-endpoints"></a>Endpoint di Ricerca video
L'**API Ricerca video** include tre endpoint.  L'endpoint 1 restituisce video dal Web in base a una query. L'endpoint 2 restituisce informazioni dettagliate su un video in base al parametro URL `modules`.  L'endpoint 3 restituisce immagini di tendenza.

## <a name="endpoints"></a>Endpoint
Per ottenere risultati video tramite l'API Bing, inviare una richiesta `GET` a uno degli endpoint seguenti. Usare le intestazioni e i parametri URL per definire ulteriori specifiche.

**Endpoint 1:** restituisce video attinenti alla query di ricerca dell'utente definita da `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Endpoint 2:** restituisce informazioni dettagliate su un video, ad esempio i video correlati. Includere il [parametro di query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters) `modules`, ovvero un elenco delimitato da virgole delle informazioni dettagliate da richiedere.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Endpoint 3:** restituisce video di tendenza in base alle richieste di ricerca di altri utenti. I video sono suddivisi in diverse categorie, ad esempio in base a eventi o persone importanti.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Per maggiori dettagli su intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere le informazioni di riferimento per l'[API Ricerca video Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).
## <a name="response-json"></a>Risposta JSON
La risposta a una richiesta di ricerca di video include i risultati come oggetti JSON. Per esempi di analisi dei risultati, vedere l'[esercitazione](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) e il [codice sorgente](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Passaggi successivi
Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti della risposta JSON.  Tutti gli endpoint supportano query che restituiscono una posizione e/o una lingua specifica in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base eseguite con l'API Ricerca video, vedere le [guide introduttive per Ricerca video](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).