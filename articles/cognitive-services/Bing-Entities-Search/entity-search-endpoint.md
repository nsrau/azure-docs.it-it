---
title: Endpoint dell'API Ricerca entità Bing
titleSuffix: Azure Cognitive Services
description: Informazioni sull'endpoint dell'API Ricerca entità Bing e su come inviare richieste a tale endpoint.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9d08091d0ea6869d13e294e60454f85a84f672ad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424046"
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
