---
title: Endpoint di Ricerca entità Bing
titlesuffix: Azure Cognitive Services
description: Riepilogo degli endpoint dell'API Ricerca entità.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: aahi
ms.openlocfilehash: 561c64db2b97ed8792acab6cc87de861ecc30fe9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183986"
---
# <a name="entity-search-endpoints"></a>Endpoint di Ricerca entità
L'**API Ricerca entità** include un endpoint.

## <a name="endpoint"></a>Endpoint
Per richiedere i risultati della ricerca di entità, è consigliabile inviare una richiesta all'endpoint seguente. Usare le intestazioni e i parametri URL per definire ulteriori specifiche.

Endpoint `GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Sono necessari i seguenti parametri URL:
- mkt. Mercato dal quale provengono i risultati. 
- q. Query di ricerca identità.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guide introduttive alla Ricerca entità Bing](quickstarts/csharp.md)

## <a name="see-also"></a>Vedere anche  

[Panoramica di Ricerca entità Bing](search-the-web.md )
[Informazioni di riferimento sulle API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
