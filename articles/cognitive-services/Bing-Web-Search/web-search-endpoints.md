---
title: Endpoint di Ricerca Web
titleSuffix: Azure Cognitive Services
description: Per ottenere i risultati della ricerca Web, inviare una `GET` richiesta all'endpoint seguente. Le intestazioni e i parametri URL definiscono ulteriori specifiche.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381253"
---
# <a name="web-search-endpoint"></a>Endpoint di Ricerca Web

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

L' **API Ricerca Web** restituisce pagine Web, notizie, immagini, video ed [entità](../bing-entities-search/overview.md). Le entità contengono informazioni di riepilogo su una persona, un luogo o un argomento.

## <a name="endpoint"></a>Endpoint

Per ottenere risultati della ricerca Web tramite l'API Bing, inviare una richiesta `GET` all'endpoint seguente. Le intestazioni e i parametri URL definiscono ulteriori specifiche.

**Endpoint:** restituisce risultati Web attinenti alla query di ricerca dell'utente definita da `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Endpoint: per i dettagli relativi a intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere [Web Search API v7 reference](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) (Informazioni di riferimento sull'API Ricerca Web versione 7).

## <a name="response-json"></a>Risposta JSON

I risultati inclusi nella risposta a una richiesta di ricerca Web sono tutti costituiti da oggetti JSON. L'analisi dei risultati richiede procedure che gestiscano gli elementi di ogni tipo. Per alcuni esempi, vedere l'[esercitazione](./tutorial-bing-web-search-single-page-app.md) e il [codice sorgente](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search).

## <a name="next-steps"></a>Passaggi successivi

Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti della risposta JSON.  Tutti gli endpoint supportano query che restituiscono una lingua e una posizione specifiche in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base eseguite con l'API Ricerca Web, vedere le [guide introduttive per la ricerca Web](./overview.md).