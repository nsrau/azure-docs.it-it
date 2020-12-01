---
title: Invio di richieste di ricerca all'API Ricerca entità Bing
titleSuffix: Azure cognitive Services
description: L'API Ricerca entità Bing invia una query di ricerca a Bing e ottiene risultati che includono entità e località.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: ad6d89fec9f2c94129e19c09ee3e1e76d5bb6e44
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353273"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Invio di richieste di ricerca all'API Ricerca entità Bing

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

L'API Ricerca entità Bing invia una query di ricerca a Bing e ottiene risultati che includono entità e località. I risultati relativi alle località includono ristoranti, hotel o altre aziende locali. Per le località, la query può specificare il nome dell'azienda locale o richiedere un elenco (ad esempio, ristoranti nelle vicinanze). I risultati relativi all'entità includono persone, località o cose. In questo contesto, per località si intendono attrazioni turistiche, stati, paesi/aree geografiche e così via.

## <a name="the-endpoint"></a>L'endpoint

Per ottenere entità e località nei risultati della ricerca, inviare una richiesta GET all'endpoint seguente:  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Le richieste devono usare il protocollo HTTPS.

È consigliabile che tutte le richieste abbiano origine da un server. La distribuzione della chiave come parte di un'applicazione client consente a terze parti dannose di accedervi più facilmente. L'esecuzione delle chiamate da un server fornisce anche un singolo punto di aggiornamento per le future versioni dell'API.

## <a name="specifying-query-parameters-and-headers"></a>Specificare parametri di query e intestazioni

La richiesta deve specificare il parametro di query [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query), contenente il termine di ricerca dell'utente. La richiesta deve specificare anche il parametro di query [mkt](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt), che identifica il mercato da cui devono provenire i risultati. Per un elenco di parametri di query facoltativi, vedere [parametri di query](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters). Eseguire la codifica URL di tutti i parametri della query.  
  
La richiesta deve specificare l'intestazione [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey). Nonostante sia facoltativo, è consigliabile specificare anche le intestazioni seguenti:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-ricerca-percorso](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

Le intestazioni relative a IP client e posizione sono importanti per la restituzione di contenuto con riconoscimento della posizione.  

Per un elenco di tutte le intestazioni di richiesta e risposta, vedere [Headers](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) (Intestazioni).

## <a name="the-request"></a>Richiesta.

Di seguito è illustrata una richiesta di entità che include tutte le intestazioni e i parametri di query suggeriti. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Se è la prima volta che si chiama un'API Bing, non includere l'intestazione dell'ID client. Includere l'ID client solo se in precedenza è già stata chiamata un'API Bing e Bing ha restituito un ID client per la combinazione utente e dispositivo.

## <a name="the-response"></a>Risposta

Di seguito è riportata la risposta alla richiesta precedente. L'esempio mostra anche le intestazioni di risposta specifiche di Bing. Per informazioni sull'oggetto della risposta, vedere [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse).

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>Passaggi successivi

* [Ricerca di entità con l'API Ricerca entità Bing](search-for-entities.md)
* [Requisiti per l'uso e la visualizzazione dell'API Bing](../../bing-web-search/use-display-requirements.md)