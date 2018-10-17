---
title: 'Guida introduttiva: Usare un modello di dominio - REST, Python - Visione artificiale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si identificheranno celebrità e luoghi di interesse in un'immagine in base a modelli di dominio usando l'API Visione artificiale con Python.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 93027e2f9cd3a9b0e9c6ef261b8af876022632a4
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632450"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>Guida introduttiva: Usare un modello di dominio usando l'API REST e Python in Visione artificiale

In questa guida introduttiva si identificheranno luoghi di interesse o celebrità in un'immagine archiviata in remoto in base a un modello di dominio usando l'API REST di Visione artificiale. Con il metodo [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200), è possibile applicare un modello specifico di un dominio per riconoscere contenuti all'interno di un'immagine.

È possibile seguire passo passo la procedura descritta in questa guida introduttiva usando un notebook di Jupyter in [MyBinder](https://mybinder.org). Per avviare Binder, selezionare il pulsante seguente:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario installare [Python](https://www.python.org/downloads/) se si vuole eseguire l'esempio in locale.
- È necessario avere una chiave di sottoscrizione per Visione artificiale. Per ottenere una chiave di sottoscrizione, vedere la sezione [Come ottenere chiavi di sottoscrizione](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-landmarks-sample"></a>Creare ed eseguire i luoghi di interesse di esempio

Per creare ed eseguire il luogo di interesse di esempio, seguire questa procedura:

1. Copiare il codice seguente in un editor di testo.
1. Apportare le modifiche seguenti al codice, dove necessario:
    1. Sostituire il valore di `subscription_key` con la chiave di sottoscrizione.
    1. Se necessario, sostituire il valore di `vision_base_url` con l'URL endpoint per la risorsa Visione artificiale nell'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
    1. Facoltativamente, sostituire il valore di `image_url` con l'URL di un'altra immagine in cui si vuole identificare i luoghi di interesse.
1. Salvare il codice in un file con estensione `.py`. Ad esempio: `get-landmarks.py`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python get-landmarks.py`.

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

landmark_analyze_url = vision_base_url + "models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'model': 'landmarks'}
data    = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-landmarks-sample"></a>Analizzare il risultato per i luoghi di interesse di esempio

Una risposta con esito positivo viene restituita in JSON. La pagina Web di esempio analizza e visualizza una risposta con esito positivo nella finestra del prompt dei comandi, come nell'esempio seguente:

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="create-and-run-the-celebrities-sample"></a>Creare ed eseguire le celebrità di esempio

Per creare ed eseguire il luogo di interesse di esempio, seguire questa procedura:

1. Copiare il codice seguente in un editor di testo.
1. Apportare le modifiche seguenti al codice, dove necessario:
    1. Sostituire il valore di `subscription_key` con la chiave di sottoscrizione.
    1. Se necessario, sostituire il valore di `vision_base_url` con l'URL endpoint per la risorsa Visione artificiale nell'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
    1. Facoltativamente, sostituire il valore di `image_url` con l'URL di un'altra immagine in cui si vuole identificare le celebrità.
1. Salvare il codice in un file con estensione `.py`. Ad esempio: `get-celebrities.py`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python get-celebrities.py`.

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

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'model': 'celebrities'}
data    = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-celebrities-sample"></a>Analizzare il risultato per le celebrità di esempio

Una risposta con esito positivo viene restituita in JSON. La pagina Web di esempio analizza e visualizza una risposta con esito positivo nella finestra del prompt dei comandi, come nell'esempio seguente:


```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare i file relativi a entrambi gli esempi.

## <a name="next-steps"></a>Passaggi successivi

Esaminare un'applicazione Python che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime ritagliate in modo intelligente, nonché rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive, inclusi i visi, di un'immagine. Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esercitazione per Python dell'API Visione artificiale](../Tutorials/PythonTutorial.md)
