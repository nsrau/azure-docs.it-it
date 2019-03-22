---
title: "Guida introduttiva: Usare l'SDK di Ricerca Web Bing per Python"
titleSuffix: Azure Cognitive Services
description: L'SDK di Ricerca Web Bing facilita l'integrazione di Ricerca Web Bing nell'applicazione Python. In questa guida introduttiva si apprenderà come inviare una richiesta, ricevere una risposta JSON e filtrare e analizzare i risultati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 273922c8cf48c24ff3b1b55fa44b36b69e061057
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863900"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>Guida introduttiva: Usare l'SDK di Ricerca Web Bing per Python

L'SDK di Ricerca Web Bing facilita l'integrazione di Ricerca Web Bing nell'applicazione Python. In questa guida introduttiva si apprenderà come inviare una richiesta, ricevere una risposta JSON e filtrare e analizzare i risultati.

Per vedere il codice subito, gli [esempi dell'SDK di Ricerca Web Bing per Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) sono disponibili in GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Prerequisiti
L'SDK di Ricerca Web Bing è compatibile con Python 2.7, 3.3, 3.4, 3.5 e 3.6. Per questa guida introduttiva è consigliabile usare un ambiente virtuale.

* Python 2.7, 3.3, 3.4, 3.5 o 3.6
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) per Python 2.7
* [venv](https://pypi.python.org/pypi/virtualenv) per Python 3.x

## <a name="create-and-configure-your-virtual-environment"></a>Creare e configurare l'ambiente virtuale

Le istruzioni per configurare un ambiente virtuale sono diverse per Python 2.x e Python 3.x. Per creare e inizializzare l'ambiente virtuale, seguire questa procedura.

### <a name="python-2x"></a>Python 2.x

Creare un ambiente virtuale con `virtualenv` per Python 2.7:

```console
virtualenv mytestenv
```

Attivare l'ambiente:

```console
cd mytestenv
source bin/activate
```

Installare le dipendenze di Bing Web Search SDK:

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Creare un ambiente virtuale con `venv` per Python 3.x:

```console
python -m venv mytestenv
```

Installare le dipendenze di Bing Web Search SDK:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Creare un client e stampare i primi risultati

Dopo aver configurato l'ambiente virtuale e installato le dipendenze, è ora possibile creare un client. Il client gestirà le richieste inviate all'API Ricerca Web Bing e le risposte provenienti da tale API.

Se la risposta contiene pagine Web, immagini, notizie o video, viene stampato il primo risultato di ogni elemento.

1. Creare un nuovo progetto Python tramite l'editor o l'IDE preferito.

1. Copiare l'esempio di codice seguente nel progetto:  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchAPI
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client.
    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```

1. Sostituire `subscription_key` con una chiave di sottoscrizione valida.

1. Eseguire il programma. Ad esempio: `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Definire le funzioni e filtrare i risultati

Ora che è stata creata la prima chiamata all'API Ricerca Web Bing, esaminiamo alcune funzioni. Le sezioni seguenti illustrano la funzionalità SDK per perfezionare le query e filtrare i risultati. Ogni funzione può essere aggiunta al programma Python creato nella sezione precedente.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Limitare il numero di risultati restituiti da Bing

In questo esempio vengono usati i parametri `count` e `offset` per limitare il numero di risultati restituiti tramite il [metodo `search`](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python) dell'SDK. Vengono stampati `name` e `url` per il primo risultato.

1. Aggiungere il codice seguente al progetto Python:

   ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Eseguire il programma.

### <a name="filter-for-news-and-freshness"></a>Filtrare in base alle notizie e all'aggiornamento

In questo esempio vengono usati i parametri `response_filter` e `freshness` per filtrare i risultati della ricerca tramite il [metodo `search`](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations) dell'SDK. I risultati della ricerca restituiti si limitano agli articoli e alle pagine rilevati da Bing nelle ultime 24 ore. Vengono stampati `name` e `url` per il primo risultato.

1. Aggiungere il codice seguente al progetto Python:

    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```

1. Eseguire il programma.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Usare i filtri safe search, answer count e promote

In questo esempio vengono usati i parametri `answer_count`, `promote` e `safe_search` per filtrare i risultati della ricerca tramite il [metodo `search`](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python) dell'SDK. Vengono visualizzati `name` e `url` per il primo risultato.

1. Aggiungere il codice seguente al progetto Python:

    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
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

1. Eseguire il programma.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver terminato di usare questo progetto, rimuovere la chiave di sottoscrizione dal codice del programma e disattivare l'ambiente virtuale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esempi di Python SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>Vedere anche 

* [Informazioni di riferimento su Azure Python SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/websearch)
