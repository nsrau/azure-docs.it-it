---
title: 'Guida introduttiva: Cercare immagini - API REST Ricerca immagini Bing e Python'
titleSuffix: Azure Cognitive Services
description: Usare questa guida di avvio rapido per inviare richieste di ricerca di immagini all'API REST Ricerca immagini Bing usando Python e ricevere risposte JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 08/26/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9f43b056275ba83630e711ff1a512cb73e84216a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034634"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Guida introduttiva: Cercare immagini con l'API REST Ricerca immagini Bing e Python

Usare questa guida introduttiva per iniziare a inviare richieste di ricerca all'API Ricerca immagini Bing. Questa applicazione Python invia una query di ricerca all'API e visualizza l'URL della prima immagine inclusa nei risultati. L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

È possibile eseguire questo esempio come Jupyter Notebook in [MyBinder](https://mybinder.org) facendo clic sulla notifica di avvio del Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Il codice sorgente per questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) con annotazioni aggiuntive e altre informazioni sulla gestione degli errori.


## <a name="prerequisites"></a>Prerequisiti

* [Python 2.x o 3.x](https://www.python.org/)
* [Python Imaging Library (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file Python nell'editor o nell'ambiente IDE preferito e importare i moduli seguenti. Creare una variabile per la chiave di sottoscrizione, l'endpoint del servizio di ricerca e un termine di ricerca.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Aggiungere la chiave della sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key` creando un dizionario e aggiungendo la chiave come valore. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Creare e inviare una richiesta di ricerca

1. Creare un dizionario per i parametri della richiesta di ricerca. Aggiungere il termine di ricerca al parametro `q`. Usare "public" per il parametro `license` per cercare immagini nel dominio pubblico. Usare "photo" per `imageType` per cercare solo foto.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Usare la libreria `requests` per chiamare l'API Ricerca immagini Bing. Aggiungere l'intestazione e i parametri alla richiesta e restituire la risposta come oggetto JSON. 

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

## <a name="view-the-response"></a>Visualizzare la risposta

1. Creare una nuova figura con quattro colonne e quattro righe usando la libreria matplotlib. 

2. Eseguire l'iterazione tra le righe e le colonne della figura e usare il metodo `Image.open()` della libreria PIL per aggiungere un'anteprima dell'immagine in ogni spazio. 

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    ```

3. Usare `plt.show()` per disegnare la figura e visualizzare le immagini.

## <a name="example-json-response"></a>Risposta JSON di esempio

Le risposte dell'API Ricerca immagini Bing vengono restituite in formato JSON. Questa risposta di esempio è stata troncata in modo da visualizzare un singolo risultato.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca immagini Bing](../tutorial-bing-image-search-single-page-app.md)

* [Che cos'è l'API Ricerca immagini Bing?](../overview.md)  
* [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) delle API Ricerca Bing. 
* [Ottenere gratuitamente una chiave di accesso per Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentazione di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Informazioni di riferimento per l'API Ricerca immagini Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
