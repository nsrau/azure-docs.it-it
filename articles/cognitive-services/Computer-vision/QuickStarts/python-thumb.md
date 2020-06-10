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
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 762688aaf21f88ef1404bc09be32cf4129bfd0db
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608843"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Guida introduttiva: Generare un'anteprima usando l'API REST Visione artificiale e Python

In questa guida di avvio rapido si genererà un'anteprima da un'immagine usando l'API REST Visione artificiale. Con il metodo [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c) (Ottieni miniature) è possibile specificare l'altezza e la larghezza desiderate e Visione artificiale userà il ritaglio intelligente per identificare in modo intelligente l'area di interesse e generare le coordinate di ritaglio in base a tale area.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/try/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario avere una chiave di sottoscrizione per Visione artificiale. È possibile ottenere una chiave della versione di valutazione gratuita nella pagina [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Oppure seguire le istruzioni riportate in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere Visione artificiale e ottenere la chiave. Quindi, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per la stringa dell'endpoint di servizio, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.
- Un editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Creare ed eseguire l'esempio

Per creare ed eseguire l'esempio, copiare questo codice nell'editor di codice. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v3.0/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

Eseguire quindi queste operazioni:

1. (Facoltativo) Sostituire il valore di `image_url` con l'URL della propria immagine.
1. Salvare il codice in un file con estensione `.py`. Ad esempio: `get-thumbnail.py`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Esaminare i risultati

Viene restituita una risposta positiva sotto forma di dati binari, come rappresentazione dei dati dell'immagine per l'anteprima. L'esempio mostrerà questa immagine. Se la richiesta ha esito negativo, la risposta verrà visualizzata nella finestra del prompt dei comandi e conterrà un codice di errore.

## <a name="run-in-jupyter-optional"></a>Eseguire in Jupyter (facoltativo)

È possibile seguire questa guida introduttiva passo per passo usando un notebook di Jupyter in [MyBinder](https://mybinder.org). Per avviare Binder, selezionare il pulsante seguente:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Passaggi successivi

Esplorare ora un'applicazione Python che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime con ritaglio intelligente e rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive nelle immagini.

> [!div class="nextstepaction"]
> [Esercitazione per Python dell'API Visione artificiale](../Tutorials/PythonTutorial.md)

* Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).
