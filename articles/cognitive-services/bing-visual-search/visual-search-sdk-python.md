---
title: 'Guida introduttiva: SDK di Ricerca visiva Bing, Python'
titleSuffix: Azure Cognitive Services
description: Usare questo argomento di avvio rapido per iniziare a ottenere informazioni dettagliate sulle immagini dal servizio Ricerca visiva Bing con l'SDK per Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 4d5679e75f0feb4fd502abc92fc7c1de16b53bc8
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111577"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>Guida introduttiva: Ottenere informazioni dettagliate sulle immagini usando l'SDK di Ricerca visiva Bing per Python

Usare questo argomento di avvio rapido per iniziare a ottenere informazioni dettagliate sulle immagini dal servizio Ricerca visiva Bing con l'SDK per Python. Mentre Ricerca visiva Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK fornisce un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 

## <a name="prerequisites"></a>Prerequisiti

* [Python](https://www.python.org/) 2.x o 3.x
* È consigliabile usare un [ambiente virtuale](https://docs.python.org/3/tutorial/venv.html). Installare e inizializzare l'ambiente virtuale con il [modulo venv](https://pypi.python.org/pypi/virtualenv).
* SDK di Ricerca visiva Bing per Python. È possibile installarlo con i comandi seguenti:
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`



[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere le istruzioni di importazione seguenti. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Creare variabili per la chiave di sottoscrizione, l'ID di configurazione personalizzata e l'immagine che si vuole caricare. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Creare un'istanza del client

    ```python
    client = VisualSearchClient(endpoint="https://api.cognitive.microsoft.com", credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-the-search-request"></a>Inviare la richiesta di ricerca

1. Con il file di immagine aperto, serializzare `VisualSearchRequest()` e trasmetterlo come parametro `knowledge_request` per `visual_search()`.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Se vengono restituiti dei risultati, stamparli insieme ai tag e alle azioni nel primo tag.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](tutorial-bing-visual-search-single-page-app.md)
