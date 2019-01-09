---
title: "Guida introduttiva: Ottenere informazioni dettagliate sulle immagini usando l'SDK di Ricerca visiva Bing per Node.js"
titleSuffix: Azure Cognitive Services
description: Caricare un'immagine usando l'SDK di Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: 1dba0f3aacd0e51a9e4d8017a93f18928fd6b2ea
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744294"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Guida introduttiva: Ottenere informazioni dettagliate sulle immagini usando l'SDK di Ricerca visiva Bing per Node.js

Usare questo argomento di avvio rapido per iniziare a ottenere informazioni dettagliate sulle immagini dal servizio Ricerca visiva Bing con l'SDK per Node.js. Mentre Ricerca visiva Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK fornisce un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

## <a name="prerequisites"></a>Prerequisiti
* [Node.js](https://www.nodejs.org/)
* SDK di Ricerca visiva Bing per Node.js
    * Per configurare un'applicazione console usando l'SDK di Ricerca visiva Bing, eseguire i comandi seguenti:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`.


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file JavaScript nell'ambiente di sviluppo integrato o nell'editor preferito e aggiungere i requisiti seguenti. Quindi, creare variabili per la chiave di sottoscrizione, l'ID di configurazione personalizzata e un percorso file all'immagine che si vuole caricare. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Creare un'istanza del client.

    ```javascript
    let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);
    ```

## <a name="search-for-images"></a>Eseguire la ricerca di immagini

1. Usare `fs.createReadStream()` per leggere il file di immagine e creare le variabili per la richiesta e i risultati della ricerca. Quindi, usare il client per eseguire la ricerca nelle immagini.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchApiClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Analizzare i risultati della query precedente:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](tutorial-bing-visual-search-single-page-app.md)
