---
title: Informazioni sull'API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: L'API Ricerca Web Bing è un servizio RESTful che offre risposte immediate a query di ricerca Web. Configurare i risultati per includere pagine Web, immagini, video, notizie e altro ancora. I risultati sono restituiti in formato JSON e basati sulla pertinenza della ricerca e sulle sottoscrizioni di Ricerca Web Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 7643486962df0516cc61857a7e31cf34bc41c732
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350637"
---
# <a name="what-is-the-bing-web-search-api"></a>Informazioni sull'API Ricerca Web Bing

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

L'API Ricerca Web Bing è un servizio RESTful che fornisce risposte immediate alle query dell'utente. I risultati della ricerca sono configurati in modo facile per includere pagine Web, immagini, video, notizie, traduzioni e altro ancora. Ricerca Web Bing restituisce i risultati in formato JSON in base alla pertinenza della ricerca e alle sottoscrizioni di Ricerca Web Bing.

Questa API è ideale per le applicazioni che devono accedere a tutto il contenuto pertinente a una query di ricerca di un utente. Se si sta sviluppando un'applicazione che richiede solo un tipo specifico di risultato, è consigliabile usare l'[API Ricerca immagini Bing](../Bing-Image-Search/overview.md), l'[API Ricerca video Bing](../bing-video-search/overview.md) o l'[API Ricerca notizie Bing](../Bing-News-Search/search-the-web.md). Per un elenco completo delle API di ricerca Bing, vedere [API Servizi cognitivi](../index.yml).

Per scoprire come funziona, provare la [demo dell'API Ricerca Web Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Funzionalità  

Ricerca Web Bing non consente solo di accedere a risposte istantanee, ma offre anche caratteristiche e funzionalità aggiuntive che permettono di personalizzare i risultati della ricerca per gli utenti.

| Funzionalità | Descrizione |
|---------|-------------|
| [Suggerimento dei termini di ricerca in tempo reale](../bing-autosuggest/get-suggested-search-terms.md) | È possibile migliorare l'esperienza dell'applicazione con l'API Suggerimenti automatici Bing per visualizzare i termini di ricerca suggeriti durante la digitazione. |
| [Filtro e limitazione dei risultati in base al tipo di contenuto](filter-answers.md) | È possibile personalizzare e affinare i risultati della ricerca con filtri e parametri di query per pagine Web, immagini, video, ricerca sicura e altro ancora. |
| [Evidenziazione dei risultati per i caratteri Unicode](hit-highlighting.md) | È possibile identificare e rimuovere i caratteri Unicode indesiderati dai risultati della ricerca prima che siano mostrati agli utenti tramite l'evidenziazione. |
| [Distribuzione dei risultati della ricerca per paese, area geografica e/o mercato](./language-support.md) | Ricerca Web Bing supporta quasi quaranta paesi o aree geografiche. Usare questa funzionalità per affinare i risultati della ricerca in base a paesi, aree geografiche o mercati specifici. |
| [Analisi delle metriche di ricerca con Statistiche Bing](bing-web-stats.md) | Statistiche Bing è una sottoscrizione a pagamento che fornisce analisi sul volume di chiamate, sulle stringhe di query più frequenti, sulla distribuzione geografica e altro ancora. |

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca Web Bing è facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare risposte JSON. Il servizio è accessibile tramite [API REST](quickstarts/python.md) o [Librerie client di Ricerca Web Bing](./quickstarts/client-libraries.md).

1. [Creare una risorsa di Azure](../cognitive-services-apis-create-account.md) per le API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/).  
2. Inviare una [richiesta all'API Ricerca Web Bing](quickstarts/python.md).
3. Analizzare la risposta JSON.

## <a name="next-steps"></a>Passaggi successivi

* Usare la [Guida introduttiva a Python](./quickstarts/client-libraries.md?pivots=programming-language-python) per effettuare la prima chiamata all'API Ricerca Web Bing.  
* [Creare app Web a pagina singola](tutorial-bing-web-search-single-page-app.md).
* Rivedere il documento [Web Search API v7 reference](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) (Informazioni di riferimento sull'API Ricerca Web v7).  
* Altre informazioni sui [requisiti per l'uso e la visualizzazione](./use-display-requirements.md) per Ricerca Web Bing.