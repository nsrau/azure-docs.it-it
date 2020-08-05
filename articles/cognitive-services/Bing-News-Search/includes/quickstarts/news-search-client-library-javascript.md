---
title: 'Avvio rapido: Libreria client di Ricerca notizie Bing per JavaScript'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: cc96233ea6e2d02f3c3a2036466e3934aa234f5b
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407975"
---
Seguire questo argomento di avvio rapido per iniziare a cercare notizie con la libreria client di Ricerca notizie Bing per JavaScript. Anche se Ricerca notizie Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, la libreria client offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/en/)

Per configurare un'applicazione console usando la libreria client di Ricerca notizie Bing:
1. Eseguire `npm install ms-rest-azure` nell'ambiente di sviluppo.
2. Eseguire `npm install azure-cognitiveservices-newssearch` nell'ambiente di sviluppo.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un'istanza di `CognitiveServicesCredentials`. Creare variabili per la chiave di sottoscrizione e un termine di ricerca.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. Creare un'istanza del client:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Inviare una query di ricerca

1. Usare il client per eseguire una ricerca con un termine di query, in questo caso "Winter Olympics":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

Il codice visualizza elementi `result.value` nella console senza analizzare il testo. I risultati, se disponibili per ogni categoria, includono:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](../../tutorial-bing-news-search-single-page-app.md)
