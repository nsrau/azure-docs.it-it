---
title: "Guida introduttiva: Ottenere informazioni dettagliate sulle immagini con l'API REST e Node.js - Ricerca visiva Bing"
titleSuffix: Azure Cognitive Services
description: Informazioni su come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.openlocfilehash: 7dfb3adb5d7bf5b005beb7e7b75fb339d456cd15
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872592"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Guida introduttiva: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e Node.js

Usare questa guida di avvio rapido per effettuare la prima chiamata all'API Ricerca visiva Bing. Questa semplice applicazione JavaScript carica un'immagine nell'API e visualizza le informazioni restituite. Anche se l'applicazione è scritta in JavaScript, l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/en/download/)
* Modulo di richiesta per JavaScript. È possibile usare il comando `npm install request` per installare il modulo.
* Modulo form-data. È possibile usare il comando `npm install form-data` per installare il modulo. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inizializzare l'applicazione

1. Creare un file JavaScript nell'ambiente di sviluppo integrato o nell'editor preferito e impostare i requisiti seguenti:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Creare variabili per l'endpoint dell'API, la chiave di sottoscrizione e il percorso dell'immagine. Per il valore di `baseUri`, è possibile usare l'endpoint globale nel codice seguente o l'endpoint del [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Creare una funzione denominata `requestCallback()` per visualizzare la risposta dell'API.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Costruire e inviare la richiesta di ricerca

1. Quando si carica un'immagine locale, i dati del modulo devono includere l'intestazione `Content-Disposition`. Impostare il parametro `name` su "image" e il parametro `filename` sul nome file dell'immagine. Il contenuto del modulo include i dati binari dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB.

   ```
   --boundary_1234-abcd
   Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

   ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

   --boundary_1234-abcd--
   ```

2. Creare un nuovo oggetto `FormData` con `FormData()` e accodare il percorso dell'immagine usando `fs.createReadStream()`.
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

3. Usare la libreria di richieste per caricare l'immagine e chiamare `requestCallback()` per stampare la risposta. Aggiungere la chiave di sottoscrizione all'intestazione della richiesta.

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola con Ricerca visiva](../tutorial-bing-visual-search-single-page-app.md)
