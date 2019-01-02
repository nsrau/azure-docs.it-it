---
title: Entità predefinita Currency
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita valuta in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: c87bfddb611e3e232d4a43f45ae60412566b98fc
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161764"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Entità predefinita Currency per un'app LUIS
L'entità predefinita valuta rileva la valuta in numerose denominazioni e nazioni, indipendentemente dalle impostazioni cultura dell'app LUIS. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti la valuta per le finalità dell'applicazione. L'entità valuta è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

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