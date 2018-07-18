---
title: Endpoint di Suggerimenti automatici Bing | Microsoft Docs
description: Riepilogo dell'endpoint dell'API Suggerimenti automatici.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 5aaddd09006cb6f1812bb6ae213a2f5e6720fb1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376884"
---
# <a name="autosuggest-endpoint"></a>Endpoint di Suggerimenti automatici

L'**API Suggerimenti automatici** include un endpoint che restituisce un elenco di query suggerite da un termine di ricerca parziale.

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

Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al loro tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti risposta JSON.
Tutti gli endpoint supportano query che restituiscono una posizione e/o una lingua specifica in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base con l'API Suggerimenti automatici, vedere [Guide introduttive a Suggerimenti automatici](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).