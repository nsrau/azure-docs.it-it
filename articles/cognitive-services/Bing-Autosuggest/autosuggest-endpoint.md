---
title: Endpoint di Suggerimenti automatici Bing
titlesuffix: Azure Cognitive Services
description: Riepilogo dell'endpoint dell'API Suggerimenti automatici Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 03f7ac11b08d9cad633e207337ff963114f2c68f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149238"
---
# <a name="bing-autosuggest-endpoint"></a>Endpoint di Suggerimenti automatici Bing

L'**API Suggerimenti automatici Bing** include un endpoint che restituisce un elenco di query suggerite da un termine di ricerca parziale.

## <a name="endpoint"></a>Endpoint

Per ottenere query suggerite tramite l'API Bing, inviare una richiesta `GET` all'endpoint seguente. Usare le intestazioni e i parametri URL per definire altre specifiche.

**Endpoint:** restituisce suggerimenti per la ricerca come risultati JSON pertinenti per l'input dell'utente definito da `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Per maggiori dettagli su intestazioni, parametri, codici di mercato, oggetti risposta, errori e altro ancora, vedere il riferimento per l'[API Suggerimenti automatici Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference).

## <a name="response-json"></a>Risposta JSON

La risposta a una richiesta di suggerimenti automatici include i risultati come oggetti JSON. Per esempi di analisi dei risultati, vedere l'[esercitazione](tutorials/autosuggest.md) e il [codice sorgente](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Passaggi successivi

Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti della risposta JSON.
Tutti gli endpoint supportano query che restituiscono una posizione e/o una lingua specifica in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base con l'API Suggerimenti automatici, vedere [Guide introduttive a Suggerimenti automatici](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).
