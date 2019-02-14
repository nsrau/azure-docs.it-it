---
title: "Avvio rapido: Inviare una richiesta di ricerca con l'SDK di Ricerca entità Bing per Node.js"
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per cercare entità con l'SDK di Ricerca entità Bing per Node.js
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 04ec95e891b4e9333949a3a0f40dcc9df88e49e7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865856"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Avvio rapido: Inviare una richiesta di ricerca con l'SDK di Ricerca entità Bing per Node.js

Usare questa guida introduttiva per iniziare a cercare entità con l'SDK di Ricerca entità Bing per Node.js. Mentre Ricerca entità Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Prerequisiti

* La versione più recente di [Node.js](https://nodejs.org/en/download/).

* L'[SDK di Ricerca entità Bing per Node.js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Per installare l'SDK di Ricerca entità Bing:

1. Eseguire `npm install ms-rest-azure` nell'ambiente di sviluppo.
2. Eseguire `npm install azure-cognitiveservices-entitysearch` nell'ambiente di sviluppo.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file JavaScript nell'ambiente di sviluppo integrato o nell'editor preferito e aggiungere i requisiti seguenti. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Creare un'istanza di `CognitiveServicesCredentials` usando la chiave della sottoscrizione. Quindi usarla per creare un'istanza del client di ricerca.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Inviare una richiesta e ricevere una risposta

2. Inviare una richiesta di ricerca entità con `entitiesOperations.search()`. Dopo aver ricevuto una risposta, stampare `queryContext`, il numero di risultati restituiti e la descrizione del primo risultato.
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](../tutorial-bing-entities-search-single-page-app.md)

* [Informazioni sull'API Ricerca entità Bing](../overview.md )