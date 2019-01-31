---
title: Entità predefinita Ordinal
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita ordinale in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 22b66b36eb3bde4e5953435d3ecf9d872d999589
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218700"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entità predefinita Ordinal per un'app LUIS
I numeri ordinali sono una rappresentazione numerica di un oggetto all'interno di un insieme: `first`, `second`, `third`. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti l'ordinale per le finalità dell'applicazione. L'entità ordinale è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Tipi di ordinale
L'entità Ordinal viene gestita dal repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Risoluzione per l'entità ordinale predefinita
L'esempio seguente illustra la risoluzione dell'entità **builtin.ordinal**.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [percentuale](luis-reference-prebuilt-percentage.md), [numero di telefono](luis-reference-prebuilt-phonenumber.md) e [temperatura](luis-reference-prebuilt-temperature.md). 
