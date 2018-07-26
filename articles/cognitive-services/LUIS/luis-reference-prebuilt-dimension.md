---
title: Riferimento alle dimensioni per le entità predefinite LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita dimensioni in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 3c923e7791c58255690100b04700577eb5c3f5dd
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237265"
---
# <a name="dimension-entity"></a>Entità di dimensione
L'entità predefinita dimensioni rileva i diversi tipi di dimensioni, indipendentemente dalle impostazioni cultura dell'app LUIS. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti le dimensioni per le finalità dell'applicazione. L'entità dimensioni è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Tipi di dimensioni

Le dimensioni vengono gestite nel repository Github [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)


## <a name="resolution-for-dimension-entity"></a>Risoluzione per l'entità dimensioni
L'esempio seguente illustra la risoluzione dell'entità **builtin.dimension**.

```JSON
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [posta elettronica](luis-reference-prebuilt-email.md), [numero](luis-reference-prebuilt-number.md) e [ordinale](luis-reference-prebuilt-ordinal.md). 