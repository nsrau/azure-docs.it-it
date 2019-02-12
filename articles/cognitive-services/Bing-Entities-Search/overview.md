---
title: Informazioni sull'API Ricerca entità Bing
titlesuffix: Azure Cognitive Services
description: Usare l'API Ricerca entità Bing per estrarre e cercare entità e luoghi da query di ricerca.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 9190c87b7afff66162e25fb3cd08bfeac76aff74
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757827"
---
# <a name="what-is-bing-entity-search-api"></a>Informazioni sull'API Ricerca entità Bing

L'API Ricerca entità Bing invia una query di ricerca a Bing e ottiene risultati che includono entità e località. I risultati relativi alle località includono ristoranti, hotel o altre aziende locali. Bing restituisce località se la query specifica il nome dell'azienda locale o richiede un tipo di azienda (ad esempio, ristoranti nelle vicinanze). Bing restituisce entità se la query specifica persone, oggetti o località note (attrazioni, stati, paesi e così via).

|Funzionalità  |DESCRIZIONE  |
|---------|---------|
|[Suggerimenti per la ricerca in tempo reale](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Suggerimenti per la ricerca che possono essere visualizzati come elenchi a discesa man mano che l'utente digita.       | 
| [Disambiguazione entità](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Ottenere più entità per le query con più significati possibili. |
| [Individuare le località](concepts/search-for-entities.md#find-places) | Cercare e restituire informazioni su entità e attività commerciali locali  |

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca entità Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. È possibile usare il servizio mediante l'API REST o il Software Development Kit.

1. Creare un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuito.
2. Inviare una richiesta all'API con una query di ricerca valida.
3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

* Provare la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) per l'API Ricerca entità Bing. 
* Per iniziare rapidamente con la prima richiesta, provare una [guida introduttiva](quickstarts/csharp.md).
* Sezione di riferimento sull'[API Ricerca entità Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference).
* I [requisiti per l'uso e la visualizzazione di Bing](./use-display-requirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.
