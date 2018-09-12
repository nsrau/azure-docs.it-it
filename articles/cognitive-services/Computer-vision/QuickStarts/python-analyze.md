---
title: API Visione artificiale - Guida introduttiva all'analisi di un'immagine remota con Python | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: In questa guida introduttiva si analizza un'immagine remota usando Visione artificiale con Python in Servizi cognitivi.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 65f9b0d4fb007a6a9b8ef489ca59f384e047a0dd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772338"
---
# <a name="quickstart-analyze-a-remote-image---rest-python"></a>Guida introduttiva: Analizzare un'immagine remota - REST, Python

In questa guida introduttiva si analizza un'immagine remota usando Visione artificiale. Per analizzare un'immagine locale, vedere [Analizzare un'immagine locale con Python](python-disk.md).

È possibile seguire passo passo la procedura descritta in questa guida introduttiva usando un notebook di Jupyter in [MyBinder](https://mybinder.org). Per avviare Binder, selezionare il pulsante seguente:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Prerequisiti

Per usare Visione artificiale, è necessario avere una chiave di sottoscrizione. A tale scopo, vedere [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Come ottenere chiavi di sottoscrizione).

## <a name="analyze-a-remote-image"></a>Analizzare un'immagine remota

Con il [metodo Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), è possibile estrarre caratteristiche visive in base al contenuto di un'immagine. È possibile caricare un'immagine o specificare l'URL di un'immagine e scegliere le caratteristiche da restituire, tra cui:

* Un elenco dettagliato di tag correlati al contenuto dell'immagine.
* Una frase completa di descrizione del contenuto dell'immagine.
* Le coordinate, il sesso e l'età di tutti i visi presenti nell'immagine.
* Il tipo di immagine (ClipArt o disegno di linee).
* Il colore principale, il colore dominante o l'indicazione se un'immagine è in bianco e nero.
* La categoria definita in questa [tassonomia](../Category-Taxonomy.md).
* L'indicazione se l'immagine presenta contenuto per adulti o con riferimenti sessuali.

Per eseguire l'esempio, seguire questa procedura:

1. Copiare il codice seguente in un nuovo file di script Python.
1. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
1. Modificare il valore di `vision_base_url` impostando l'indirizzo in cui si sono ottenute le chiavi di sottoscrizione, se necessario.
1. Facoltativamente, modificare il valore di `image_url` impostando un'altra immagine.
1. Eseguire lo script.

Il codice seguente usa la libreria `requests` di Python per chiamare il metodo Analyze Image dell'API Visione artificiale. I risultati vengono restituiti come oggetto JSON. La chiave API viene passata tramite il dizionario `headers`. I tipi di caratteristiche da riconoscere vengono passati tramite il dizionario `params`.

## <a name="analyze-image-request"></a>Richiesta di analisi dell'immagine

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
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
print(analysis)
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
```

## <a name="analyze-image-response"></a>Riposta alla richiesta di analisi dell'immagine

Viene restituita una risposta con esito positivo in formato JSON, ad esempio:

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

## <a name="next-steps"></a>Passaggi successivi

Esaminare un'applicazione Python che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime ritagliate in modo intelligente, nonché rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive, inclusi i visi, di un'immagine. Per sperimentare rapidamente le API Visione artificiale, provare la [console di test delle API aperta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esercitazione per Python dell'API Visione artificiale](../Tutorials/PythonTutorial.md)
