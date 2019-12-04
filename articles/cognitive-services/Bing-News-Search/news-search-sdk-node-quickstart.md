---
title: "Guida introduttiva: Eseguire una ricerca di notizie con l'SDK per Node.js - Ricerca notizie Bing"
titleSuffix: Azure Cognitive Services
description: Usare questo avvio rapido per cercare notizie con l'SDK di Ricerca notizie Bing per Node.js ed elaborare la risposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9bbad7685cf7d562189871bb5fa4a56d69287db4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383892"
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
> [Creare app Web a pagina singola](tutorial-bing-news-search-single-page-app.md)
