---
title: 'Avvio rapido: Eseguire una ricerca di notizie - SDK di Ricerca notizie Bing per Node.js'
titleSuffix: Azure Cognitive Services
description: Usare questo avvio rapido per cercare notizie con l'SDK di Ricerca notizie Bing per Node.js ed elaborare la risposta.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 8f45e0921748092af4702e03d557e52153a78ec0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258663"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Avvio rapido: Eseguire una ricerca di notizie con l'SDK di Ricerca notizie Bing per Node.js

Usare questa guida introduttiva per iniziare a cercare notizie con l'SDK di Ricerca notizie Bing per Node.js. Mentre Ricerca notizie Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK fornisce un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/en/)

Per configurare un'applicazione console usando Bing News Search SDK:
1. Eseguire `npm install ms-rest-azure` nell'ambiente di sviluppo.
2. Eseguire `npm install azure-cognitiveservices-newssearch` nell'ambiente di sviluppo.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

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

3. Usare il client per eseguire una ricerca con un termine di query, in questo caso "Winter Olympics":
    
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
[Creare app Web a pagina singola](tutorial-bing-news-search-single-page-app.md)
