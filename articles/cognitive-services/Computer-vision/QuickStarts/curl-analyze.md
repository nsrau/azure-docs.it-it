---
title: "Guida introduttiva: Analizzare un'immagine remota - REST, cURL - Visione artificiale"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si analizzerà un'immagine remota usando l'API Visione artificiale con cURL.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: v-deken
ms.openlocfilehash: 51122da84e3845bba4919e4f2b2e46d513bbb3f4
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633487"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-curl-in-computer-vision"></a>Guida introduttiva: Analizzare un'immagine remota usando l'API REST e cURL in Visione artificiale

In questa guida introduttiva si analizzerà un'immagine archiviata in remoto per estrarre le caratteristiche visive usando l'API REST di Visione artificiale. Con il metodo [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) è possibile estrarre caratteristiche visive in base al contenuto di un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario avere [cURL](https://curl.haxx.se/windows).
- È necessario avere una chiave di sottoscrizione per Visione artificiale. Per ottenere una chiave di sottoscrizione, vedere la sezione [Come ottenere chiavi di sottoscrizione](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-command"></a>Creare ed eseguire il comando di esempio

Per creare ed eseguire l'esempio, seguire questa procedura:

1. Copiare il comando seguente in un editor di testo.
1. Apportare le modifiche seguenti al comando, dove necessario:
    1. Sostituire il valore di `<subscriptionKey>` con la chiave di sottoscrizione.
    1. Se necessario, sostituire l'URL della richiesta (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze`) con l'URL endpoint per il metodo [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) dall'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
    1. Facoltativamente, per usare un'altra lingua supportata, modificare il parametro di lingua dell'URL della richiesta (`language=en`).
    1. Facoltativamente, modificare l'URL dell'immagine nel corpo della richiesta (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) nell'URL di un'altra immagine da analizzare.
1. Aprire una finestra del prompt dei comandi.
1. Incollare il comando dall'editor di testo nella finestra del prompt dei comandi e quindi eseguire il comando.

```console
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="examine-the-response"></a>Esaminare i risultati

Una risposta con esito positivo viene restituita in JSON. L'applicazione di esempio analizza e visualizza una risposta con esito positivo nella finestra del prompt dei comandi, come nell'esempio seguente:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, chiudere la finestra del prompt dei comandi e l'editor di testo.

## <a name="next-steps"></a>Passaggi successivi

Esaminare l'API Visione artificiale usata per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato. Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esplorare l'API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
