---
title: Suggerimenti automatici per i termini di ricerca Bing
titleSuffix: Microsoft Cognitive Services
description: L'API Ricerca Web Bing può essere usata insieme all'API Suggerimenti automatici Bing per offrire agli utenti un'esperienza di ricerca migliorata.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: df8a57b3136abfacce971f4d01ccb2296dfa784c
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889390"
---
# <a name="autosuggest-bing-search-terms"></a>Suggerimenti automatici per i termini di ricerca Bing

Se si fornisce una casella di ricerca in cui l'utente immette il termine di ricerca, usare l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per migliorare l'esperienza. Mentre l'utente digita, l'API restituisce le stringhe di query suggerite, in base ai termini di ricerca parziali.

Dopo che l'utente ha immesso un termine di ricerca, questo deve essere codificato come URL prima dell'impostazione del parametro di query [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query). Se ad esempio l'utente immette *sailing dinghies*, impostare `q` su `sailing+dinghies` o `sailing%20dinghies`.

Se il termine di ricerca contiene un errore di ortografia, la risposta della ricerca include un oggetto [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext). L'oggetto mostra l'ortografia originale e l'ortografia corretta usata da Bing per la ricerca.

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

* [Informazioni di riferimento per l'API Ricerca Web Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
