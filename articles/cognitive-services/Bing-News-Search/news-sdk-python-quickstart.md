---
title: 'Guida introduttiva: Bing News Search SDK, Python'
titleSuffix: Azure Cognitive Services
description: Configurazione per l'applicazione console Bing News Search SDK.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 8e4343b053835c0fc2219373ad60f96c7b80636a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803342"
---
# <a name="quickstart-bing-news-search-sdk-with-python"></a>Guida introduttiva: Bing News Search SDK con Python

L'SDK di Ricerca notizie contiene la funzionalità dell'API REST per query Web e analisi dei risultati. 

Gli [esempi di codice origine per l'SDK di Ricerca notizie Bing per Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) sono disponibili su GitHub.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione
Se Python non è disponibile, installarlo. L'SDK è compatibile con Python 2.7, 3.3, 3.4, 3.5 e 3.6.

L'indicazione generale per lo sviluppo di Python è di usare un [ambiente virtuale](https://docs.python.org/3/tutorial/venv.html). Installare e inizializzare l'ambiente virtuale con il [modulo venv](https://pypi.python.org/pypi/virtualenv). È necessario installare virtualenv per Python 2.7.
```
python -m venv mytestenv
```
Installare le dipendenze dell'SDK di Ricerca notizie Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Client di Ricerca notizie
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in *Ricerca*. Aggiungere le importazioni:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Creare un'istanza di `CognitiveServicesCredentials`. Creare un'istanza del client:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Cercare i risultati e visualizzare il primo risultato della pagina Web:
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")

```
Applicando i filtri, cercare le notizie più recenti per "Artificial Intelligence" con i parametri `freshness` e `sortBy`. Verificare il numero di risultati e visualizzare `totalEstimatedMatches`, `name`, `url`, `description`, `published time` e `name of provider` del primo risultato delle notizie.
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Cercare le notizie relative a intrattenimento film e TV con la ricerca sicura. Verificare il numero di risultati e visualizzare `category`, `name`, `url`, `description`, `published time` e `name of provider` del primo risultato delle notizie.
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
Cercare gli argomenti di tendenza delle notizie in Bing.  Verificare il numero di risultati e visualizzare `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` e `image Url` del primo risultato delle notizie.
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>Passaggi successivi

[Esempi di Python SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


