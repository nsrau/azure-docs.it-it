---
title: 'Avvio rapido: Libreria client di Ricerca personalizzata Bing per Python'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253050"
---
Introduzione alla libreria client di Ricerca personalizzata Bing per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. L'API Ricerca personalizzata Bing consente di creare esperienze di ricerca personalizzate senza pubblicità per specifici argomenti di interesse. Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Usare la libreria client di Ricerca personalizzata Bing per Python per:
* Trovare i risultati della ricerca sul Web dall'istanza di Ricerca personalizzata Bing.

[Documentazione di riferimento](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [Pacchetto (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Prerequisiti

- Un'istanza di Ricerca personalizzata Bing. Vedere [Avvio rapido: Creare la prima istanza di Ricerca personalizzata Bing](../../quick-start.md) per altre informazioni.
- Python [2.x o 3.x](https://www.python.org/) 
- [SDK di Ricerca personalizzata Bing per Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Installare la libreria client per Python

Installare la libreria client di Ricerca personalizzata Bing con il comando seguente.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Creare una nuova applicazione

Creare un nuovo file Python nell'editor o nell'IDE preferito e aggiungere le importazioni seguenti.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Creare un client di ricerca e inviare una richiesta

1. Creare una variabile per l'endpoint e la chiave della sottoscrizione.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Creare un'istanza di `CustomSearchClient` usando un `CognitiveServicesCredentials` oggetto con la chiave di sottoscrizione. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Inviare una richiesta di ricerca con `client.custom_instance.search()`. Aggiungere il termine di ricerca al `query` parametro e impostare `custom_config` l'ID di configurazione personalizzata in modo che usi l'istanza di ricerca. È possibile ottenere l'ID dal [portale di Ricerca personalizzata Bing](https://www.customsearch.ai/), facendo clic sulla scheda **Production** (Produzione).

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Visualizzare i risultati della ricerca

Se sono stati trovati risultati di ricerca su una pagina Web, prendere il primo e stampare i relativi nome, URL e totale pagine Web trovate.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](../../tutorials/custom-search-web-page.md)
