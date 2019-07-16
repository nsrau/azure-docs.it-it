---
title: "Guida introduttiva: Generare un'anteprima - REST, Python"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva verrà generata l'anteprima di un'immagine usando l'API Visione artificiale con Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5203467eadadf1180eabf7b5a51b113ae273ee7c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603548"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Guida introduttiva: Generare un'anteprima usando l'API REST Visione artificiale e Python

In questa guida introduttiva si genererà l'anteprima di un'immagine usando l'API REST di Visione artificiale. Con il metodo [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Ottieni miniature) è possibile specificare l'altezza e la larghezza desiderate e Visione artificiale userà il ritaglio intelligente per identificare in modo intelligente l'area di interesse e generare le coordinate di ritaglio in base a tale area.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/try/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario avere una chiave di sottoscrizione per Visione artificiale. È possibile ottenere una chiave della versione di valutazione gratuita nella pagina [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Oppure seguire le istruzioni riportate in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere Visione artificiale e ottenere la chiave.
- Un editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="create-and-run-the-sample"></a>Creare ed eseguire l'esempio

Per creare ed eseguire l'esempio, copiare questo codice nell'editor di codice. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
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
# Free trial subscription keys are generated in the "westcentralus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Eseguire quindi queste operazioni:
1. Sostituire il valore di `subscription_key` con la chiave di sottoscrizione.
1. Se necessario, sostituire il valore di `vision_base_url` con l'URL endpoint per la risorsa Visione artificiale nell'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
1. Facoltativamente, sostituire il valore di `image_url` con l'URL di un'altra immagine per la quale si vuole generare un'anteprima.
1. Salvare il codice in un file con estensione `.py`. Ad esempio: `get-thumbnail.py`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Esaminare i risultati

Viene restituita una risposta positiva sotto forma di dati binari, come rappresentazione dei dati dell'immagine per l'anteprima. L'esempio mostrerà questa immagine. Se la richiesta ha esito negativo, la risposta verrà visualizzata nella finestra del prompt dei comandi e conterrà un codice di errore.

## <a name="run-in-jupyter-optional"></a>Eseguire in Jupyter (facoltativo)

È possibile seguire questa guida introduttiva passo per passo usando un notebook di Jupyter in [MyBinder](https://mybinder.org). Per avviare Binder, selezionare il pulsante seguente:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Passaggi successivi

Vedere informazioni più dettagliate sulla funzionalità di generazione di anteprime.

> [!div class="nextstepaction"]
> [Generazione di anteprime](../concept-generating-thumbnails.md)
