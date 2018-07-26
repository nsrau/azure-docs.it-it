---
title: Riferimento all'URL per le entità predefinite LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita URL in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 86989abab1dcf64384b8b26b9484bc508f2ce31f
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236789"
---
# <a name="url-entity"></a>Entità URL
L'entità URL consente di estrarre gli URL con nomi di dominio o indirizzi IP. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti URL per le finalità dell'applicazione. L'entità URL è supportata solo nelle impostazioni cultura `en-us`. 

## <a name="types-of-urls"></a>Tipi di URL
Gli URL vengono gestiti nel repository Github [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Risoluzione per l'entità URL predefinita
L'esempio seguente illustra la risoluzione dell'entità **builtin.url**.

```JSON
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