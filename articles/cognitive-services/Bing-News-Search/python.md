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
ms.date: 06/16/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 9d73bbbfb5d6339e4bd1c6c4d38ec268b2461150
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095493"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Guida introduttiva: Eseguire una ricerca di notizie con Python e l'API REST Ricerca notizie Bing

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Usare questa guida di avvio rapido per effettuare la prima chiamata all'API Ricerca notizie Bing. Questa semplice applicazione Python invia una query di ricerca all'API ed elabora il risultato JSON. 

Anche se l'applicazione è scritta in Python, l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

Per eseguire questo codice di esempio come Jupyter Notebook in [MyBinder](https://mybinder.org), selezionare la notifica di **avvio del binder** : 

[![avvio del binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Il codice sorgente di questo esempio è disponibile anche in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

Creare un nuovo file Python nell'ambiente di sviluppo integrato o nell'editor preferito e importare il modulo di richiesta. Creare le variabili per la chiave di sottoscrizione, l'endpoint e il termine di ricerca. È possibile usare l'endpoint globale nel codice seguente o l'endpoint del [sottodominio personalizzato](../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>Creare i parametri per la richiesta

Aggiungere la chiave di sottoscrizione a un nuovo dizionario, usando `Ocp-Apim-Subscription-Key` come chiave. Eseguire la stessa operazione per i parametri di ricerca.

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>Inviare una richiesta e ottenere una risposta

1. Usare la libreria di richieste per chiamare l'API Ricerca visiva Bing con la chiave di sottoscrizione e gli oggetti dizionario creati nel passaggio precedente.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = json.dumps(response.json())
    ```

2. Accedere alle descrizioni degli articoli contenuti nella risposta dell'API, archiviata in `search_results` come oggetto JSON. 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>Visualizzare i risultati

Si può quindi eseguire il rendering di queste descrizioni come una tabella con la parola chiave di ricerca evidenziata in grassetto.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](tutorial-bing-news-search-single-page-app.md)
