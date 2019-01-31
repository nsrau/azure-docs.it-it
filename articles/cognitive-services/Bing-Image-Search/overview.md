---
title: Che cos'è l'API Ricerca immagini Bing?
titleSuffix: Azure Cognitive Services
description: L'API Ricerca immagini Bing consente di usare le funzionalità di ricerca cognitiva di immagini di Bing nella propria applicazione. Tramite l'invio di query di ricerca utente con l'API, è possibile ottenere e visualizzare immagini pertinenti e di alta qualità simili alle immagini Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 8115ffd943750d88a1aa9f94ba53ff972a636373
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157313"
---
# <a name="what-is-the-bing-image-search-api"></a>Che cos'è l'API Ricerca immagini Bing?

L'API Ricerca immagini Bing consente di usare le funzionalità di ricerca cognitiva di immagini di Bing nella propria applicazione. Mediante l'invio di query di ricerca utente con l'API, è possibile ottenere e visualizzare immagini pertinenti e di alta qualità simili alle [immagini Bing](https://www.bing.com/images).

Tenere presente che l'API Ricerca immagini Bing fornisce risultati della ricerca costituiti esclusivamente da immagini. Usare l'[API Ricerca Web Bing](../bing-web-search/search-the-web.md), l'[API Ricerca video](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search) e l'[API Ricerca notizie](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search) per altri tipi di contenuto Web.

## <a name="bing-image-search-features"></a>Funzionalità della Ricerca immagini Bing

Ricerca immagini Bing trova e restituisce principalmente immagini rilevanti da una query di ricerca, ma il servizio offre anche diverse funzionalità aggiuntive per il recupero intelligente e mirato di immagini sul Web.


| Funzionalità                                                                                                                                                                                 | DESCRIZIONE                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggerimento dei termini di ricerca in tempo reale](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | È possibile migliorare l'esperienza dell'app con l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per visualizzare suggerimenti sui termini di ricerca durante la digitazione. |
| [Filtro e limitazione delle immagini restituite come risultato](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#filtering-images)                       | È possibile filtrare le immagini restituite da Bing modificando i parametri di query.                                                                                                       |
| [Ritaglio, ridimensionamento e visualizzazione anteprime](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | È possibile modificare e visualizzare le anteprime delle immagini restituite dalla Ricerca immagini Bing.                                                                                      |
| [Espansione e trasformazione tramite pivot delle query di ricerca utente](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#pivoting-the-query)               | È possibile espandere le funzionalità di ricerca includendo e visualizzando i termini di ricerca suggeriti da Bing per le query.                                                                    |
| [Ricerca di immagini di tendenza](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | È possibile personalizzare una ricerca per trovare immagini di tendenza da tutto il mondo.                                                                                                          |

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca immagini Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. È possibile usare il servizio mediante l'[API REST](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) o il [Software Development Kit](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Creare un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuito.
2. Inviare una richiesta all'API con una [query di ricerca](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) valida.
3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

Provare prima la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) dell'API Ricerca immagini Bing.
Questa demo illustra come personalizzare rapidamente una query di ricerca e perlustrare il Web alla ricerca di immagini.

Quando si è pronti per chiamare l'API, creare un [account API servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuito.

Per iniziare rapidamente con la prima richiesta all'API, vedere le istruzioni relative a:

* [Inviare query di ricerca a Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) con l'API REST, oppure
* [Richiedere e filtrare](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) le immagini restituite da Bing usando l'SDK.

## <a name="see-also"></a>Vedere anche 

* La sezione di riferimento sull'[API Ricerca immagini Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) contiene definizioni e informazioni sugli endpoint, le intestazioni, le risposte dell'API e i parametri di query che è possibile usare per richiedere i risultati della ricerca basata su immagine.

* I [requisiti per l'uso e la visualizzazione di Bing](./useanddisplayrequirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.

* L'argomento [Acquisizione di immagini dal Web con l'API Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) descrive come eseguire ricerche e ottenere immagini dal Web.

* L'argomento sull'[invio e l'uso di query di ricerca](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) descrive come creare, personalizzare e trasformare tramite Pivot le query di ricerca.
