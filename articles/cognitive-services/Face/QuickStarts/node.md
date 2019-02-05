---
title: "Guida introduttiva: Rilevare i visi in un'immagine con l'API REST di Azure e Node.js"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'API REST Viso di Azure con Node.js per rilevare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 3b0eefb7471061e7e3547898b2edf02ed0f94ccf
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210081"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>Guida introduttiva: Rilevare i visi in un'immagine con l'API REST Viso e Node.js

In questa guida introduttiva si userà l'API REST Viso di Azure con Node.js per rilevare i visi umani in un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione API Viso. È possibile ottenere una chiave di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api). In alternativa, seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione al servizio API Viso e ottenere la chiave.

## <a name="create-the-nodejs-script"></a>Creare lo script Node.js

Il codice seguente chiamerà l'API Viso e otterrà i dati dell'attributo viso da un'immagine. In primo luogo, copiare il codice in un editor di testo. È necessario apportare alcune modifiche prima di poterlo eseguire.

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase = 'https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg';

// Request parameters.
const params = {
    'returnFaceId': 'true',
    'returnFaceLandmarks': 'false',
    'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,' +
        'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
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
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

### <a name="subscription-key"></a>Chiave della sottoscrizione
Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione Viso valida.

### <a name="face-endpoint-url"></a>URL endpoint Viso

L'URL `https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect` indica l'endpoint Viso di Azure su cui eseguire la query. Può essere necessario modificare la prima parte dell'URL con l'area che corrisponde alla chiave di sottoscrizione (vedere la [documentazione dell'API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) per un elenco degli endpoint di tutte le aree).

### <a name="url-query-string"></a>Stringa di query URL

Il campo `returnFaceAttributes` specifica gli attributi del viso da recuperare. È possibile modificare questa stringa in base all'uso previsto.

### <a name="image-source-url"></a>URL origine immagine
Il campo `imageUrl` indica l'immagine da usare come input. È possibile modificarlo in modo che punti a un'immagine che si vuole analizzare.

## <a name="save-and-run-the-script"></a>Salvare ed eseguire lo script

Dopo aver apportato le modifiche, salvare il file come script JavaScript (.js). Quindi, aprire un prompt dei comandi ed eseguirlo con il comando `node`.

```
node myfile.js
```

Le informazioni sul viso dovrebbero essere visualizzate come dati JSON nella finestra della console. Ad esempio: 

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato scritto un comando cURL che chiama l'API Viso di Azure per rilevare i visi in un'immagine e restituire i relativi attributi. Successivamente, esplorare la documentazione di riferimento dell'API Viso per altre informazioni.

> [!div class="nextstepaction"]
> [API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
