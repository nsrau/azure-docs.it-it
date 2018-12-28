---
title: Entità predefinita dimension
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita dimension in Language Understanding, ovvero LUIS.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 0ed5d68905353cc0b99a8ce5e1d1b6bfde9d1b87
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166048"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Entità predefinita dimension per un'app LUIS
L'entità predefinita dimension rileva i diversi tipi di dimensioni, indipendentemente dalle impostazioni cultura dell'app LUIS. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti le dimensioni per le finalità dell'applicazione. L'entità dimension è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Tipi di dimensione

L'entità dimension viene gestita nel repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml).


## <a name="resolution-for-dimension-entity"></a>Risoluzione per l'entità dimension
L'esempio seguente illustra la risoluzione dell'entità **builtin.dimension**.

```json
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