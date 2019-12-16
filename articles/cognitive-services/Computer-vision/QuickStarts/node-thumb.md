---
title: "Guida introduttiva: Generare un'anteprima - REST, Node.js"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva, si genererà l'anteprima di un'immagine usando l'API Visione artificiale con Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ac97ae1d3eb260a9e0c70d3f831efb8b56a5c29d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973816"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-nodejs"></a>Guida introduttiva: Generare un'anteprima usando l'API REST Visione artificiale e Node.js

Questa guida di avvio rapido mostra come generare un'anteprima da un'immagine usando l'API REST di Visione artificiale. Il metodo [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) consente di generare l'anteprima di un'immagine. Si specificano l'altezza e la larghezza, che possono essere diverse rispetto alle proporzioni dell'immagine di input. Visione artificiale ricorre al ritaglio intelligente per identificare l'area di interesse in modo intelligente e generare le coordinate di ritaglio in base a tale area.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario aver installato [Node.js](https://nodejs.org) 4.x o versione successiva.
- È necessario aver installato [npm](https://www.npmjs.com/).
- È necessario avere una chiave di sottoscrizione per Visione artificiale. È possibile ottenere una chiave della versione di valutazione gratuita nella pagina [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Oppure seguire le istruzioni riportate in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere Visione artificiale e ottenere la chiave. Quindi, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per la stringa dell'endpoint di servizio, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample"></a>Creare ed eseguire l'esempio

Per creare ed eseguire l'esempio, seguire questa procedura:

1. Installare il pacchetto [`request`](https://www.npmjs.com/package/request) npm.
   1. Aprire una finestra del prompt dei comandi come amministratore.
   1. Eseguire il comando seguente:

      ```console
      npm install request
      ```

   1. Dopo l'installazione del pacchetto, chiudere la finestra del prompt dei comandi.

1. Copiare il codice seguente in un editor di testo.
1. Facoltativamente, sostituire il valore di `imageUrl` con l'URL di un'altra immagine da analizzare.
1. Salvare il codice in un file con estensione `.js`. Ad esempio: `get-thumbnail.js`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `node` per eseguire il file. Ad esempio: `node get-thumbnail.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.1/generateThumbnail';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
});
```

## <a name="examine-the-response"></a>Esaminare i risultati

Una risposta positiva è costituita dai dati binari che rappresentano i dati dell'immagine per l'anteprima. Se la richiesta ha esito negativo, la risposta viene visualizzata nella finestra della console. La risposta a una richiesta con esito negativo contiene un codice di errore e un messaggio utile per determinarne la causa.

## <a name="next-steps"></a>Passaggi successivi

Esaminare l'API Visione artificiale usata per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato. Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esplorare l'API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
