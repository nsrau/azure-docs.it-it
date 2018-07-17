---
title: Guida introduttiva all'SDK di Ricerca immagini per Python | Microsoft Docs
description: Configurazione per l'applicazione console dell'SDK di Ricerca immagini.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: e30852439ad8ec2d5ddc667b75167e8b5d35be33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377892"
---
# <a name="image-search-sdk-python-quickstart"></a>Guida introduttiva all'SDK di Ricerca immagini per Python

L'SDK di Ricerca immagini Bing contiene la funzionalità dell'API REST per query Web e analisi dei risultati. 

Gli [esempi di codice origine per l'SDK di Ricerca immagini Bing per Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) sono disponibili su GitHub.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione
Se Python non è disponibile, installarlo. L'SDK è compatibile con Python 2.7, 3.3, 3.4, 3.5 e 3.6.

L'indicazione generale per lo sviluppo di Python è di usare un [ambiente virtuale](https://docs.python.org/3/tutorial/venv.html). Installare e inizializzare l'ambiente virtuale con il [modulo venv](https://pypi.python.org/pypi/virtualenv). È necessario installare virtualenv per Python 2.7.
```
python -m venv mytestenv
```
Installare le dipendenze dell'SDK di Ricerca immagini Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Client di Ricerca immagini
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in *Ricerca*. Aggiungere le importazioni:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Creare un'istanza di `CognitiveServicesCredentials` e creare un'istanza del client:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Cercare le immagini in base a una query (Yosemite) con filtro per GIF animate e proporzioni grandi. Verificare il numero di risultati e visualizzare insightsToken, URL di anteprima e URL Web del primo risultato.
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
Cercare le immagini per (Yosemite) con filtro per GIF animate e proporzioni grandi.  Verificare il numero di risultati.  Visualizzare `insightsToken`, `thumbnail url` e `web url` del primo risultato.
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

Ottenere risultati di tendenza:
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>Passaggi successivi

[Esempi di Python SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


