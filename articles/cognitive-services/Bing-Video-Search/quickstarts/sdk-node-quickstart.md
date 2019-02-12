---
title: "Guida introduttiva: Cercare video con l'SDK di Ricerca video Bing per Node.js"
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per inviare richieste di ricerca video con l'SDK di Ricerca video Bing per Node.js
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ffb04634b41d14e151c5a14d052d0d9f70b34d70
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569202"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Guida introduttiva: Eseguire una ricerca di video con l'SDK di Ricerca video Bing per Node.js

Usare questa guida introduttiva per iniziare a cercare notizie con l'SDK di Ricerca video Bing per Node.js. Mentre Ricerca video Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Contiene altre annotazioni e funzionalità.

## <a name="prerequisites"></a>Prerequisiti

- [Node.js](https://www.nodejs.org/)

Per configurare un'applicazione console usando Bing Video Search SDK:
* Eseguire `npm install ms-rest-azure` nell'ambiente di sviluppo.
* Eseguire `npm install azure-cognitiveservices-videosearch` nell'ambiente di sviluppo.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file JavaScript nell'editor o nell'ambiente IDE preferito e aggiungere un'istruzione `require()` per l'SDK di Ricerca video Bing e il modulo `CognitiveServicesCredentials`. Creare una variabile per la chiave di sottoscrizione. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
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

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche  

* [Informazioni sull'API Ricerca video Bing](../overview.md)
* [Esempi di .NET SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)