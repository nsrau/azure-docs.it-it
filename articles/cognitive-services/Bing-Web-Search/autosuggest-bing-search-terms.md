---
title: Suggerimenti automatici per i termini di ricerca - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: L'API Ricerca Web Bing può essere usata insieme all'API Suggerimenti automatici Bing per offrire agli utenti un'esperienza di ricerca avanzata.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 5bd5ff0e49b125277255ec8e5c216583d75043a0
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381134"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Suggerimenti automatici per i termini di ricerca di Bing nell'applicazione

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Se si fornisce una casella di ricerca in cui l'utente immette il termine di ricerca, usare l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per migliorare l'esperienza. Mentre l'utente digita, l'API restituisce le stringhe di query suggerite, in base ai termini di ricerca parziali.

Dopo che l'utente ha immesso un termine di ricerca, questo deve essere codificato come URL prima dell'impostazione del parametro di query [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query). Se ad esempio l'utente immette *sailing dinghies* , impostare `q` su `sailing+dinghies` o `sailing%20dinghies`.

Se il termine di ricerca contiene un errore di ortografia, la risposta della ricerca include un oggetto [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext). L'oggetto mostra l'ortografia originale e l'ortografia corretta usata da Bing per la ricerca.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

È possibile usare queste informazioni per informare l'utente che la stringa di query è stata modificata quando vengono visualizzati i risultati della ricerca.

![Esempio di esperienza utente con contesto di query](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Passaggi successivi  

* Vedere l'esempio [Risposte dell'API Ricerca Web Bing](search-responses.md).  

## <a name="see-also"></a>Vedere anche  

* [Informazioni di riferimento per l'API Ricerca Web Bing](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)