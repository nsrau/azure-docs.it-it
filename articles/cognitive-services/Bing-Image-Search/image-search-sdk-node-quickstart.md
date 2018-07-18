---
title: Guida introduttiva all'SDK di Ricerca immagini per Node | Microsoft Docs
description: Configurazione per l'applicazione console dell'SDK di Ricerca immagini.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e4c8303e39accbb7caec15c0ef47d701971ce632
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377900"
---
# <a name="image-search-sdk-node-quickstart"></a>Guida introduttiva all'SDK di Ricerca immagini per Node

L'SDK di Ricerca immagini Bing contiene la funzionalità dell'API REST per query di immagini e analisi dei risultati. 

Il [codice sorgente per gli esempi dell'SDK di Ricerca immagini Bing per Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) è disponibile su GitHub.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione

Per configurare un'applicazione console tramite l'SDK di Ricerca immagini Bing, eseguire `npm install azure-cognitiveservices-imagesearch` nell'ambiente di sviluppo.

## <a name="image-search-client"></a>Client di Ricerca immagini
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in *Ricerca*. Creare un'istanza di `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Creare quindi un'istanza del client:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Usare il client per eseguire una ricerca con il testo della query "El Capitan":
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>Passaggi successivi

[Esempi di SDK Node.js per servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)