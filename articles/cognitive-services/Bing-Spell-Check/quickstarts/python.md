---
title: "Guida introduttiva: Controllare l'ortografia con l'API REST Controllo ortografico Bing e Python"
titlesuffix: Azure Cognitive Services
description: Introduzione all'uso dell'API REST Controllo ortografico Bing per controllare l'ortografia e la grammatica.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: e95006c6448bf1179d33bcd00c16d6e4246db148
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887319"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Guida introduttiva: Controllare l'ortografia con l'API REST Controllo ortografico Bing e Python

Usare questa guida introduttiva per effettuare la prima chiamata all'API REST Controllo ortografico Bing. Questa semplice applicazione Python invia una richiesta all'API e restituisce un elenco di correzioni suggerite. L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Prerequisiti

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="initialize-the-application"></a>Inizializzare l'applicazione

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere l'istruzione di importazione seguente.

   ```python
   import requests
   import json
   ```

2. Creare variabili per il testo di cui si vuole eseguire il controllo ortografico, la chiave di sottoscrizione e l'endpoint di Controllo ortografico Bing.

    ```python
    api_key = "enter-your-key-here"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Creare i parametri per la richiesta

1. Creare un nuovo dizionario con `text` come chiave e il testo come valore.

    ```python
    data = {'text': example_text}
    ```

2. Aggiungere i parametri della richiesta. Impostare il parametro `mkt` sul proprio mercato e `mode` su `proof`. 

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Aggiungere un'intestazione `Content-Type` e la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Inviare la richiesta e leggere la risposta

1. Inviare la richiesta POST usando la libreria delle richieste.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Ottenere la risposta JSON e stamparla.
    
    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```

## <a name="example-json-response"></a>Risposta JSON di esempio

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](../tutorials/spellcheck.md)

- [Informazioni sull'API Controllo ortografico Bing](../overview.md)
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
