---
title: "Guida introduttiva: Rilevare e incorniciare i visi in un'immagine con Python SDK"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva verrà creato un semplice script Python che usa l'API Viso per rilevare e incorniciare i visi in un'immagine remota.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: sbowles
ms.openlocfilehash: e8b16f7ebe918e5b8d59c6b57794c4f35a89b5f3
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684002"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>Guida introduttiva: Creare uno script Python per rilevare e incorniciare i visi in un'immagine

In questa guida introduttiva verrà creato un semplice script Python che usa l'API Viso di Azure, attraverso Python SDK, per rilevare visi umani in un'immagine remota. L'applicazione mostra un'immagine selezionata e traccia una cornice attorno a ogni viso rilevato.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione API Viso. È possibile ottenere una chiave di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api). In alternativa, seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione al servizio API Viso e ottenere la chiave.
- [Python 2.7 o 3.5 e versioni successive](https://www.python.org/downloads/)
- Strumento [pip](https://pip.pypa.io/en/stable/installing/)
- Python SDK dell'API Viso. È possibile installarlo eseguendo il comando seguente:
    ```bash
    pip install cognitive_face
    ```

## <a name="detect-faces-in-an-image"></a>Rilevare i visi in un'immagine

Creare un nuovo script Python, _FaceQuickstart.py_. Aggiungere il codice seguente. Questa è la funzionalità principale di rilevamento volto. Sarà necessario sostituire `<Subscription Key>` con il valore della chiave. È anche necessario modificare il valore di `BASE_URL` per usare l'identificatore di area corretto per la chiave. Le chiavi di sottoscrizione per la versione di valutazione gratuita vengono generate nell'area **westus**. Facoltativamente, impostare `img_url` sull'URL di qualsiasi immagine si voglia usare.

Lo script rileverà i visi chiamando il metodo **cognitive_face.face.detect**, che esegue il wrapping dell'API REST [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) e restituisce un elenco di visi.

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

### <a name="try-the-app"></a>Provare l'app

Eseguire l'app con il comando `python FaceQuickstart.py`. Si otterrà una risposta di testo nella finestra della console, simile alla seguente:

```shell
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

Si tratta di un elenco dei visi rilevati. Ogni voce dell'elenco è un'istanza **dict** dove `faceId` è un ID univoco per il viso rilevato e `faceRectangle` descrive la posizione del viso rilevato. 

> [!NOTE]
> Gli ID viso scadono dopo 24 ore. È necessario archiviare i dati del viso in modo esplicito per poterli conservare a lungo termine.

## <a name="draw-face-rectangles"></a>Disegnare rettangoli intorno ai visi

Usando le coordinate ricevute dal comando precedente, è possibile disegnare rettangoli sull'immagine per rappresentare visivamente ogni viso. Sarà necessario installare Pillow (`pip install pillow`) per usare il modulo immagine Pillow. All'inizio dello script *FaceQuickstart.py* aggiungere il codice seguente:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Quindi, nella parte inferiore dello script, aggiungere il codice seguente. Ciò consente di creare una semplice funzione per l'analisi delle coordinate del rettangolo e di usare Pillow per disegnare rettangoli sull'immagine originale. Quindi, mostra l'immagine nel visualizzatore immagine predefinito.

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name="run-the-app"></a>Esecuzione dell'app

Prima di tutto, potrebbe essere richiesto di selezionare un visualizzatore di immagini predefinito. Quindi, verrà visualizzata un'immagine simile alla seguente. Nella finestra della console dovrebbero essere visualizzati anche i dati del rettangolo.

![Una giovane donna con un rettangolo rosso tracciato attorno al viso](../images/face-rectangle-result.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso il processo di base per l'uso del Python SDK dell'API Viso ed è stato creato uno script per rilevare e incorniciare i visi in un'immagine. In seguito, si esaminerà l'uso di Python SDK in un esempio più complesso. Passare all'esempio Cognitive Face Python su GitHub, clonarlo nella propria cartella di progetto e seguire le istruzioni nel file _README.md_.

> [!div class="nextstepaction"]
> [Esempio Cognitive Face Python](https://github.com/Microsoft/Cognitive-Face-Python)