---
title: Riferimento alla percentuale per le entità predefinite LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita percentuale in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 6929fc3cc41db4e4bd4a1f8bda62c953a3722eff
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041168"
---
# <a name="percentage-entity"></a>Entità percentuale
I numeri in percentuale possono essere visualizzati come frazioni `3 1/2`, o come percentuale, `2%`. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti la percentuale per le finalità dell'applicazione. L'entità percentuale è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Tipi di percentuale
La percentuale viene gestita nel repository Github [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Risoluzione per l'entità percentuale predefinita
L'esempio seguente illustra la risoluzione dell'entità **builtin.percentage**.

```JSON
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