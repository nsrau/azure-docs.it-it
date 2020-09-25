---
title: Modifica dei dati-LUIS
description: Informazioni su come modificare i dati prima delle previsioni in Language Understanding (LUIS)
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f3eb30662cae5f117942db6322b27491670abb1b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324842"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Modificare i dati delle espressioni prima e durante la stima
LUIS offre vari modi per manipolare le espressioni prima o durante la previsione. Che includono la [correzione dell'ortografia](luis-tutorial-bing-spellcheck.md)e la correzione dei problemi di fuso orario per [datetimeV2](luis-reference-prebuilt-datetimev2.md)predefiniti.

## <a name="correct-spelling-errors-in-utterance"></a>Correggere gli errori di ortografia nell'espressione


### <a name="v3-runtime"></a>Runtime V3

Pre-elaborare il testo per le correzioni ortografiche prima di inviare l'espressione a LUIS. Usare espressioni di esempio con l'ortografia corretta per assicurarsi di ottenere le stime corrette.

Usare [controllo ortografico Bing](../bing-spell-check/overview.md) per correggere il testo prima di inviarlo a Luis.

### <a name="prior-to-v3-runtime"></a>Prima del runtime V3

Per correggere gli errori di ortografia nell'espressione, LUIS usa l'[API Controllo ortografico Bing V7](../Bing-Spell-Check/overview.md) e ha bisogno della chiava associata a tale servizio. Occorre quindi creare la chiave e aggiungerla come parametro querystring all'[endpoint](https://go.microsoft.com/fwlink/?linkid=2092356).

L'endpoint richiede due parametri per il funzionamento delle correzioni ortografiche:

|Param|valore|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Chiave endpoint [API Controllo ortografico Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Quando l'[API Controllo ortografico Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) rileva un errore, dall'endpoint vengono restituite l'espressione originale e l'espressione corretta insieme alle previsioni.

#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)

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

### <a name="list-of-allowed-words"></a>Elenco di parole consentite
L'API controllo ortografico Bing utilizzata in LUIS non supporta un elenco di parole da ignorare durante le modifiche del controllo ortografico. Se è necessario consentire un elenco di parole o acronimi, elaborare l'espressione nell'applicazione client prima di inviare l'espressione a LUIS per la stima degli intenti.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Cambiare il fuso orario dell'entità datetimeV2 predefinita
Quando un'app LUIS USA l'entità [datetimeV2](luis-reference-prebuilt-datetimev2.md) predefinita, nella risposta di stima può essere restituito un valore DateTime. Il fuso orario della richiesta viene utilizzato per determinare il valore datatime corretto da restituire. Se la richiesta proviene da un bot o da un'altra applicazione centralizzata prima di passare a LUIS, occorre correggere il fuso orario usato da LUIS.

### <a name="v3-prediction-api-to-alter-timezone"></a>API di stima V3 per modificare il fuso orario

In V3, il `datetimeReference` determina l'offset del fuso orario. Altre informazioni sulle [stime V3](luis-migration-api-v3.md#v3-post-body).

### <a name="v2-prediction-api-to-alter-timezone"></a>V2 API di stima per modificare il fuso orario
Il fuso orario viene corretto aggiungendo il fuso orario dell'utente all'endpoint usando il `timezoneOffset` parametro in base alla versione dell'API. Il valore del parametro deve essere il numero positivo o negativo, in minuti, per modificare l'ora.

#### <a name="v2-prediction-daylight-savings-example"></a>V2 stima dell'ora legale di stima
Se è necessario che la datetimeV2 predefinita restituita venga modificata per l'ora legale, è consigliabile usare il parametro QueryString con un valore +/-in minuti per la query dell' [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) .

Aggiungere 60 minuti:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Sottrarre 60 minuti:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>V2 il codice C# di stima determina il valore corretto del parametro

Il codice C# seguente usa il metodo [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) della classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) per determinare il valore di offset corretto in base all'ora di sistema:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Correggere gli errori di ortografia con questa esercitazione](luis-tutorial-bing-spellcheck.md)
