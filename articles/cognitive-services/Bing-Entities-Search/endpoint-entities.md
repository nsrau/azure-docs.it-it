---
title: Endpoint di Ricerca entità Bing
titlesuffix: Azure Cognitive Services
description: Riepilogo degli endpoint dell'API Ricerca entità Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: d781a4b3cd0119f5624b4dd20b514894ea339414
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816583"
---
# <a name="entity-search-endpoint"></a>Endpoint di Ricerca entità
L'**API Ricerca entità** include un endpoint che restituisce le entità dal Web in base a una query.

## <a name="endpoint"></a>Endpoint
Per ottenere i risultati dell'entità tramite l'**API Bing**, inviare una richiesta `GET` all'endpoint seguente. Usare le intestazioni e i parametri URL per definire ulteriori specifiche.

**Endpoint:** restituisce entità attinenti alla query di ricerca dell'utente definita da `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Per maggiori dettagli su intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere il riferimento per l'[API Ricerca entità Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference).

## <a name="response-json"></a>Risposta JSON
La risposta a una richiesta di ricerca entità include i risultati come oggetti JSON. Per gli esempi dei risultati, vedere [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>Passaggi successivi
Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti della risposta JSON.  Tutti gli endpoint supportano query che restituiscono una posizione e/o una lingua specifica in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per gli esempi che usano l'API Ricerca entità, vedere [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) e [Ridimensionamento e ritaglio delle immagini di anteprima](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).