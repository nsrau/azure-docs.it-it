---
title: "Guida introduttiva: Controllare l'ortografia con l'API REST e Node.js - Controllo ortografico Bing"
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come usare l'API REST Controllo ortografico Bing e Node.js per eseguire il controllo ortografico e grammaticale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 1f207a93af22029e4bfdc20e5f84ed8ef3dee37c
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366962"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Guida introduttiva: Controllare l'ortografia con l'API REST Controllo ortografico Bing e Node.js

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Usare questa guida introduttiva per effettuare la prima chiamata all'API REST Controllo ortografico Bing. Questa semplice applicazione JavaScript invia una richiesta all'API e restituisce un elenco di correzioni suggerite. 

Anche se l'applicazione è scritta in JavaScript, l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Prerequisiti

* [Node.js 6](https://nodejs.org/en/download/) o versione successiva

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto

1. Creare un nuovo file JavaScript nell'ambiente IDE o nell'editor preferito. Impostare la severità e l'uso obbligatorio di `https`. Creare quindi le variabili per l'host e il percorso dell'endpoint API e per la chiave di sottoscrizione. È possibile usare l'endpoint globale nel codice seguente o l'endpoint del [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Creare le variabili per i parametri di ricerca e il testo da controllare: 

   1. Assegnare il codice di mercato al parametro `mkt` con l'operatore `=`. Il codice di mercato è il codice del paese o dell'area geografica da cui si effettua la richiesta. 

   1. Aggiungere il parametro `mode` con l'operatore `&` e quindi assegnare la modalità di controllo ortografico. La modalità può essere `proof` (individua la maggior parte degli errori di ortografia/grammatica) o `spell` (individua la maggior parte degli errori di ortografia, ma meno errori di grammatica).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Creare i parametri della richiesta

Creare i parametri della richiesta creando un nuovo oggetto con un metodo `POST`. Aggiungere il percorso accodando il percorso dell'endpoint e la stringa di query. Aggiungere quindi la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

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

Creare una funzione denominata `response_handler` per recuperare la risposta JSON dall'API e visualizzarla. Creare una variabile per il corpo della risposta. Aggiungere la risposta quando viene ricevuto un flag `data` usando `response.on()`. Dopo la ricezione di un flag `end`, visualizzare il corpo JSON nella console.

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

Chiamare l'API usando `https.request()` con i parametri della richiesta e il gestore della risposta. Scrivere il testo nell'API e quindi terminare la richiesta.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Eseguire l'applicazione

1. Compilare ed eseguire il progetto.

1. Se si usa la riga di comando, usare il comando seguente per creare ed eseguire l'applicazione:

   ```bash
   node <FILE_NAME>.js
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
> [Creare app Web a pagina singola](../tutorials/spellcheck.md)

- [Informazioni sull'API Controllo ortografico Bing](../overview.md)
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)