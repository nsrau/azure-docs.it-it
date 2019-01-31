---
title: Informazioni sull'API Ricerca notizie Bing
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare l'API Ricerca notizie Bing per la ricerca sul Web di notizie in diverse categorie, tra cui titoli e gli argomenti di tendenza.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 01/10/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 1409839718b7ea2f685952a396ea2f52bd34fae1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55148813"
---
# <a name="what-is-the-bing-news-search-api"></a>Informazioni sull'API Ricerca notizie Bing

L'API Ricerca notizie Bing facilita l'integrazione delle funzionalità di ricerca notizie cognitive nelle applicazioni. L'API fornisce un'esperienza simile a [Bing Notizie](https://www.bing.com/news), consentendo di inviare le query di ricerca e ricevere le notizie pertinenti.

Tenere presente che l'API Ricerca notizie Bing fornisce solo risultati della ricerca notizie. Usare l'[API Ricerca Web Bing](../bing-web-search/search-the-web.md), l'[API Ricerca video](../bing-video-search/search-the-web.md) e l'[API Ricerca immagini Bing](../bing-image-search/overview.md) per altri tipi di contenuto Web.

## <a name="bing-news-search-api-features"></a>Funzionalità dell'API Ricerca notizie Bing

Oltre a trovare e restituire principalmente le notizie pertinenti, l'API Ricerca notizie Bing offre diverse funzionalità per il recupero intelligente e mirato delle notizie sul Web.

|Funzionalità  |DESCRIZIONE  |
|---------|---------|
|[Suggerire e usare i termini di ricerca](concepts/search-for-news.md#suggest-and-use-search-terms)     | È possibile migliorare l'esperienza di ricerca con l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per visualizzare suggerimenti sui termini di ricerca durante la digitazione.         |
|[Ottenere notizie generali](concepts/search-for-news.md#get-general-news)     | Per trovare notizie, inviare una query di ricerca all'API Ricerca notizie Bing per ricevere un elenco delle notizie pertinenti.           |
|[Notizie principali del giorno](concepts/search-for-news.md#get-todays-top-news)      | È possibile ottenere le notizie principali del giorno in tutte le categorie.       |
|[Notizie per categoria](concepts/search-for-news.md)     | È possibile cercare notizie in categorie specifiche.        | 
|[Notizie in prima pagina](concepts/search-for-news.md)     | È possibile cercare i titoli principali in tutte le categorie.         |

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca notizie Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. È possibile usare il servizio mediante l'API REST o il Software Development Kit.

1. Creare un account API Servizi cognitivi con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Inviare una richiesta all'API con una query di ricerca valida.

3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

Provare prima la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) per l'API Ricerca notizie Bing. Questa demo illustra come personalizzare rapidamente una query di ricerca e trovare notizie sul Web.

Per iniziare rapidamente con la prima richiesta API, provare un'esercitazione introduttiva per l'[API REST](quickstart.md) o uno degli [SDK](sdk.md).

## <a name="see-also"></a>Vedere anche 

* La sezione di riferimento sull'[API Ricerca notizie Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) contiene definizioni e informazioni sugli endpoint, le intestazioni, le risposte dell'API e i parametri di query che è possibile usare per richiedere i risultati della ricerca basata su immagine.

* I [requisiti per l'uso e la visualizzazione di Bing](./useanddisplayrequirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.
