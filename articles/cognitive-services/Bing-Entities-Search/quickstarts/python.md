---
title: "Guida introduttiva: Inviare una richiesta di ricerca all'API REST con Python - Ricerca entità Bing"
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per inviare una richiesta all'API REST Ricerca entità Bing con Python e ricevere una risposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 52695cca2fe49829705ac2283c9f083ada8ae0b0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327098"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>Guida introduttiva: Inviare una richiesta di ricerca all'API REST Ricerca entità Bing con Python

Usare questa guida introduttiva per eseguire la prima chiamata all'API Ricerca entità Bing e visualizzare la risposta JSON. Questa semplice applicazione Python invia una query di ricerca notizie all'API e visualizza la risposta. Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py).

L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

* [Python](https://www.python.org/downloads/) 2.x o 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere le importazioni seguenti. Creare variabili per la chiave di sottoscrizione, l'endpoint, il mercato e una query di ricerca. È possibile trovare l'endpoint nel dashboard di Azure.

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. Creare un URL di richiesta aggiungendo la variabile relativa al mercato al parametro `?mkt=`. Codificare la query con l'URL e aggiungerla al parametro `&q=`. 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>Inviare una richiesta e ottenere una risposta

1. Creare una funzione denominata `get_suggestions()`. Quindi, eseguire i passaggi seguenti.
   1. Aggiungere la chiave di sottoscrizione a un dizionario, usando `Ocp-Apim-Subscription-Key` come chiave.
   2. Usare `http.client.HTTPSConnection()` per creare un oggetto client HTTPS. Inviare una richiesta `GET` usando `request()` con le informazioni di percorso, parametri e intestazione.
   3. Archiviare la risposta con `getresponse()` e restituire `response.read()`.

      ```python
      def get_suggestions ():
       headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
       conn = http.client.HTTPSConnection (host)
       conn.request ("GET", path + params, None, headers)
       response = conn.getresponse ()
       return response.read()
      ```

2. Chiamare `get_suggestions()` e stampare la risposta json.

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
    ```

## <a name="example-json-response"></a>Risposta JSON di esempio

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](../tutorial-bing-entities-search-single-page-app.md)

* [Informazioni sull'API Ricerca entità Bing](../search-the-web.md)
* [Informazioni di riferimento sull'API Ricerca entità Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
