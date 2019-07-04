---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 67c95ffcdbdbcfbb9a86e15c91d984953d7bbffc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180460"
---
Per comprendere ciò che restituisce un endpoint di stima LUIS, visualizzare un risultato di stima in un Web browser. Per eseguire query su un'app pubblica, sono necessari la propria chiave e l'ID app. L'ID app IoT pubblica, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, viene fornito come parte dell'URL al passaggio uno.

Il formato dell'URL per una richiesta dell'endpoint **GET** è:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. L'endpoint dell'app IoT pubblica presenta il formato seguente: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Copiare l'URL e sostituire la chiave con il valore di `<YOUR_KEY>`.

2. Incollare l'URL in una finestra del browser e premere INVIO. Il browser visualizzerà un risultato JSON che indica che LUIS rileva la finalità `HomeAutomation.TurnOn` come finalità principale e l'entità `HomeAutomation.Room` con il valore `bedroom`.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. Sostituire il valore del parametro `q=` nell'URL con `turn off the living room light` e premere INVIO. Il risultato indica adesso che LUIS ha rilevato la finalità `HomeAutomation.TurnOff` come finalità principale e l'entità `HomeAutomation.Room` con valore `living room`. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
