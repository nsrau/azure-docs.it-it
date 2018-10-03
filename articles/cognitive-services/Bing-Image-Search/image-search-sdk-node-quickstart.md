---
title: 'Guida introduttiva: ricerca di immagini con il SDK di ricerca immagini Bing per Node.js'
description: Usare questa guida introduttiva per effettuare la prima ricerca di immagini tramite il SDK di ricerca immagini Bing, che consiste in un wrapper per l'API e contiene le stesse funzionalità. Questa semplice applicazione Node.js invia una query di ricerca immagine, analizza la risposta JSON e visualizza l'URL della prima immagine restituita.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 2fa3a221d42a522f19b00a4cb5596d93039a85ac
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220221"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-nodejs"></a>Guida introduttiva: ricerca di immagini con il SDK di ricerca immagini Bing e Node.js

Usare questa guida introduttiva per effettuare la prima ricerca di immagini tramite il SDK di ricerca immagini Bing, che consiste in un wrapper per l'API e contiene le stesse funzionalità. Questa semplice applicazione JavaScript invia una query di ricerca immagine, analizza la risposta JSON e visualizza l'URL della prima immagine restituita.

Il codice sorgente per questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) con altre annotazioni e informazioni sulla gestione degli errori.

## <a name="prerequisites"></a>Prerequisiti

* Il [SDK Ricerca immagini di Servizi cognitivi per Node.js](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Installare usando `npm install azure-cognitiveservices-imagesearch`
* Il modulo [REST di Azure Node.js](https://www.npmjs.com/package/ms-rest-azure)
    * Installare usando `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file JavaScript nell'IDE o editor preferito e impostare severità, https e altri requisiti.

    ```javascript
    'use strict';
    https = require('https');
    const Search = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. Nel metodo principale del progetto, creare variabili per la chiave di sottoscrizione valida, i risultati di immagine che devono essere restituita da Bing e un termine di ricerca. Creare quindi il client di ricerca delle immagini usando la chiave.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new Search.ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Creare una funzione helper asincrone

1. Creare una funzione per chiamare il client in modo asincrono e restituire la risposta dal servizio di ricerca immagini Bing.  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
## <a name="send-a-query-and-handle-the-response"></a>Inviare una query e gestire la risposta

1. Chiamare la funzione helper e gestire il relativo `promise` per analizzare i risultati di immagine restituiti con la risposta.

    Se la risposta contiene i risultati della ricerca, archiviare il primo risultato e stampare i dettagli, ad esempio l'URL di anteprima e l'URL originale, oltre al numero totale di immagini restituite.  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Vedere anche 

* [Informazioni su Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Provare una demo interattiva online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ottenere gratuitamente una chiave di accesso per Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Esempi Node.js per SDK di Servizi cognitivi di Azure](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Documentazione di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Informazioni di riferimento per l'API Ricerca immagini Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
