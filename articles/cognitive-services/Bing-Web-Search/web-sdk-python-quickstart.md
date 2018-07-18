---
title: Guida introduttiva per Web Search SDK in Python | Microsoft Docs
description: Configurazione per l'applicazione console Web Search SDK.
titleSuffix: Azure Cognitive Services Web search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 2a5fed58be863b882b827dbed73862bc690bab1e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377956"
---
# <a name="web-search-sdk-python-quickstart"></a>Guida introduttiva per Web Search SDK in Python

Bing Web Search SDK contiene la funzionalità dell'API REST per query Web e analisi dei risultati. 

Il [codice sorgente per gli esempi di Bing Web Search SDK per Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/web_search_samples.py) è disponibile in Git Hub.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione
Se Python non è disponibile, installarlo. L'SDK è compatibile con Python 2.7, 3.3, 3.4, 3.5 e 3.6.

L'indicazione generale per lo sviluppo di Python è di usare un [ambiente virtuale](https://docs.python.org/3/tutorial/venv.html). Installare e inizializzare l'ambiente virtuale con il [modulo venv](https://pypi.python.org/pypi/virtualenv). È necessario installare virtualenv per Python 2.7.
```
python -m venv mytestenv
```
Installare le dipendenze di Bing Web Search SDK:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```
## <a name="web-search-client"></a>Client Ricerca Web
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in *Ricerca*. Aggiungere le importazioni e creare un'istanza di `CognitiveServicesCredentials`:
```
from azure.cognitiveservices.search.websearch import WebSearchAPI
from azure.cognitiveservices.search.websearch.models import SafeSearch
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Creare quindi un'istanza del client:
```
client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Cercare risultati e stampare la prima pagina Web ottenuta:
```
web_data = client.web.search(query="Yosemite")
print("\r\nSearched for Query# \" Yosemite \"")

# WebPages
if web_data.web_pages.value:

    print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

    first_web_page = web_data.web_pages.value[0]
    print("First web page name: {} ".format(first_web_page.name))
    print("First web page URL: {} ".format(first_web_page.url))

else:
    print("Didn't see any Web data..")
```
Stampare altri tipi di risultati: immagini, notizie e video:
```
# Images
if web_data.images.value:

    print("\r\nImage Results#{}".format(len(web_data.images.value)))

    first_image = web_data.images.value[0]
    print("First Image name: {} ".format(first_image.name))
    print("First Image URL: {} ".format(first_image.url))

else:
    print("Didn't see any Image..")
        
# News
if web_data.news.value:

    print("\r\nNews Results#{}".format(len(web_data.news.value)))

    first_news = web_data.news.value[0]
    print("First News name: {} ".format(first_news.name))
    print("First News URL: {} ".format(first_news.url))

else:
    print("Didn't see any News..")
            
# Videos
if web_data.videos.value:

    print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

    first_video = web_data.videos.value[0]
    print("First Videos name: {} ".format(first_video.name))
    print("First Videos URL: {} ".format(first_video.url))

else:
    print("Didn't see any Videos..")

```
Cercare (Best restaurants in Seattle), verificare il numero di risultati e stampare `name` e `URL` del primo risultato.
```
def web_results_with_count_and_offset(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
        print("\r\nSearched for Query# \" Best restaurants in Seattle \"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))```

```
Cercare "xbox" con `response_filter` assegnato a `News`.  Stampare i dettagli dei risultati relativi alle notizie.
```
def web_search_with_response_filter(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="xbox", response_filter=["News"])
        print("\r\nSearched for Query# \" xbox \" with response filters \"News\"")

        # News attribute since I filtered "News"
        if web_data.news.value:

            print("Webpage Results#{}".format(len(web_data.news.value)))

            first_web_page = web_data.news.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Cercare "Niagara Falls" usando i parametri `answerCount` e `promote`. Stampare i dettagli dei risultati.
```
def web_search_with_answer_count_promote_and_safe_search(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(
            query="Niagara Falls",
            answer_count=2,
            promote=["videos"],
            safe_search=SafeSearch.strict  # or directly "Strict"
        )
        print("\r\nSearched for Query# \" Niagara Falls\"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>Passaggi successivi

[Esempi di Python SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


