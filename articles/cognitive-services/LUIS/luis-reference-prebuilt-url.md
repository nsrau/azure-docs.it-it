---
title: Entità predefinite URL
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita URL in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 3e5f6edf213838345c21598213b4fc2065e66335
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884138"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Entità predefinita URL per un'app LUIS
L'entità URL consente di estrarre gli URL con nomi di dominio o indirizzi IP. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti URL per le finalità dell'applicazione. L'entità URL è supportata solo nelle impostazioni cultura `en-us`. 

## <a name="types-of-urls"></a>Tipi di URL
L'entità URL viene gestita dal repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Risoluzione per l'entità URL predefinita
L'esempio seguente illustra la risoluzione dell'entità **builtin.url**.

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [ordinali](luis-reference-prebuilt-ordinal.md), [numeri](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md).
