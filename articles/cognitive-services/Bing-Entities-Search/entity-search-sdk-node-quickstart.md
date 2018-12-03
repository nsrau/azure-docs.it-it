---
title: 'Guida introduttiva: Bing Entity Search SDK, Node'
titleSuffix: Azure Cognitive Services
description: Configurazione per l'applicazione console Entity Search SDK con Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: f94e3b5a6070da5ef9510216abd3f52a958030c5
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311382"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Guida introduttiva: Bing Entity Search SDK con Node

Bing Entity Search SDK contiene la funzionalità dell'API REST per query di entità e analisi dei risultati. 

Il [codice sorgente per esempi C# di Bing Entity Search SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) è disponibile su Git Hub.
## <a name="application-dependencies"></a>Dipendenze dell'applicazione
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in **Ricerca**.  Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Per configurare un'applicazione console usando Bing Entity Search SDK:
* Eseguire `npm install ms-rest-azure` nell'ambiente di sviluppo.
* Eseguire `npm install azure-cognitiveservices-entitysearch` nell'ambiente di sviluppo.

## <a name="entity-search-client"></a>Client di Ricerca entità
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in *Ricerca*. Creare un'istanza di `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Creare quindi un'istanza del client e cercare i risultati:
```
const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');

let entitySearchApiClient = new EntitySearchAPIClient(credentials);

entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
    console.log(result.queryContext);
    console.log(result.entities.value);
    console.log(result.entities.value[0].description);
}).catch((err) => {
    throw err;
});

```
Il codice stampa elementi `result.value` nella console senza analizzare il testo.  I risultati, se disponibili per ogni categoria, includono:
- _type: 'Thing'
- _type: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Passaggi successivi

[Esempi di SDK Node.js per servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
