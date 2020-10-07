---
title: 'Avvio rapido: Libreria client di Ricerca video Bing per JavaScript'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: fa1ecb24649d9355e6104bff8add5430fdd64c3c
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377033"
---
Seguire questo argomento di avvio rapido per iniziare a cercare notizie con la libreria client di Ricerca video Bing per JavaScript. Anche se Ricerca video Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, la libreria client offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Contiene altre annotazioni e funzionalità.

## <a name="prerequisites"></a>Prerequisiti

* La versione più recente di [Node.js](https://nodejs.org/en/download/).
* L'[SDK di Ricerca video Bing per JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-videosearch)
     *  Per installarlo, eseguire `npm install @azure/cognitiveservices-videosearch`
* La classe `CognitiveServicesCredentials` del pacchetto `@azure/ms-rest-azure-js` per autenticare il client.
     * Per installarlo, eseguire `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file JavaScript nell'editor o nell'ambiente IDE preferito, quindi aggiungere un'istruzione `require()` per la libreria client di Ricerca video Bing e il modulo `CognitiveServicesCredentials`. Creare una variabile per la chiave di sottoscrizione. 
    
    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('@azure/cognitiveservices-videosearch');
    ```

2. Creare un'istanza di `CognitiveServicesCredentials` con la chiave. Usarla quindi per creare un'istanza del client di ricerca video.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Inviare la richiesta di ricerca

1. Usare `client.videosOperations.search()` per inviare una richiesta di ricerca all'API Ricerca video Bing. Quando vengono restituiti i risultati della ricerca, usare `.then()` per registrare il risultato.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche 

* [Informazioni sull'API Ricerca video Bing](../../overview.md)
* [Esempi di .NET SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)