---
title: Invio e uso di query e risposte dell'API di ricerca di attività locali Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Usare questo articolo per informazioni su come inviare e usare query di ricerca con l'API di ricerca di attività locali Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/26/2018
ms.author: rosh; v-gedod
ms.openlocfilehash: e2911ebe9157507534717a4177d4380813dd2ff6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541564"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Invio e uso di query e risposte dell'API di ricerca di attività locali Bing

È possibile ottenere risultati locali dall'API di ricerca di attività locali Bing inviando una query di ricerca all'endpoint e includendo l'intestazione `Ocp-Apim-Subscription-Key`, che è obbligatoria. Insieme alle [intestazioni](local-search-reference.md#headers) e ai [parametri](local-search-reference.md#query-parameters) disponibili, le ricerche possono essere personalizzate specificando [confini geografici](specify-geographic-search.md) per l'area in cui cercare e le [categorie](local-search-query-response.md) delle località restituite.

## <a name="creating-a-request"></a>Creazione di una richiesta

Per inviare una richiesta all'API di ricerca di attività locali Bing, aggiungere un termine di ricerca al parametro `q=` prima di aggiungerlo all'endpoint API, includendo l'intestazione `Ocp-Apim-Subscription-Key`. Ad esempio:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Di seguito viene mostrata la sintassi dell'URL della richiesta completa. Per altre informazioni sull'invio di richieste, vedere le [guide introduttive](quickstarts/local-quickstart.md) sull'API di ricerca di attività locali Bing e fare riferimento al contenuto relativo a [intestazioni](local-search-reference.md#headers) e [parametri](local-search-reference.md#query-parameters). 

Per informazioni sulle categorie di ricerca locale, vedere [Categorie di ricerca per l'API di ricerca di attività locali Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Uso di risposte

Le risposte JSON dell'API di ricerca di attività locali Bing contengono un oggetto `SearchResponse`. L'API restituirà risultati della ricerca pertinenti nel campo `places`. Se non vengono trovati risultati, il campo `places` non verrà incluso nella risposta.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Attributi dei risultati della ricerca

I risultati JSON restituiti dall'API includono gli attributi seguenti:

* _type
* Address
* entityPresentationInfo
* area geografica
* id
* name
* routeablePoint
* telephone
* url

Per informazioni generali su intestazioni, parametri, codici di mercato, oggetti di risposta, errori e altro ancora, vedere le informazioni di riferimento sull'[API di ricerca locale Bing v7](local-search-reference.md).

> [!NOTE]
> L'utente, o una terza parte per conto dell'utente, non può usare, conservare, archiviare, memorizzare nella cache, condividere o distribuire alcun dato dall'API di ricerca locale per scopi di test, sviluppo, training o distribuzione di alcun servizio o funzionalità non Microsoft né per rendere disponibili servizi o funzionalità non Microsoft. 


## <a name="example-json-response"></a>Risposta JSON di esempio

La risposta JSON seguente include i risultati della ricerca specificati dalla query `?q=restaurant+in+Bellevue`.

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Passaggi successivi
- [Local Business Search quickstart](quickstarts/local-quickstart.md) (Guida introduttiva alla ricerca di attività locali)
- [Local Business Search Java quickstart](quickstarts/local-search-java-quickstart.md) (Guida introduttiva alla ricerca di attività locali in Java)
- [Guida introduttiva a Local Business Search in Node](quickstarts/local-search-node-quickstart.md)
- [Guida introduttiva a Local Business Search in Python](quickstarts/local-search-python-quickstart.md)
