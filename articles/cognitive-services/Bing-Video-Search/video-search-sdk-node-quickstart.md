---
title: Guida introduttiva a Video Search SDK per Node | Microsoft Docs
description: Configurazione per l'applicazione console Video Search SDK.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 5718c750288e0a5605db3296d2911cca5e03375c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378512"
---
# <a name="video-search-sdk-node-quickstart"></a>Guida introduttiva a Video Search SDK per Node

Bing Video Search SDK contiene la funzionalità dell'API REST per query di video e analisi dei risultati. 

Il [codice sorgente per esempi Node di Bing Video Search SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) è disponibile su Git Hub.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione

Per configurare un'applicazione console tramite Bing Video Search SDK, eseguire `npm install azure-cognitiveservices-videosearch` nell'ambiente di sviluppo.

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
