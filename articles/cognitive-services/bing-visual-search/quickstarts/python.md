---
title: "Guida introduttiva: Ottenere informazioni dettagliate sulle immagini con l'API REST e Python - Ricerca visiva Bing"
titleSuffix: Azure Cognitive Services
description: Informazioni su come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: tracking-python
ms.openlocfilehash: 8a28b187898bc9725b79bfb73c76d161b5ecd4d5
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607262"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Guida introduttiva: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e Python

Usare questa guida di avvio rapido per effettuare la prima chiamata all'API Ricerca visiva Bing. Quest'applicazione Python carica un'immagine nell'API e visualizza le informazioni restituite. Anche se l'applicazione è scritta in Python, l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inizializzare l'applicazione

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere l'istruzione `import` seguente:

    ```python
    import requests, json
    ```

2. Creare le variabili per la chiave di sottoscrizione, l'endpoint e il percorso dell'immagine da caricare. Per il valore di `BASE_URI`, è possibile usare l'endpoint globale nel codice seguente o l'endpoint del [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. Quando si carica un'immagine locale, i dati del modulo devono includere l'intestazione `Content-Disposition`. Impostare il parametro `name` su "image" e il parametro `filename` sul nome file dell'immagine. Il contenuto del modulo include i dati binari dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. Creare un oggetto dizionario per contenere le informazioni di intestazione della richiesta. Associare la chiave di sottoscrizione alla stringa `Ocp-Apim-Subscription-Key`.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. Creare un altro dizionario per contenere l'immagine, che verrà aperta e caricata quando si invia la richiesta.

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analizzare la risposta JSON.

Creare un metodo denominato `print_json()` per accettare la risposta dell'API e visualizzare il codice JSON.

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>Inviare la richiesta

Usare `requests.post()` per inviare una richiesta all'API Ricerca visiva Bing. Includere la stringa per l'endpoint, l'intestazione e le informazioni sui file. Visualizzare `response.json()` con `print_json()`.

```python
try:
    response = requests.post(BASE_URI, headers=HEADERS, files=file)
    response.raise_for_status()
    print_json(response.json())
    
except Exception as ex:
    raise ex
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola con Ricerca visiva](../tutorial-bing-visual-search-single-page-app.md)
