---
title: Esercitazione per l'API Viso in Python | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Informazioni sull'uso dell'API Viso con Python SDK per rilevare visi umani in un'immagine in Servizi cognitivi.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 90d74d8df2ed59e6f3313ef7c620284d1022a667
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049112"
---
# <a name="getting-started-with-face-api-in-python-tutorial"></a>Esercitazione introduttiva per l'API Viso in Python

In questa esercitazione si apprenderà come richiamare l'API Viso tramite Python SDK per rilevare visi umani in un'immagine.

## <a name="prerequisites"></a> Prerequisiti

Per usare l'esercitazione è necessario eseguire le operazioni seguenti:

- Installare Python 2.7+ o Python 3.5+.
- Installare pip.
- Installare Python SDK per l'API Viso come indicato di seguito:

```bash
pip install cognitive_face
```

- Ottenere una [chiave di sottoscrizione](https://azure.microsoft.com/try/cognitive-services/) per Servizi cognitivi Microsoft. In questa esercitazione è possibile usare la chiave primaria o quella secondaria. Tenere presente che per usare qualsiasi API Viso è necessario avere una chiave di sottoscrizione valida.

## <a name="sdk-example"></a> Rilevare un viso in un'immagine

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

Di seguito è riportato un risultato di esempio. Si tratta di un elemento `list` di visi rilevati. Ogni voce dell'elenco è un'istanza `dict` dove `faceId` è un ID univoco per il viso rilevato e `faceRectangle` descrive la posizione del viso rilevato. Un ID viso scade dopo 24 ore.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Disegnare rettangoli attorno ai visi

Usando le coordinate JSON ricevute dal comando precedente, è possibile disegnare rettangoli sull'immagine per rappresentare visivamente ogni viso. È necessario eseguire l'operazione `pip install Pillow` per usare il modulo di immagini `PIL`.  All'inizio del file aggiungere quanto segue:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Quindi, dopo `print(faces)`, includere nello script quanto segue:

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

## <a name='further'></a> Esplorazione aggiuntiva

Se si vuole esplorare ulteriormente l'API Viso, questa esercitazione offre un esempio di interfaccia utente grafica. Per eseguirla, installare prima [wxPython](https://wxpython.org/pages/downloads/) e quindi eseguire il comando seguente.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a> Riepilogo

In questa esercitazione si è appreso il processo di base per l'uso dell'API Viso tramite la chiamata di Python SDK. Per altre informazioni sui dettagli dell'API, vedere le guide pratiche e le [informazioni di riferimento dell'API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Argomenti correlati

- [Getting Started with Face API in CSharp](FaceAPIinCSharpTutorial.md) (Introduzione all'API Viso in CSharp)
- [Getting Started with Face API in Java for Android](FaceAPIinJavaForAndroidTutorial.md) (Introduzione all'API Viso in Java per Android)
