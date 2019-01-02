---
title: Entità predefinite URL
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita URL in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 5a1d86c58b6b6b8b54d170459157371e8e73f0e8
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163668"
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