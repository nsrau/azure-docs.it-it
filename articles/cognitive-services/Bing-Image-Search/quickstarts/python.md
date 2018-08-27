---
title: "Guida introduttiva: inviare query di ricerca con l'API REST per l'API ricerca immagini Bing in Python"
description: In questa guida introduttiva, è possibile inviare le query di ricerca all'API di ricerca Bing per ottenere un elenco di immagini pertinenti usando Python.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929936"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Guida introduttiva: inviare query di ricerca con l'API REST e Python

L'API Ricerca immagini Bing offre un'esperienza simile a quella di Bing.com/Images. Consente infatti di inviare una query di ricerca utente a Bing e di ottenere un elenco di immagini pertinenti.

Questa procedura dettagliata illustra un esempio semplice di chiamata dell'API Ricerca immagini Bing e la post-elaborazione dell'oggetto JSON risultante. Per altre informazioni, vedere la [documentazione di Ricerca immagini Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

È possibile eseguire questo esempio come Jupyter Notebook in [MyBinder](https://mybinder.org) facendo clic sulla notifica di avvio del Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>Esecuzione della procedura dettagliata
Per continuare con la procedura dettagliata, impostare `subscription_key` sulla chiave API per il servizio API Bing API.


```python
subscription_key = None
assert subscription_key
```

Verificare quindi che l'endpoint `search_url` sia corretto. Al momento della pubblicazione di questo documento, per le API di ricerca Bing viene usato un solo endpoint. Se si verificano errori di autorizzazione, verificare di nuovo questo valore nell'endpoint di ricerca Bing nel dashboard di Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Impostare `search_term` per ricercare immagini di cuccioli.


```python
search_term = "puppies"
```

Il blocco seguente usa la libreria `requests` di Python per chiamare le API di ricerca Bing e restituire i risultati come oggetto JSON. Osservare che la chiave API viene passata attraverso il dizionario `headers` e il termine di ricerca attraverso il dizionario `params`. Per visualizzare l'intero elenco di opzioni che è possibile usare per filtrare i risultati della ricerca, vedere la documentazione dell'[API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

L'oggetto `search_results` contiene le immagini effettive insieme ai metadati avanzati, quali gli elementi correlati. Ad esempio, la riga di codice seguente consente di estrarre gli URL di anteprima per i primi 16 risultati.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Quindi, è possibile usare la libreria `PIL` per scaricare le immagini di anteprima e la libreria `matplotlib` per eseguirne il rendering in una griglia $4 \times 4$.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca immagini Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche  

[Panoramica di Ricerca immagini Bing](../overview.md)  
[Prova](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Ottenere una chiave di accesso gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Informazioni di riferimento per l'API Ricerca immagini Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
