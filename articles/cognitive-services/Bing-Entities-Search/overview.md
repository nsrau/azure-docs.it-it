---
title: Informazioni sull'API Ricerca entità Bing
titleSuffix: Azure Cognitive Services
description: Informazioni dettagliate sull'API Ricerca entità Bing per estrarre e cercare entità e luoghi da query di ricerca.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2a3d971ce9a4f89555eb3ffa489f8b19172a4b83
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351470"
---
# <a name="what-is-bing-entity-search-api"></a>Informazioni sull'API Ricerca entità Bing

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

L'API Ricerca entità Bing invia una query di ricerca a Bing e ottiene risultati che includono entità e località. I risultati relativi alle località includono ristoranti, hotel o altre aziende locali. Bing restituisce località se la query specifica il nome dell'azienda locale o richiede un tipo di azienda (ad esempio, ristoranti nelle vicinanze). Bing restituisce entità se la query specifica persone, oggetti o località note (attrazioni, stati, paesi/aree geografiche e così via).

|Funzionalità  |Descrizione  |
|---------|---------|
|[Suggerimenti per la ricerca in tempo reale](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Suggerimenti per la ricerca che possono essere visualizzati come elenchi a discesa man mano che l'utente digita.       | 
| [Disambiguazione entità](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Ottenere più entità per le query con più significati possibili. |
| [Individuare le località](concepts/search-for-entities.md#find-places) | Cercare e restituire informazioni su entità e attività commerciali locali  |

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca entità Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. È possibile usare il servizio mediante l'API REST o il Software Development Kit.

1. Creare un [account API Servizi cognitivi](../cognitive-services-apis-create-account.md) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/free/cognitive-services/) gratuito.
2. Inviare una richiesta all'API con una query di ricerca valida.
3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

* Provare la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) per l'API Ricerca entità Bing. 
* Per iniziare rapidamente con la prima richiesta, provare una [guida introduttiva](quickstarts/csharp.md).
* Sezione di riferimento sull'[API Ricerca entità Bing v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
* I [requisiti per l'uso e la visualizzazione di Bing](../bing-web-search/use-display-requirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.
* Per esplorare le altre API disponibili, visitare la [pagina dell'hub dell'API Ricerca Bing](../bing-web-search/overview.md).