---
title: Avvio rapido di Node.js per Servizi cognitivi di Azure, API Ricerca notizie Bing | Microsoft Docs
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare l'API Ricerca notizie Bing in Servizi cognitivi Microsoft in Azure.
services: cognitive-services
documentationcenter: ''
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 1c68e75319a34f4ac9726c047fc7d6d0269634ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377372"
---
# <a name="quickstart-for-bing-news-search-api-with-nodejs"></a>Avvio rapido per l'API Ricerca notizie Bing con Node.js

Questo articolo spiega come usare l'API Ricerca notizie Bing, inclusa in Servizi cognitivi Microsoft in Azure. Questo articolo riguarda Node.js, ma l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione che sia in grado di effettuare richieste HTTP e analizzare una stringa JSON. 

L'esempio è scritto in JavaScript ed eseguito in Node.js 6.

Per i dettagli tecnici sulle API, vedere le [informazioni di riferimento sulle API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

## <a name="prerequisites"></a>prerequisiti

È necessario avere un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con le **API di ricerca Bing**. Per questo Avvio rapido è sufficiente la [versione di prova gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="bing-news-search"></a>Ricerca notizie Bing

L'[API Ricerca notizie Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) restituisce i risultati delle notizie dal motore di ricerca Bing.

1. Creare un nuovo progetto Node.js nell'IDE o nell'editor preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore `subscriptionKey` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```javascript
'use strict';

let https = require('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'enter key here';

// Verify the endpoint URI.  At this writing, only one endpoint is used for Bing
// search APIs.  In the future, regional endpoints may be available.  If you
// encounter unexpected authorization errors, double-check this host against
// the endpoint for your Bing Search instance in your Azure dashboard.
let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/news/search';

let term = 'Microsoft';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        console.log('\nRelevant Headers:\n');
        for (var header in response.headers)
            // header keys are lower-cased by Node.js
            if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                 console.log(header + ": " + response.headers[header]);
        body = JSON.stringify(JSON.parse(body), null, '  ');
        console.log('\nJSON Response:\n');
        console.log(body);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let bing_news_search = function (search) {
  console.log('Searching news for: ' + term);
  let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}
bing_news_search(term);
```

**Risposta**

Una risposta con esito positivo viene restituita in JSON, come illustrato nell'esempio seguente: 

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .
      
      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Paging di notizie ](paging-news.md)
> [Uso degli indicatori di effetto per evidenziare il testo](hit-highlighting.md)
> [Ricerca di notizie sul Web](search-the-web.md)  
> [Prova](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)

