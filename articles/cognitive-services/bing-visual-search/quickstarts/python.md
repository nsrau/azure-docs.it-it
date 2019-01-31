---
title: "Avvio rapido: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e Python"
titleSuffix: Azure Cognitive Services
description: Informazioni su come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 37fef6100d78b46d0fb52e486f1788eb78ea2578
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193761"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Avvio rapido: La prima query di Ricerca visiva Bing in Python

Usare questo argomento di avvio rapido per eseguire la prima chiamata all'API Ricerca visiva Bing e visualizzare i risultati della ricerca. Questa semplice applicazione JavaScript carica un'immagine nell'API e visualizza le informazioni restituite. L'applicazione è scritta in JavaScript, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

Quando si carica un'immagine locale, i dati del modulo POST devono includere l'intestazione Content-Disposition. Il parametro `name` deve essere impostato su "image" e il parametro `filename` può essere impostato su qualsiasi stringa. Il contenuto del modulo è il file binario dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB.

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

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere l'istruzione di importazione seguente.

    ```python
    import requests, json
    ```

2. Creare le variabili per la chiave di sottoscrizione, l'endpoint e il percorso dell'immagine da caricare.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Creare un oggetto dizionario per contenere le informazioni di intestazione delle richieste. Associare la chiave di sottoscrizione alla stringa `Ocp-Apim-Subscription-Key`, come illustrato di seguito.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Creare un altro dizionario per contenere l'immagine, che verrà aperta e caricata quando si invia la richiesta. 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analizzare la risposta JSON.

1. Creare un metodo denominato `print_json()` per accettare la risposta dell'API e stampare il codice JSON.

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Inviare la richiesta

1. Usare `requests.post()` per inviare una richiesta all'API Ricerca visiva Bing. Includere la stringa per l'endpoint, l'intestazione e le informazioni sui file. Stampare `response.json()` con `print_json()`

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
> [Compilare un'app Web di Ricerca personalizzata](../tutorial-bing-visual-search-single-page-app.md)
