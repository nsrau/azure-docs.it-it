---
title: Entità predefiniti valuta
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita valuta in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 1dfa81ad7981578d4f296de1b421c7e064819718
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867282"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Entità predefinita Currency per un'app LUIS
L'entità di valuta predefinito rileva valuta in molti denominazioni e paesi/aree geografiche, indipendentemente dalle impostazioni cultura app LUIS. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti la valuta per le finalità dell'applicazione. L'entità valuta è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Tipi di valuta
L'entità Currency viene gestita dal repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Risoluzione per entità valuta
L'esempio seguente illustra la risoluzione dell'entità **builtin.currency**.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimensioni](luis-reference-prebuilt-dimension.md) e [posta elettronica](luis-reference-prebuilt-email.md). 
