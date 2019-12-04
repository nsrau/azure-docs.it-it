---
title: "Guida introduttiva: Controllare l'ortografia con l'API REST e Node.js - Controllo ortografico Bing"
titleSuffix: Azure Cognitive Services
description: Introduzione all'uso dell'API REST Controllo ortografico Bing per controllare l'ortografia e la grammatica.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: ab8f1d52b5a0b9f5f2539de0acc4728277f9f7b2
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378830"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Guida introduttiva: Controllare l'ortografia con l'API REST Controllo ortografico Bing e Node.js

Usare questa guida introduttiva per effettuare la prima chiamata all'API REST Controllo ortografico Bing. Questa semplice applicazione Node invia una richiesta all'API e restituisce un elenco di parole non riconosciute, seguite dalle correzioni suggerite. L'applicazione è scritta in Node.js, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Prerequisiti

* [Node.js 6](https://nodejs.org/en/download/) o versione successiva

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto

1. Creare un nuovo file JavaScript nell'ambiente IDE o nell'editor preferito. Impostare la severità e l'uso obbligatorio di `https`. Creare quindi variabili per l'host, il percorso e la chiave di sottoscrizione dell'endpoint API.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Creare variabili per i parametri di ricerca e il testo da controllare. Aggiungere il codice di mercato dopo `mkt=`. Il codice di mercato è il paese da cui si effettua la richiesta. Aggiungere anche la modalità di controllo ortografico dopo `&mode=`. La modalità è `proof` (individua la maggior parte degli errori di ortografia/grammatica) o `spell` (individua la maggior parte degli errori di ortografia, ma meno errori di grammatica).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Creare i parametri della richiesta

Creare i parametri della richiesta creando un nuovo oggetto con un metodo `POST`. Aggiungere il percorso accodando il percorso dell'endpoint e la stringa di query. Aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Creare un gestore per la risposta

Creare una funzione denominata `response_handler` per recuperare la risposta JSON dall'API e visualizzarla. Creare una variabile per il corpo della risposta. Aggiungere la risposta quando si riceve un flag `data` usando `response.on()`. Quando si riceve un flag `end`, visualizzare il corpo JSON nella console.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>Inviare la richiesta

Chiamare l'API usando `https.request()` con i parametri della richiesta e il gestore della risposta. Scrivere il testo nell'API e terminare quindi la richiesta.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

## <a name="example-json-response"></a>Risposta JSON di esempio

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](../tutorials/spellcheck.md)

- [Informazioni sull'API Controllo ortografico Bing](../overview.md)
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
