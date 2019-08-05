---
title: "Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando Python SDK | Microsoft Docs"
titleSuffix: Azure Cognitive Services
description: Usare questo argomento di avvio rapido per iniziare a richiedere risultati della ricerca dall'istanza di Ricerca personalizzata Bing con Python SDK.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 7a9d0d6619878299768e1f17b4da3280ea1ec1fc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564621"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando Python SDK 

Usare questo argomento di avvio rapido per iniziare a richiedere risultati della ricerca dall'istanza di Ricerca personalizzata Bing con Python SDK. Benché Ricerca personalizzata Bing includa un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK offre un semplice strumento per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) con annotazioni aggiuntive e altre informazioni sulla gestione degli errori.

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza di Ricerca personalizzata Bing. Vedere [Avvio rapido: Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md) per altre informazioni.
- Python [2.x o 3.x](https://www.python.org/) 
- [SDK di Ricerca personalizzata Bing per Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Installare l'SDK per Python

Installare l'SDK di Ricerca personalizzata Bing con il comando seguente.

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

1. Creare una variabile per la chiave di sottoscrizione.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Creare un'istanza di `CustomSearchClient` usando un `CognitiveServicesCredentials` oggetto con la chiave di sottoscrizione. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
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
> [Compilare un'app Web di Ricerca personalizzata](./tutorials/custom-search-web-page.md)
