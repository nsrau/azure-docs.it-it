---
title: 'Guida introduttiva: Bing News Search SDK, Node'
titleSuffix: Azure Cognitive Services
description: Configurare l'applicazione console Bing News Search SDK
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 2279a6475ab8c39b3ff599f7244caea59d622651
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803002"
---
# <a name="quickstart-bing-news-search-sdk-with-node"></a>Guida introduttiva: Bing News Search SDK con Node

L'SDK di Ricerca notizie Bing contiene la funzionalità dell'API REST per query di notizie e analisi dei risultati. 

Il [codice sorgente per gli esempi dell'SDK di Ricerca notizie Bing per Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) è disponibile su GitHub.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione

Per configurare un'applicazione console tramite l'SDK di Ricerca notizie Bing, eseguire `npm install azure-cognitiveservices-newssearch` nell'ambiente di sviluppo.

## <a name="news-search-client"></a>Client di Ricerca notizie
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in *Ricerca*. Creare un'istanza di `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Creare quindi un'istanza del client:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Usare il client per eseguire una ricerca con il testo di una query, in questo caso "Winter Olympics":
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Il codice visualizza elementi `result.value` nella console senza analizzare il testo. I risultati, se disponibili per ogni categoria, includono:
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Passaggi successivi

[Esempi di SDK Node.js per servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
