---
title: "Avvio rapido: Controllare l'ortografia con l'API REST e Python - Controllo ortografico Bing"
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come usare l'API REST Controllo ortografico Bing per eseguire il controllo ortografico e grammaticale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 6b0977628f7c3d971804d8597f42425608028081
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448466"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Avvio rapido: Controllare l'ortografia con l'API REST Controllo ortografico Bing e Python

Usare questa guida introduttiva per effettuare la prima chiamata all'API REST Controllo ortografico Bing. Questa semplice applicazione Python invia una richiesta all'API e restituisce un elenco di correzioni suggerite. L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Prerequisites

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inizializzare l'applicazione

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere l'istruzione di importazione seguente.

   ```python
   import requests
   import json
   ```

2. Creare variabili per il testo di cui si vuole eseguire il controllo ortografico, la chiave di sottoscrizione e l'endpoint di Controllo ortografico Bing. È possibile usare l'endpoint globale seguente o l'endpoint [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Creare i parametri per la richiesta

1. Creare un nuovo dizionario con `text` come chiave e il testo come valore.

    ```python
    data = {'text': example_text}
    ```

2. Aggiungere i parametri della richiesta. Aggiungere il codice di mercato dopo `mkt=`. Il codice di mercato è il paese da cui si effettua la richiesta. Aggiungere anche la modalità di controllo ortografico dopo `&mode=`. La modalità è `proof` (individua la maggior parte degli errori di ortografia/grammatica) o `spell` (individua la maggior parte degli errori di ortografia, ma meno errori di grammatica).

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


## <a name="run-the-application"></a>Eseguire l'applicazione

Se si usa la riga di comando, usare i comandi seguenti per eseguire l'applicazione.

```bash
python <FILE_NAME>.py
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
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
