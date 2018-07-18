---
title: Chiamata e risposta - Avvio rapido di Node.js per Servizi cognitivi di Azure, API Ricerca Web Bing | Microsoft Docs
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare l'API Ricerca Web Bing nei Servizi cognitivi Microsoft in Azure.
services: cognitive-services
documentationcenter: ''
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: a47dfaa48acb5b4a8ffc9b9f8da98f42e7729399
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374105"
---
# <a name="call-and-response-your-first-bing-web-search-query-for-nodejs"></a>Chiamata e risposta: la prima query di Ricerca Web Bing per Node.js

L'API Ricerca Web Bing offre un'esperienza simile a Bing.com/Search restituendo i risultati di ricerca che Bing ritiene rilevanti per la query dell'utente. I risultati possono includere pagine Web, immagini, video, notizie e entità, oltre alle query di ricerca correlate, le correzioni ortografiche, i fusi orari, le unità di conversione, traduzioni e calcoli. I tipi di risultati che si ottengono sono basati sulla pertinenza e il livello delle API di ricerca Bing a cui si effettua la sottoscrizione.

Questo articolo include una semplice applicazione console che esegue una query dell'API Ricerca Web Bing e visualizza i risultati della ricerca non elaborati restituiti, che sono in formato JSON. L'applicazione è scritta in JavaScript e si esegue in Node.js, ma l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione che sia in grado di effettuare richieste HTTP e analizzare una stringa JSON. 

## <a name="prerequisites"></a>prerequisiti

Per eseguire questo codice è necessario [Node.js 6](https://nodejs.org/en/download/).

È necessario avere un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con le **API di ricerca Bing**. Per questo Avvio rapido è sufficiente la [versione di prova gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="running-the-application"></a>Esecuzione dell'applicazione

Per eseguire l'applicazione seguire questa procedura.

1. Creare un nuovo progetto Node.js nell'IDE o nell'editor preferito.
2. Aggiungere il codice fornito.
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
// the endpoint for your Bing Web search instance in your Azure dashboard.
let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/search';

let term = 'Microsoft Cognitive Services';

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

let bing_web_search = function (search) {
  console.log('Searching the Web for: ' + term);
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

if (subscriptionKey.length === 32) {
    bing_web_search(term);
} else {
    console.log('Invalid Bing Search API subscription key!');
    console.log('Please paste yours into the source code.');
}

```

## <a name="json-response"></a>Risposta JSON

Segue una risposta di esempio. Per limitare la lunghezza del file JSON, è mostrato solo un singolo risultato e altre parti della risposta sono state troncate. 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face API",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face API from Microsoft Azure. ... Cognitive Services; Face API;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca Web Bing](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche  

[Panoramica di Ricerca Web Bing](../overview.md)  
[Prova](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Ottenere una chiave di accesso gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)  
[Informazioni di riferimento per l'API Ricerca Web Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
