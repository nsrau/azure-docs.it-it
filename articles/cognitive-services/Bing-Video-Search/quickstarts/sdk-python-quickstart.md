---
title: "Guida introduttiva: Cercare video con l'SDK di Ricerca video Bing per Python"
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per inviare richieste di ricerca video con l'SDK di Ricerca video Bing per Python
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ead69b0165831fef04e68b4c2eb7ea43115ca4ea
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867801"
---
#  <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Guida introduttiva: Eseguire una ricerca di video con l'SDK di Ricerca video Bing per Python

Usare questa guida introduttiva per iniziare a cercare notizie con l'SDK di Ricerca video Bing per Python. Mentre Ricerca video Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py). Contiene altre annotazioni e funzionalità.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Python](https://www.python.org/) 2.x o 3.x
- SDK di Ricerca video Bing per Python

È consigliabile usare un [ambiente virtuale](https://docs.python.org/3/tutorial/venv.html) Python. È possibile installare e inizializzare un ambiente virtuale con il [modulo venv](https://pypi.python.org/pypi/virtualenv). Installare virtualenv per Python 2.7 con:

```console
python -m venv mytestenv
```
   
Installare l'SDK di Ricerca video Bing con:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere le istruzioni di importazione seguenti. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Creare una variabile per la chiave di sottoscrizione. 
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>Creare il client di ricerca

Creare un'istanza di `CognitiveServicesCredentials` e creare un'istanza del client:

    ```python
    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-get-a-response"></a>Inviare una richiesta di ricerca e ottenere una risposta

1. Usare `client.videos.search()` con la query di ricerca per inviare una richiesta all'API Ricerca video Bing e ottenere una risposta.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Se la risposta contiene risultati della ricerca, recuperare il primo e stamparne l'ID, il nome e l'URL.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche  

* [Informazioni sull'API Ricerca video Bing](../overview.md)
* [Esempi di .NET SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)