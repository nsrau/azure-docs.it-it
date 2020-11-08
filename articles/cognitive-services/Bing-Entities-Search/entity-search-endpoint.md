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
ms.openlocfilehash: 1d9b7c79919569830834915fc609b849e717dce8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365840"
---
# <a name="bing-entity-search-api-endpoint"></a>Endpoint dell'API Ricerca entità Bing

> [!WARNING]
> API di ricerca Bing passano da servizi cognitivi a Ricerca Bing Services. A partire dal **30 ottobre 2020** , le nuove istanze di ricerca Bing devono essere sottoposte a provisioning in base al processo documentato [qui](https://aka.ms/cogsvcs/bingmove).
> API di ricerca Bing provisioning con servizi cognitivi sarà supportato per i prossimi tre anni o fino alla fine del Enterprise Agreement, a seconda di quale evento si verifichi per primo.
> Per istruzioni sulla migrazione, vedere [ricerca Bing Services](https://aka.ms/cogsvcs/bingmigration).


L'API Ricerca entità Bing include un endpoint che restituisce le entità dal Web in base a una query. I risultati della ricerca vengono restituiti in formato JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Ottenere risultati dell'entità dall'endpoint

Per ottenere i risultati dell'entità tramite l' **API Bing** , inviare una richiesta `GET` all'endpoint seguente. Usare [intestazioni](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) e [parametri di query](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) per personalizzare la richiesta di ricerca. Le richieste di ricerca possono essere inviate usando il parametro `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'API Ricerca entità Bing](overview.md)

## <a name="see-also"></a>Vedere anche 

Per altre informazioni su intestazioni, parametri, codici di mercato, oggetti risposta, errori e altro ancora, vedere l'articolo di riferimento [API Ricerca entità Bing v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).