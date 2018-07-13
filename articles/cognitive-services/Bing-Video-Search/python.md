---
title: Guida introduttiva a Python per Servizi cognitivi di Azure, API Ricerca video Bing | Microsoft Docs
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare l'API Ricerca video Bing in Servizi cognitivi Microsoft in Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: ce4356f05e69540bc3bc3241e2ec1751ff7a7276
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374017"
---
# <a name="quickstart-for-bing-video-search-api-with-python"></a>Guida introduttiva per l'API Ricerca video Bing con Python

Questa procedura dettagliata spiega come usare l'API Ricerca video Bing inclusa in Servizi cognitivi Microsoft in Azure. Per i dettagli tecnici sulle API, vedere le [informazioni di riferimento sulle API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).

È possibile eseguire questo esempio come notebook Jupyter in [MyBinder](https://mybinder.org) facendo clic sulla notifica di avvio di Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Prerequisiti
È necessario avere un [account delle API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con le **API di ricerca Bing**. Per questa guida introduttiva è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="running-the-walkthrough"></a>Esecuzione della procedura dettagliata

Impostare prima di tutto `subscription_key` sulla chiave API per il servizio API Bing.


```python
subscription_key = None
assert subscription_key
```

Verificare quindi che l'endpoint `search_url` sia corretto. Al momento della pubblicazione di questo documento, per le API di ricerca Bing viene usato un solo endpoint. Se si verificano errori di autorizzazione, controllare di nuovo questo valore rispetto l'endpoint di ricerca Bing nel dashboard di Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Impostare `search_term` per la ricerca di video con gattini


```python
search_term = "kittens"
```

Il blocco seguente usa la libreria `requests` di Python per chiamare le API di ricerca Bing e restituire i risultati come oggetto JSON. Osservare che la chiave API viene passata tramite il dizionario `headers` e il termine di ricerca tramite il dizionario `params`. Per visualizzare l'intero elenco di opzioni che è possibile usare per filtrare i risultati della ricerca, vedere la documentazione dell'[API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

L'oggetto `search_results` contiene i video rilevanti insieme a metadati dettagliati. Per visualizzare uno dei video, usare la relativa proprietà `embedHtml` e inserirla in un oggetto `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Paging dei video](paging-videos.md)
> [Ridimensionamento e ritaglio delle immagini di anteprima](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Vedere anche  

 [Ricerca di video sul Web](search-the-web.md) [Prova](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
