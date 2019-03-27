---
title: "Avvio rapido: Inviare una richiesta di ricerca all'API REST Ricerca entità Bing con Node.js"
titlesuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per inviare una richiesta all'API REST Ricerca entità Bing con C# e ricevere una risposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 8007d576a6b896f12423087cfd4a483d9171abc5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104370"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Avvio rapido: Inviare una richiesta di ricerca all'API REST Ricerca entità Bing con Node.js

Usare questa guida introduttiva per eseguire la prima chiamata all'API Ricerca entità Bing e visualizzare la risposta JSON. Questa semplice applicazione JavaScript invia una query di ricerca notizie all'API e visualizza la risposta. Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

L'applicazione è scritta in JavaScript, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

* La versione più recente di [Node.js](https://nodejs.org/en/download/).

* [Libreria di richieste JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file JavaScript nell'ambiente di sviluppo integrato o nell'editor preferito e impostare la severità e i requisiti https.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Creare variabili per l'endpoint dell'API, la chiave di sottoscrizione e la query di ricerca.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Aggiungere i parametri di query e del mercato a una stringa denominata `query`. Creare la versione codificata con URL della query con `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Gestire e analizzare la risposta

1. Definire una funzione denominata `response_handler` che accetta una chiamata HTTP, `response`, come parametro. In questa funzione eseguire i passaggi seguenti:

    1. Definire una variabile in cui sarà contenuto il corpo della risposta JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Memorizzare il corpo della risposta quando viene chiamato il flag **data**
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Quando viene segnalato un flag **end**, analizzare il codice JSON e stamparlo.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Inviare una richiesta

1. Creare una funzione denominata `Search` per inviare una richiesta di ricerca. In essa, eseguire la procedura seguente.

   1. Creare un oggetto JSON contenente i parametri della richiesta: usare `Get` per il metodo e aggiungere le informazioni su host e percorso. Aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`. 
   2. Usare `https.request()` per inviare la richiesta con il gestore di risposta creato in precedenza e i parametri di ricerca.
    
      ```javascript
      let Search = function () {
       let request_params = {
           method : 'GET',
           hostname : host,
           path : path + query,
           headers : {
               'Ocp-Apim-Subscription-Key' : subscriptionKey,
           }
       };
    
       let req = https.request (request_params, response_handler);
       req.end ();
      }
      ```

2. Chiamare la funzione `Search()`.

## <a name="example-json-response"></a>Risposta JSON di esempio

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](../tutorial-bing-entities-search-single-page-app.md)

* [Informazioni sull'API Ricerca entità Bing](../overview.md )
* [Informazioni di riferimento sull'API Ricerca entità Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
