---
title: 'Avvio rapido: Libreria client di Ricerca entità Bing per Python'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "79136600"
---
Seguire questo argomento di avvio rapido per iniziare a cercare entità con la libreria client di Ricerca entità Bing per Python. Anche se Ricerca entità Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, la libreria client offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Prerequisiti

* Python [2.x o 3.x](https://www.python.org/)

* [SDK di Ricerca entità Bing per Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

È consigliabile usare un ambiente virtuale Python. È possibile installare e inizializzare un ambiente virtuale con il modulo venv. È possibile installare virtualenv con:

```Console
python -m venv mytestenv
```

Installare la libreria client di Ricerca entità Bing con:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere le istruzioni di importazione seguenti. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Creare una variabile per l'endpoint e la chiave della sottoscrizione. Creare un'istanza del client creando un nuovo oggetto `CognitiveServicesCredentials` con la chiave.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Inviare una richiesta di ricerca e ricevere una risposta

1. Inviare una richiesta di ricerca a Ricerca entità Bing con `client.entities.search()` e una query di ricerca. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Se vengono restituite entità, convertire `entity_data.entities.value` in un elenco e stampare il primo risultato.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](../../tutorial-bing-entities-search-single-page-app.md)

* [Informazioni sull'API Ricerca entità Bing](../../overview.md )