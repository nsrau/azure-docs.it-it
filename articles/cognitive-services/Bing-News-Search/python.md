---
title: "Guida introduttiva: Eseguire una ricerca di notizie con Python e l'API REST Ricerca notizie Bing"
titleSuffix: Azure Cognitive Services
description: Usare questa guida di avvio rapido per inviare una richiesta all'API REST Ricerca notizie Bing usando Python e ricevere una risposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 6/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2fc67cbfedccd69ff9063b0575ae51197eceb09d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423691"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Avvio rapido: Eseguire una ricerca di notizie con Python e l'API REST Ricerca notizie Bing

Usare questa guida introduttiva per eseguire la prima chiamata all'API Ricerca notizie Bing e ricevere una risposta JSON. Questa semplice applicazione JavaScript invia una query di ricerca all'API ed elabora i risultati. L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

È possibile eseguire questo codice di esempio come Jupyter Notebook in [MyBinder](https://mybinder.org) facendo clic sulla notifica di avvio del Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Il codice sorgente di questo esempio è disponibile anche in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file Python nell'ambiente di sviluppo integrato o nell'editor preferito e importare il modulo di richiesta. Creare variabili per la chiave di sottoscrizione, l'endpoint e un termine di ricerca. È possibile trovare l'endpoint nel dashboard di Azure.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Creare i parametri per la richiesta

1. Aggiungere la chiave di sottoscrizione a un nuovo dizionario, usando `"Ocp-Apim-Subscription-Key"` come chiave. Eseguire la stessa operazione per i parametri di ricerca.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Inviare una richiesta e ottenere una risposta

1. Usare la libreria di richieste per chiamare l'API Ricerca visiva Bing usando la chiave di sottoscrizione e gli oggetti dizionario creati nel passaggio precedente.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` contiene la risposta dall'API come oggetto JSON. Accedere alle descrizioni di articoli contenuti nella risposta.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Visualizzazione dei risultati

Si può quindi eseguire il rendering di queste descrizioni come una tabella con la parola chiave di ricerca evidenziata in **grassetto**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](tutorial-bing-news-search-single-page-app.md)
