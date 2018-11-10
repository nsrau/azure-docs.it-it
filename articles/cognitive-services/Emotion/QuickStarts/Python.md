---
title: "Guida introduttiva: Riconoscere le emozioni espresse dai visi in un'immagine - API Emozioni, Python"
description: Ottenere informazioni ed esempi di codice per iniziare a usare rapidamente l'API Emozioni con Python.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 02/05/2018
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: f0bcc88b60e0a9b93856aa32a10b9c0ad898ce95
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240699"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Guida introduttiva: Creare un'app per il riconoscimento delle emozioni espresse dai visi in un'immagine.

> [!IMPORTANT]
> L'API Emozioni verrà deprecata il 15 febbraio 2019. La funzionalità di riconoscimento delle emozioni è ora generalmente disponibile nell'ambito dell'[API Viso](https://docs.microsoft.com/azure/cognitive-services/face/). 

Questa procedura dettagliata contiene informazioni ed esempi di codice per iniziare rapidamente a usare il [metodo di riconoscimento dell'API Emozioni](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) con Python per riconoscere le emozioni espresse da una o più persone in un'immagine.

È possibile eseguire questo esempio come notebook Jupyter in [MyBinder](https://mybinder.org) facendo clic sulla notifica di avvio di Binder: [![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>Prerequisito
Per ottenere la chiave di sottoscrizione gratuita fare clic [qui](https://azure.microsoft.com/try/cognitive-services/)

## <a name="running-the-walkthrough"></a>Eseguire la procedura dettagliata
Per continuare con questa procedura dettagliata, sostituire `subscription_key` con la chiave API ottenuta in precedenza.


```python
subscription_key = None
assert subscription_key
```

Verificare quindi che l'URL del servizio corrisponda all'area usata durante la configurazione della chiave API. Se si usa una chiave di prova, non occorre apportare alcuna modifica.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

Questa procedura dettagliata usa le immagini archiviate su disco. È anche possibile usare immagini disponibili tramite un URL accessibile pubblicamente. Per altre informazioni, vedere la [documentazione dell'API Rest](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Poiché i dati delle immagini vengono passati come parte del corpo della richiesta, è necessario impostare l'intestazione `Content-Type` su `application/octet-stream`. Se si passa un'immagine tramite un URL, ricordare di impostare l'intestazione su:
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
creare un dizionario contenente l'URL:
```python
data = {'url': image_url}
```
e passarlo alla libreria `requests` usando:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Scaricare prima alcune immagini di esempio dal sito dell'[API Emozioni](https://azure.microsoft.com/services/cognitive-services/emotion/).


```bash
%%bash
mkdir -p images
curl -Ls https://aka.ms/csnb-emotion-1 -o images/emotion_1.jpg
curl -Ls https://aka.ms/csnb-emotion-2 -o images/emotion_2.jpg
```


```python
image_path = "images/emotion_1.jpg"
image_data = open(image_path, "rb").read()
```


```python
import requests
headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
response.raise_for_status()
analysis = response.json()
analysis
```




    [{'faceRectangle': {'height': 162, 'left': 130, 'top': 141, 'width': 162},
      'scores': {'anger': 9.29041e-06,
       'contempt': 0.000118981574,
       'disgust': 3.15619363e-05,
       'fear': 0.000589638,
       'happiness': 0.06630674,
       'neutral': 0.00555004273,
       'sadness': 7.44669524e-06,
       'surprise': 0.9273863}}]



L'oggetto JSON restituito contiene i rettangoli di selezione dei visi riconosciuti con le emozioni rilevate. Ogni emozione è associata a un punteggio compreso tra 0 e 1 dove un punteggio più alto indica un'emozione con maggiore precisione di uno più basso.

Le righe di codice seguenti illustrano le emozioni rilevate sui visi nell'immagine usando la libreria `matplotlib`. Per evitare confusione, vengono visualizzate solo le prime tre emozioni.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from io import BytesIO
from PIL import Image

plt.figure(figsize=(5,5))

image  = Image.open(BytesIO(image_data))
ax     = plt.imshow(image, alpha=0.6)

for face in analysis:
    fr = face["faceRectangle"]
    em = face["scores"]
    origin = (fr["left"], fr["top"])
    p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
    plt.text(origin[0], origin[1], ct, fontsize=20)    
_ = plt.axis("off")
```

La funzione `annotate_image` indicata di seguito può essere usata per sovrapporre le emozioni a un file di immagine in base al relativo percorso nel file system. Si basa sul codice per chiamare l'API Emozioni illustrata in precedenza.


```python
def annotate_image(image_path):    
    image_data = open(image_path, "rb").read()
    headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
    response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
    response.raise_for_status()
    analysis = response.json()

    plt.figure()

    image  = Image.open(image_path)
    ax     = plt.imshow(image, alpha=0.6)

    for face in analysis:
        fr = face["faceRectangle"]
        em = face["scores"]
        origin = (fr["left"], fr["top"])
        p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
        ax.axes.add_patch(p)
        ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
        plt.text(origin[0], origin[1], ct, fontsize=20, va="bottom")    
    _ = plt.axis("off")
```

La funzione `annotate_image` può infine essere chiamata su un file di immagine, come illustrato nella riga seguente:


```python
annotate_image("images/emotion_2.jpg")
```
