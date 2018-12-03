---
title: 'Guida introduttiva: SDK di Ricerca video Bing, Node'
titleSuffix: Azure Cognitive Services
description: Configurazione per l'applicazione console dell'SDK di Ricerca video Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 985ddcff35a16c747fff34ed487c72744e1ee466
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313448"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Guida introduttiva: SDK di Ricerca video Bing con Node

Bing Video Search SDK contiene la funzionalità dell'API REST per query di video e analisi dei risultati. 

Il [codice sorgente per esempi Node di Bing Video Search SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) è disponibile su Git Hub.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in **Ricerca**.  Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Per configurare un'applicazione console usando Bing Video Search SDK:
* Eseguire `npm install ms-rest-azure` nell'ambiente di sviluppo.
* Eseguire `npm install azure-cognitiveservices-videosearch` nell'ambiente di sviluppo.

## <a name="video-search-client"></a>Client Ricerca video
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in *Ricerca*. Creare un'istanza di `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Creare quindi un'istanza del client:
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Cercare i risultati.
```
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

[Esempi di SDK Node.js per servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
