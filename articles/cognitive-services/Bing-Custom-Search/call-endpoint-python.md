---
title: "Avvio rapido: Chiamare l'endpoint di Ricerca personalizzata Bing usando Python | Microsoft Docs"
titlesuffix: Azure Cognitive Services
description: Usare questo argomento di avvio rapido per iniziare a richiedere risultati della ricerca dall'istanza di Ricerca personalizzata Bing con Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: a6f8d3964c71d036053fe0d4136468fde690f872
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337278"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Avvio rapido: Chiamare l'endpoint di Ricerca personalizzata Bing usando Python

Usare questo argomento di avvio rapido per iniziare a richiedere risultati della ricerca dall'istanza di Ricerca personalizzata Bing. Benché l'applicazione sia scritta in Python, l'API Ricerca personalizzata Bing è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza di Ricerca personalizzata Bing. Vedere [Avvio rapido: Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md) per altre informazioni.
- [Python](https://www.python.org/) 2.x o 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere le istruzioni di importazione seguenti. Creare variabili per la chiave di sottoscrizione, l'ID di configurazione personalizzata e un termine di ricerca. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Inviare e ricevere una richiesta di ricerca 

1. Creare l'URL della richiesta aggiungendo il termine di ricerca al parametro di query `q=` e l'ID di configurazione personalizzata dell'istanza di ricerca a `customconfig=`. Separare i parametri con un carattere `&`. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Inviare la richiesta all'istanza di Ricerca personalizzata Bing e stampare i risultati della ricerca restituiti.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](./tutorials/custom-search-web-page.md)
