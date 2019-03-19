---
title: Entità predefinita temperatura
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita temperatura in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 6a6600a0294eaef9f3b8ee6e7f6f021eb5c1e60b
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341803"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Entità predefinita temperatura per un'app LUIS
L'entità temperatura consente di estrarre una vasta gamma di tipi di temperatura. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere all'applicazione espressioni di esempio contenenti la temperatura. L'entità temperatura è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Tipi di temperatura
La temperatura viene gestita nel repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Risoluzione per l'entità temperatura predefinita
L'esempio seguente illustra la risoluzione dell'entità **builtin.temperature**.

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [percentuale](luis-reference-prebuilt-percentage.md), [numero](luis-reference-prebuilt-number.md) ed [età](luis-reference-prebuilt-age.md). 
