---
title: 'Avvio rapido: Eseguire una ricerca di notizie - SDK di Ricerca notizie Bing per Python'
titleSuffix: Azure Cognitive Services
description: Usare questa guida di avvio rapido per cercare notizie con l'SDK di Ricerca notizie Bing per Python ed elaborare la risposta.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 7927e680194d682ab9ee48320afa42ba29c676dc
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259496"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Avvio rapido: Eseguire una ricerca di notizie con l'SDK di Ricerca notizie Bing per Python

Usare questa guida introduttiva per iniziare a cercare notizie con l'SDK di Ricerca notizie Bing per Python. Mentre Ricerca notizie Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK fornisce un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Prerequisiti

* [Python](https://www.python.org/) 2.x o 3.x

È consigliabile usare un [ambiente virtuale](https://docs.python.org/3/tutorial/venv.html) per lo sviluppo Python. È possibile installare e inizializzare l'ambiente virtuale con il [modulo venv](https://pypi.python.org/pypi/virtualenv). È necessario installare un virtualenv per Python 2.7. È possibile creare un ambiente virtuale con:

```console
python -m venv mytestenv
```

È possibile installare le dipendenze dell'SDK di Ricerca notizie Bing con questo comando:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file Python nell'ambiente di sviluppo integrato o nell'editor preferito e importare le librerie seguenti. Creare una variabile per la chiave di sottoscrizione e per il termine di ricerca.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Inizializzare il client e inviare una richiesta

1. Creare un'istanza di `CognitiveServicesCredentials`. Creare un'istanza del client:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Inviare una query di ricerca all'API Ricerca notizie Bing, archiviare la risposta.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Analizzare la risposta

Se vengono trovati risultati della ricerca, stampare il primo risultato della pagina Web:

```python
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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
[Creare app Web a pagina singola](tutorial-bing-news-search-single-page-app.md)