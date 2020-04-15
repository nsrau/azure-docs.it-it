---
title: Avvio rapido per la libreria client JavaScript di Ricerca visiva Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: 404529bf269f899603b92c6c23b0d95cd2749ee5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80573129"
---
Usare questo argomento di avvio rapido per iniziare a ottenere informazioni dettagliate sulle immagini dal servizio Ricerca visiva Bing con la libreria client JavaScript. Anche se Ricerca visiva Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, la libreria client offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

[Documentazione di riferimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-visualsearch/?view=azure-node-latest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch) | [Pacchetto (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch) | [Esempi](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Prerequisiti
* [Node.js](https://www.nodejs.org/)
* Libreria client di Ricerca visiva Bing per JavaScript
    * Per configurare un'applicazione console con la libreria client di Ricerca visiva Bing, eseguire questi comandi:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

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
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Eseguire la ricerca di immagini

1. Usare `fs.createReadStream()` per leggere il file di immagine e creare le variabili per la richiesta e i risultati della ricerca. Quindi, usare il client per eseguire la ricerca nelle immagini.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
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
> [Creare un'app Web a pagina singola](../../tutorial-bing-visual-search-single-page-app.md)
