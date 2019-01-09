---
title: "Guida introduttiva: Analizzare un'immagine remota - REST, Python"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si analizza un'immagine remota usando l'API Visione artificiale con Python.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f60a3c3c2ab586bdf1eb44cf1e81a9adb87584b0
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583937"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-python-in-computer-vision"></a>Guida introduttiva: Analizzare un'immagine remota usando l'API REST e Python in Visione artificiale

In questa guida introduttiva si analizza un'immagine archiviata in remoto per estrarre le caratteristiche visive usando l'API REST di Visione artificiale. Con il metodo [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) è possibile estrarre caratteristiche visive in base al contenuto di un'immagine.

È possibile seguire passo passo la procedura descritta in questa guida introduttiva usando un notebook di Jupyter in [MyBinder](https://mybinder.org). Per avviare Binder, selezionare il pulsante seguente:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario installare [Python](https://www.python.org/downloads/) se si vuole eseguire l'esempio in locale.
- È necessario avere una chiave di sottoscrizione per Visione artificiale. Per ottenere una chiave di sottoscrizione, vedere la sezione [Come ottenere chiavi di sottoscrizione](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Creare ed eseguire l'esempio

Per creare ed eseguire l'esempio, seguire questa procedura:

1. Copiare il codice seguente in un editor di testo.
1. Apportare le modifiche seguenti al codice, dove necessario:
    1. Sostituire il valore di `subscription_key` con la chiave di sottoscrizione.
    1. Se necessario, sostituire il valore di `vision_base_url` con l'URL endpoint per la risorsa Visione artificiale nell'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
    1. Sostituire il valore di `image_url` con l'URL di un'immagine diversa da analizzare (facoltativo).
1. Salvare il codice in un file con estensione `.py`. Ad esempio: `analyze-image.py`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python analyze-image.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
import json
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

analyze_url = vision_base_url + "analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/" + \
    "Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key }
params  = {'visualFeatures': 'Categories,Description,Color'}
data    = {'url': image_url}
response = requests.post(analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(json.dumps(response.json()))
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
plt.show()
```

## <a name="examine-the-response"></a>Esaminare i risultati

Una risposta con esito positivo viene restituita in JSON. La pagina Web di esempio analizza e visualizza una risposta con esito positivo nella finestra del prompt dei comandi, come nell'esempio seguente:

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il file.

## <a name="next-steps"></a>Passaggi successivi

Esaminare un'applicazione Python che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime ritagliate in modo intelligente, nonché rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive, inclusi i visi, di un'immagine. Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esercitazione per Python dell'API Visione artificiale](../Tutorials/PythonTutorial.md)
