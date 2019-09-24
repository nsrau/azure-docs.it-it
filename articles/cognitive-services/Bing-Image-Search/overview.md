---
title: Che cos'è l'API Ricerca immagini Bing?
titleSuffix: Azure Cognitive Services
description: L'API Ricerca immagini Bing consente di usare le funzionalità di ricerca cognitiva di immagini di Bing nella propria applicazione. Tramite l'invio di query di ricerca utente con l'API, è possibile ottenere e visualizzare immagini pertinenti e di alta qualità simili alle immagini Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 09/13/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f9e33ae30b3aa59f4705518c3df20118fa056a93
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996751"
---
# <a name="what-is-the-bing-image-search-api"></a>Che cos'è l'API Ricerca immagini Bing?

L'API Ricerca immagini Bing consente di usare le funzionalità di ricerca di immagini di Bing nella propria applicazione. Inviando query di ricerca all'API, è possibile ottenere immagini di alta qualità simili a quelle disponibili in [bing.com/images](https://www.bing.com/images).

Anche se l'API Ricerca immagini Bing fornisce solo immagini come risultati della ricerca, è possibile combinare o usare altre [API Ricerca Bing](../bing-web-search/bing-api-comparison.md) per trovare molti tipi di contenuto sul Web.

## <a name="bing-image-search-features"></a>Funzionalità della Ricerca immagini Bing

| Funzionalità                                                                                                                                                                                 | DESCRIZIONE                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggerimento dei termini di ricerca in tempo reale](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | È possibile migliorare l'esperienza dell'app con l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per visualizzare suggerimenti sui termini di ricerca durante la digitazione. |
| [Filtro e limitazione delle immagini restituite come risultato](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | È possibile filtrare le immagini restituite da Bing modificando i parametri di query.                                                                                                       |
| [Ritaglio, ridimensionamento e visualizzazione anteprime](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/resize-and-crop-thumbnails)                                                | È possibile modificare e visualizzare le anteprime delle immagini restituite dalla Ricerca immagini Bing.                                                                                      |
| [Espansione e trasformazione tramite pivot delle query di ricerca utente](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | È possibile espandere le funzionalità di ricerca includendo e visualizzando i termini di ricerca suggeriti da Bing per le query.                                                                    |
| [Ricerca di immagini di tendenza](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | È possibile personalizzare una ricerca per trovare immagini di tendenza da tutto il mondo.                                                                                                          |

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca immagini Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. È possibile usare il servizio mediante l'[API REST](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) o il [Software Development Kit](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Creare un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuito.
2. Inviare una richiesta all'API con una [query di ricerca](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) valida.
3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

Provare prima la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) dell'API Ricerca immagini Bing.
Questa demo illustra come personalizzare rapidamente una query di ricerca e perlustrare il Web alla ricerca di immagini.

Quando si è pronti per chiamare l'API, creare un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuito.

Per iniziare rapidamente con la prima richiesta all'API, vedere le istruzioni relative a:

* [Inviare query di ricerca a Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) con l'API REST, oppure
* [Richiedere e filtrare](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) le immagini restituite da Bing usando l'SDK.

## <a name="see-also"></a>Vedere anche

* [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) delle API Ricerca Bing. 

* La sezione di riferimento dell'[API Ricerca immagini Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) contiene informazioni su endpoint dell'API, intestazioni, risposte dell'API e parametri di query.

* I [requisiti per l'uso e la visualizzazione di Bing](./useanddisplayrequirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.

* L'articolo [Acquisizione di immagini dal Web con l'API Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) descrive come eseguire ricerche e ottenere immagini dal Web.

* L'articolo [Invio e uso di query di ricerca](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) descrive come creare, personalizzare e trasformare tramite Pivot le query di ricerca.

* [Confrontare le API di Ricerca Bing](../Bing-web-search/bing-api-comparison.md)