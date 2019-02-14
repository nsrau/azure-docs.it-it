---
title: 'Guida introduttiva: Cercare immagini - SDK di Ricerca immagini Bing per Python'
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per effettuare la prima ricerca di immagini tramite l'SDK di Ricerca immagini Bing, che consiste in un wrapper per l'API e contiene le stesse funzionalità. Questa semplice applicazione Python invia una query di ricerca immagine, analizza la risposta JSON e visualizza l'URL della prima immagine restituita.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: a785cb461b12c20bd5e322a55a91c8b49cb40d54
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858162"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Guida introduttiva: Cercare immagini con l’SDK di Ricerca immagini Bing per Python

Usare questa guida introduttiva per effettuare la prima ricerca di immagini tramite l'SDK di Ricerca immagini Bing, che consiste in un wrapper per l'API e contiene le stesse funzionalità. Questa semplice applicazione Python invia una query di ricerca immagine, analizza la risposta JSON e visualizza l'URL della prima immagine restituita.

Il codice sorgente per questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) con annotazioni aggiuntive e altre informazioni sulla gestione degli errori.

## <a name="prerequisites"></a>Prerequisiti
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in **Ricerca**.  Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

* [Python 2.7 o 3.4](https://www.python.org/) e versioni successive.

* [Azure Image Search SDK](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) per Python
    * Installare usando `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo script Python nell'ambiente di sviluppo integrato o nell'editor preferito ed eseguire le importazioni seguenti:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Creare variabili per la chiave di sottoscrizione e per il termine di ricerca.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Creare il client di Ricerca immagini

3. Creare un'istanza di `CognitiveServicesCredentials` e usarla per creare un'istanza del client:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
4. Inviare una query di ricerca all'API Ricerca immagini Bing:
    ```python
    image_results = client.images.search(query=search_term)
    ```
## <a name="process-and-view-the-results"></a>Elaborare e visualizzare i risultati

Analizzare le immagini restituite nella risposta.


Se la risposta contiene i risultati della ricerca, archiviare il primo risultato e stamparne i dettagli, ad esempio l'URL di anteprima e l'URL originale, insieme al numero totale di immagini restituite.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Vedere anche 

* [Informazioni su Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Provare una demo interattiva online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ottenere gratuitamente una chiave di accesso per Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Esempi Python per SDK di Servizi cognitivi di Azure](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Documentazione di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Informazioni di riferimento per l'API Ricerca immagini Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
