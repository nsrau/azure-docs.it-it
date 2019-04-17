---
title: "Guida introduttiva: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e Python"
titleSuffix: Azure Cognitive Services
description: Informazioni su come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 7ec37b4c3bdeb924b3e35dbcb5d07a478611f631
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047127"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Guida introduttiva: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e Python

Usare questo argomento di avvio rapido per eseguire la prima chiamata all'API Ricerca visiva Bing e visualizzare i risultati. Quest'applicazione Python carica un'immagine nell'API e visualizza le informazioni restituite. Anche se l'applicazione è scritta in Python, l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

Quando si carica un'immagine locale, i dati del modulo devono includere l'intestazione `Content-Disposition`. È necessario impostare il parametro `name` su "image" e il parametro `filename` su qualsiasi stringa. Il contenuto del modulo include i dati binari dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Prerequisiti

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inizializzare l'applicazione

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere l'istruzione `import` seguente:

    ```python
    import requests, json
    ```

2. Creare le variabili per la chiave di sottoscrizione, l'endpoint e il percorso dell'immagine da caricare:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Creare un oggetto dizionario per contenere le informazioni di intestazione della richiesta. Associare la chiave di sottoscrizione alla stringa `Ocp-Apim-Subscription-Key`, come illustrato di seguito:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Creare un altro dizionario per contenere l'immagine, che verrà aperta e caricata quando si invia la richiesta:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analizzare la risposta JSON.

1. Creare un metodo denominato `print_json()` per accettare la risposta dell'API e stampare il codice JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Inviare la richiesta

1. Usare `requests.post()` per inviare una richiesta all'API Ricerca visiva Bing. Includere la stringa per l'endpoint, l'intestazione e le informazioni sui file. Stampare `response.json()` con `print_json()`:

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
