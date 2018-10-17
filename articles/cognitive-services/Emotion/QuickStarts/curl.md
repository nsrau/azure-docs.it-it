---
title: "Guida introduttiva: Riconoscere le emozioni espresse dai visi in un'immagine - API Emozioni, cURL"
titlesuffix: Azure Cognitive Services
description: Ottenere informazioni ed esempi di codice per iniziare a usare rapidamente l'API Emozioni con cURL.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: dfdaa89c9d29e419539f385f601dc7f264bf838e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237076"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Guida introduttiva: Creare un'app per il riconoscimento delle emozioni espresse dai visi in un'immagine.

> [!IMPORTANT]
> L'API Emozioni verrà deprecata il 15 febbraio 2019. La funzionalità di riconoscimento delle emozioni è ora generalmente disponibile nell'ambito dell'[API Viso](https://docs.microsoft.com/azure/cognitive-services/face/).

Questo articolo contiene informazioni ed esempi di codice per iniziare rapidamente a usare il [metodo di riconoscimento dell'API Emozioni](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) con cURL per riconoscere le emozioni espresse da una o più persone in un'immagine.

## <a name="prerequisite"></a>Prerequisito
* Per ottenere la chiave di sottoscrizione gratuita fare clic [qui](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-curl-example-request"></a>Richiesta di esempio di riconoscimento emozioni con cURL

> [!NOTE]
> Usare lo stesso percorso nella chiamata REST usto per ottenere le chiavi di sottoscrizione. Ad esempio, se la chiave di sottoscrizione è stata ottenuta da westcentralus, sostituire "westus" nell'URL riportato di seguito con "westcentralus".

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}"
```

## <a name="recognize-emotions-sample-response"></a>Risposta di esempio delle emozioni riconosciute
Una chiamata completata con successo restituisce una matrice di voci relative ai visi con associati punteggi relativi alle emozioni, ordinate in base alle dimensioni del rettangolo del viso in ordine decrescente. Una risposta vuota indica che non sono stati rilevati visi. Una voce relativa alle emozioni include i campi seguenti:
* faceRectangle: posizione del rettangolo del viso nell'immagine.
* scores: punteggi delle emozioni per ogni viso nell'immagine.

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
