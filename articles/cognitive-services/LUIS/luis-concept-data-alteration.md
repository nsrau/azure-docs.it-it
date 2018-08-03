---
title: Informazioni sui concetti di modifica dei dati in LUIS - Azure | Microsoft Docs
description: Informazioni su come modificare i dati prima delle previsioni in Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: diberry
ms.openlocfilehash: d8421114bb5a7416ad2523fe9b0353f03f672619
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223984"
---
# <a name="data-alterations"></a>Modifiche dei dati
LUIS offre vari modi per manipolare le espressioni prima o durante la previsione. 

## <a name="correct-spelling-errors-in-utterance"></a>Correggere gli errori di ortografia nell'espressione
Per correggere gli errori di ortografia nell'espressione, LUIS usa l'[API Controllo ortografico Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) e ha bisogno della chiava associata a tale servizio. Occorre quindi creare la chiave e aggiungerla come parametro querystring all'[endpoint](https://aka.ms/luis-endpoint-apis). 

È possibile correggere gli errori di ortografia anche nel pannello **Test** [immettendo la chiave](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Per il pannello Test, la chiave viene archiviata come una variabile di sessione nel browser. La chiave deve essere aggiunta nel pannello Test in ogni sessione del browser in cui si vogliono correggere gli errori di ortografia. 

L'utilizzo della chiave nel pannello Test e sull'endpoint viene incluso del conteggio della quota di [utilizzo della chiave](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/). LUIS implementa i limiti di Controllo ortografico Bing per la lunghezza del testo. 

L'endpoint richiede due parametri per il funzionamento delle correzioni ortografiche:

|Param|Valore|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Chiave endpoint [API Controllo ortografico Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Quando l'[API Controllo ortografico Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) rileva un errore, dall'endpoint vengono restituite l'espressione originale e l'espressione corretta insieme alle previsioni.

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
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Cambiare il fuso orario dell'entità datetimeV2 predefinita
Quando un'app LUIS usa l'entità datetimeV2 predefinita, può essere restituito un valore datetime nella risposta di previsione. Il fuso orario della richiesta viene utilizzato per determinare il valore datatime corretto da restituire. Se la richiesta proviene da un bot o da un'altra applicazione centralizzata prima di passare a LUIS, occorre correggere il fuso orario usato da LUIS. 

### <a name="endpoint-querystring-parameter"></a>Parametro queryString dell'endpoint
Per correggere il fuso orario aggiungere il fuso orario dell'utente all'[endpoint](https://aka.ms/luis-endpoint-apis) mediante il parametro `timezoneOffset`. Il valore di `timezoneOffset` deve essere il numero positivo o negativo che, in minuti, modifica l'ora.  

|Param|Valore|
|--|--|
|`timezoneOffset`|numero positivo o negativo, in minuti|

### <a name="daylight-savings-example"></a>Esempio di ora legale
Se è necessario che l'entità datetimeV2 predefinita restituita sia regolata in base all'ora legale, occorre usare il `timezoneOffset`parametro querystring con un valore +/- in minuti per la query [endpoint](https://aka.ms/luis-endpoint-apis).

Aggiungere 60 minuti: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Sottrarre 60 minuti: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Il codice C# determina il valore corretto di timezoneOffset
Il seguente codice C# usa il metodo [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) della classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) per determinare il corretto `timezoneOffset` basato sull'ora di sistema:

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