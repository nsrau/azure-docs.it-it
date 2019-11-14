---
title: Endpoint dell'API Ricerca entità Bing
titleSuffix: Azure Cognitive Services
description: L'API Ricerca entità Bing include un endpoint che restituisce le entità dal Web in base a una query. I risultati della ricerca vengono restituiti in formato JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072660"
---
# <a name="bing-entity-search-api-endpoint"></a>Endpoint dell'API Ricerca entità Bing


L'API Ricerca entità Bing include un endpoint che restituisce le entità dal Web in base a una query. I risultati della ricerca vengono restituiti in formato JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Ottenere risultati dell'entità dall'endpoint

Per ottenere i risultati dell'entità tramite l'**API Bing**, inviare una richiesta `GET` all'endpoint seguente. Usare [intestazioni](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) e [parametri di query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) per personalizzare la richiesta di ricerca. Le richieste di ricerca possono essere inviate usando il parametro `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'API Ricerca entità Bing](overview.md)

## <a name="see-also"></a>Vedere anche 

Per altre informazioni su intestazioni, parametri, codici di mercato, oggetti risposta, errori e altro ancora, vedere l'articolo di riferimento [API Ricerca entità Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
