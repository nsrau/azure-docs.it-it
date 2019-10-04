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
ms.openlocfilehash: f699a41e566d4080b77b538d03804b1969291678
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141256"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Guida introduttiva: Generare un'anteprima usando l'API REST Visione artificiale e Python

In questa guida introduttiva si genererà l'anteprima di un'immagine usando l'API REST di Visione artificiale. Con il metodo [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Ottieni miniature) è possibile specificare l'altezza e la larghezza desiderate e Visione artificiale userà il ritaglio intelligente per identificare in modo intelligente l'area di interesse e generare le coordinate di ritaglio in base a tale area.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/try/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario avere una chiave di sottoscrizione per Visione artificiale. È possibile ottenere una chiave della versione di valutazione gratuita nella pagina [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Oppure seguire le istruzioni riportate in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere Visione artificiale e ottenere la chiave. Quindi, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per la stringa dell'endpoint di servizio, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.
- Un editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Creare ed eseguire l'esempio

Per creare ed eseguire l'esempio, copiare questo codice nell'editor di codice. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v2.0/generateThumbnail"

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
