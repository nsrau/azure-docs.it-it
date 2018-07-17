---
title: Guida introduttiva per l'API Ricerca entità | Microsoft Docs
description: Illustra come iniziare a usare l'API Ricerca entità Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: B206A254-B7E9-49FF-AFD5-87B1E4D6D30B
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: 12031d2447920c7e2d6180f35cf4fb29aa1b6150
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372929"
---
# <a name="making-your-first-entities-request"></a>Creazione della prima richiesta di identità

L'API Ricerca entità invia una query di ricerca a Bing e ottiene risultati che includono entità e località. I risultati relativi alle località includono ristoranti, hotel o altre aziende locali. Per le località, la query può specificare il nome dell'azienda locale o richiedere un elenco (ad esempio, ristoranti nelle vicinanze). I risultati relativi all'entità includono persone, località o cose. In questo contesto, per località si intendono attrazioni turistiche, stati, paesi e così via. 

## <a name="first-steps"></a>Primi passaggi

Prima di poter effettuare la prima chiamata, è necessario ottenere una chiave di sottoscrizione di Servizi cognitivi. Per ottenere una chiave, vedere [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api). (Se l'API Ricerca identità non è visibile nella parte superiore, fare clic sulla scheda **Ricerca** e scorrere verso il basso finché non viene visualizzata.)

## <a name="the-endpoint"></a>L'endpoint

Per ottenere entità e località nei risultati della ricerca, inviare una richiesta GET all'endpoint seguente:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

La richiesta deve usare il protocollo HTTPS.

È consigliabile che tutte le richieste abbiano origine da un server. La distribuzione della chiave come parte di un'applicazione client consente a terze parti dannose di accedervi più facilmente. L'esecuzione delle chiamate da un server fornisce anche un singolo punto di aggiornamento per le future versioni dell'API.

## <a name="specifying-query-parameters-and-headers"></a>Specificare parametri di query e intestazioni

La richiesta deve specificare il parametro di query [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query), contenente il termine di ricerca dell'utente. La richiesta deve specificare anche il parametro di query [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt), che identifica il mercato da cui devono provenire i risultati. Per un elenco di parametri di query facoltativi, vedere [Query Parameters](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters) (Parametri di query). Eseguire la codifica URL di tutti i parametri della query.  
  
La richiesta deve specificare l'intestazione [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey). Nonostante sia facoltativo, è consigliabile specificare anche le intestazioni seguenti:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Le intestazioni relative a IP client e posizione sono importanti per la restituzione di contenuto con riconoscimento della posizione.  

Per un elenco di tutte le intestazioni di richiesta e risposta, vedere [Headers](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) (Intestazioni).

## <a name="the-request"></a>Richiesta

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

Di seguito è riportata la risposta alla richiesta precedente. L'esempio mostra anche le intestazioni di risposta specifiche di Bing. Per informazioni sull'oggetto della risposta, vedere [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```
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

Provare l'API. Passare a [Entities Search API Testing Console](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/) (Console di test dell'API Ricerca entità). 

Per informazioni dettagliate sull'utilizzo degli oggetti della risposta, vedere [Searching the Web for entities and places](./search-the-web.md) (Ricerca di entità e località sul Web).

Per non violare nessuna delle regole relative all'uso dei risultati della ricerca, vedere [Bing Use and Display Requirements](./use-display-requirements.md) (Requisiti per l'uso e la visualizzazione di Bing).
