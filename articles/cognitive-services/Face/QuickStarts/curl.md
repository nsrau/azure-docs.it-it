---
title: "Guida introduttiva: Rilevare i visi in un'immagine - API Viso, cURL"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'API Viso con cURL per rilevare i visi in un'immagine.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: 9ae8135481eb44e3b4b876fd4916e78a41c65042
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121905"
---
# <a name="quickstart-detect-faces-in-an-image-using-curl"></a>Guida introduttiva: rilevare i visi in un'immagine con cURL

In questa guida introduttiva si userà l'API Viso per rilevare i visi in un'immagine.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire l'esempio è necessaria una sottoscrizione. È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Rilevare i visi in un'immagine

Usare il metodo [Face - Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) per rilevare i visi in un'immagine e restituirne gli attributi, tra cui:

* ID viso: ID univoco usato in diversi scenari di API Viso.
* Rettangolo del viso: riquadro i cui lati indicano la posizione del viso nell'immagine.
* Punti di riferimento: matrice con 27 punti di riferimento che indicano le posizioni importanti dei componenti del viso.
* Altri attributi del viso quali età, sesso, intensità del sorriso, orientamento della testa e peli del viso.

Per eseguire l'esempio, seguire questa procedura:

1. Aprire il prompt dei comandi.
2. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
3. Se necessario, modificare l'URL (`https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect`) impostando l'indirizzo in cui si sono ottenute le chiavi di sottoscrizione.
4. Facoltativamente, modificare l'immagine (`"{\"url\":...`) da analizzare.
5. Incollare il codice nella finestra di comando.
6. Eseguire il comando.

### <a name="face---detect-request"></a>Richiesta di rilevamento del viso

> [!NOTE]
> Usare lo stesso percorso nella chiamata REST usto per ottenere le chiavi di sottoscrizione. Se, ad esempio, le chiavi di sottoscrizione sono state ottenute da westus, nell'URL riportato di seguito sostituire "westcentralus" con "westus".

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="face---detect-response"></a>Risposta alla richiesta di rilevamento del viso

Una risposta con esito positivo viene restituita in JSON.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare le API Viso usate per rilevare i visi umani in un'immagine, delimitare i visi con rettangoli e restituire gli attributi, ad esempio l'età e il sesso.

> [!div class="nextstepaction"]
> [API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
