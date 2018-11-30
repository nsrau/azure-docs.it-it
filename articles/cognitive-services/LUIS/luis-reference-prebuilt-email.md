---
title: Riferimento alla posta elettronica per le entità predefinite LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita posta elettronica in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6d05f62ad725a89b0a34b21b8a8d36bb8fa464b1
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441012"
---
# <a name="email-entity"></a>Entità di posta elettronica
L'estrazione della posta elettronica include l'indirizzo di posta elettronica completo di un'espressione. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti la posta elettronica per le finalità dell'applicazione. L'entità posta elettronica è supportata solo nelle impostazioni cultura `en-us`. 

## <a name="resolution-for-prebuilt-email"></a>Risoluzione per il numero predefinito
L'esempio seguente illustra la risoluzione dell'entità **builtin.email**.

```JSON
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [numero](luis-reference-prebuilt-number.md), [ordinale](luis-reference-prebuilt-ordinal.md) e [percentuale](luis-reference-prebuilt-percentage.md). 