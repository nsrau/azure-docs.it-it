---
title: Data alteration - LUIS
titleSuffix: Azure Cognitive Services
description: Informazioni su come modificare i dati prima delle previsioni in Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a199821c4db7fd8131ec54700b8c999dfe604a6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222015"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Modificare i dati delle espressioni prima e durante la stima
LUIS offre vari modi per manipolare le espressioni prima o durante la previsione. These include [fixing spelling](luis-tutorial-bing-spellcheck.md), and fixing timezone issues for prebuilt [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Correggere gli errori di ortografia nell'espressione

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


Per correggere gli errori di ortografia nell'espressione, LUIS usa l'[API Controllo ortografico Bing V7](../Bing-Spell-Check/overview.md) e ha bisogno della chiava associata a tale servizio. Occorre quindi creare la chiave e aggiungerla come parametro querystring all'[endpoint](https://go.microsoft.com/fwlink/?linkid=2092356). 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

L'endpoint richiede due parametri per il funzionamento delle correzioni ortografiche:

|Param|Value|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Chiave endpoint [API Controllo ortografico Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Quando l'[API Controllo ortografico Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) rileva un errore, dall'endpoint vengono restituite l'espressione originale e l'espressione corretta insieme alle previsioni.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)
 
```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * * 

### <a name="list-of-allowed-words"></a>List of allowed words
The Bing spell check API used in LUIS does not support a list of words to ignore during the spell check alterations. If you need to allow a list of words or acronyms, process the utterance in the client application before sending the utterance to LUIS for intent prediction.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Cambiare il fuso orario dell'entità datetimeV2 predefinita
When a LUIS app uses the prebuilt [datetimeV2](luis-reference-prebuilt-datetimev2.md) entity, a datetime value can be returned in the prediction response. Il fuso orario della richiesta viene utilizzato per determinare il valore datatime corretto da restituire. Se la richiesta proviene da un bot o da un'altra applicazione centralizzata prima di passare a LUIS, occorre correggere il fuso orario usato da LUIS. 

### <a name="endpoint-querystring-parameter"></a>Parametro queryString dell'endpoint
Per correggere il fuso orario aggiungere il fuso orario dell'utente all'[endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) mediante il parametro `timezoneOffset`. Il valore di `timezoneOffset` deve essere il numero positivo o negativo che, in minuti, modifica l'ora.  

|Param|Value|
|--|--|
|`timezoneOffset`|numero positivo o negativo, in minuti|

### <a name="daylight-savings-example"></a>Esempio di ora legale
Se è necessario che l'entità datetimeV2 predefinita restituita sia regolata in base all'ora legale, occorre usare il `timezoneOffset`parametro querystring con un valore +/- in minuti per la query [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356).

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Richiesta dell'endpoint di previsione V2](#tab/V2)

Aggiungere 60 minuti: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Sottrarre 60 minuti: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Richiesta dell'endpoint di previsione V3](#tab/V3)

Aggiungere 60 minuti:

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Sottrarre 60 minuti: 

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Il codice C# determina il valore corretto di timezoneOffset
Il seguente codice C# usa il metodo [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) della classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) per determinare il corretto `timezoneOffset` basato sull'ora di sistema:

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Correggere gli errori di ortografia con questa esercitazione](luis-tutorial-bing-spellcheck.md)
