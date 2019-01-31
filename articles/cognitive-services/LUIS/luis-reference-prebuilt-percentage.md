---
title: Entità predefinita percentuale
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita percentuale in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 3cdf8844ae614e301e1477a039d949b055034ba8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208415"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Entità predefinita percentuale per un'app LUIS
I numeri in percentuale possono essere visualizzati come frazioni `3 1/2`, o come percentuale, `2%`. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti la percentuale per le finalità dell'applicazione. L'entità percentuale è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Tipi di percentuale
La percentuale viene gestita nel repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Risoluzione per l'entità percentuale predefinita
L'esempio seguente illustra la risoluzione dell'entità **builtin.percentage**.

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [ordinali](luis-reference-prebuilt-ordinal.md), [numeri](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md). 
