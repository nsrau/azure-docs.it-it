---
title: Che cos'è l'API Ricerca video Bing?
titleSuffix: Azure Cognitive Services
description: Informazioni su come cercare video nel Web usando l'API Ricerca video Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: d5a4c3110186329516f10465c5e80a10b0ceb7b7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379821"
---
# <a name="what-is-the-bing-video-search-api"></a>Che cos'è l'API Ricerca video Bing?

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

L'API Ricerca video Bing semplifica l'aggiunta di funzionalità di ricerca video a servizi e applicazioni. Tramite l'invio di query di ricerca utente con l'API, è possibile ottenere e visualizzare video pertinenti e di alta qualità in modo simile a [Bing Video](https://www.bing.com/video). Usare questa API per i risultati di ricerca che contengono solo video. L'[API Ricerca Web Bing](../bing-web-search/overview.md) può restituire altri tipi di contenuti Web, tra cui pagine Web, video, notizie e immagini.

## <a name="bing-video-search-api-features"></a>Funzionalità dell'API Ricerca video Bing

| Funzionalità                                                                                                                                                                                 | Descrizione                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggerimento dei termini di ricerca in tempo reale](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | È possibile migliorare l'esperienza dell'app con l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per visualizzare suggerimenti sui termini di ricerca durante la digitazione. |
| [Filtro e limitazione dei video restituiti come risultato](concepts/get-videos.md#filtering-videos)                      | È possibile filtrare i video restituiti come risultato modificando i parametri della query.                                                                                                       |
| [Ritaglio, ridimensionamento e visualizzazione anteprime](../bing-web-search/resize-and-crop-thumbnails.md)                                                | È possibile modificare e visualizzare le anteprime dei video restituiti dall'API Ricerca video Bing.                                                                                      |
| [Ricerca di video di tendenza](trending-videos.md) | È possibile cercare video di tendenza da tutto il mondo.                                                                                                          |
| [Ricerca di informazioni dettagliate per i video](video-insights.md) | È possibile personalizzare una ricerca per trovare video di tendenza da tutto il mondo.                                                                                                          |

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca video Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. È possibile usare il servizio mediante l'[API REST](./quickstarts/csharp.md) o il [Software Development Kit](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

1. Creare un [account API Servizi cognitivi](../cognitive-services-apis-create-account.md) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/free/cognitive-services/) gratuito.
2. Inviare una richiesta all'API con una query di ricerca valida.
3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.


## <a name="next-steps"></a>Passaggi successivi

La [demo interattiva](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) dell'API Ricerca video Bing mostra come personalizzare una query di ricerca e cercare video nel Web.

Usare la [guida introduttiva](./quickstarts/csharp.md) per eseguire rapidamente la prima richiesta API.

## <a name="see-also"></a>Vedere anche

* La pagina di riferimento per l'[API Ricerca video Bing v7](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) contiene l'elenco di endpoint, intestazioni e parametri di query da usare per richiedere i risultati di ricerca.

* I [requisiti per l'uso e la visualizzazione di Bing](../bing-web-search/use-display-requirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.

* Per esplorare le altre API disponibili, visitare la [pagina dell'hub dell'API Ricerca Bing](../bing-web-search/overview.md).