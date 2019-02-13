---
title: Endpoint dell'API Ricerca entità Bing
titlesuffix: Azure Cognitive Services
description: Informazioni sull'endpoint dell'API Ricerca entità Bing e su come inviare richieste a tale endpoint.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: cae65c8fb7deb9f68a297de2058a86249b60136b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753981"
---
# <a name="bing-entity-search-api-endpoint"></a>Endpoint dell'API Ricerca entità Bing


L'API Ricerca entità Bing include un endpoint che restituisce le entità dal Web in base a una query. I risultati della ricerca vengono restituiti in formato JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Ottenere risultati dell'entità dall'endpoint

Per ottenere i risultati dell'entità tramite l'**API Bing**, inviare una richiesta `GET` all'endpoint seguente. Usare [intestazioni](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) e [parametri di query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters) per personalizzare la richiesta di ricerca. Le richieste di ricerca possono essere inviate usando il parametro `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'API Ricerca entità Bing](overview.md)

## <a name="see-also"></a>Vedere anche  

Per altre informazioni su intestazioni, parametri, codici di mercato, oggetti risposta, errori e altro ancora, vedere l'articolo di riferimento [API Ricerca entità Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference).
