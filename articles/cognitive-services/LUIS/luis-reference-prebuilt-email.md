---
title: Riferimento alla posta elettronica per le entità predefinite LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita posta elettronica in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 1cb12bdc362955da907fb5a5ed64c2a1a43fdc32
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140864"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Entità predefinita posta elettronica per un'app LUIS
L'estrazione della posta elettronica include l'indirizzo di posta elettronica completo di un'espressione. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti la posta elettronica per le finalità dell'applicazione. L'entità posta elettronica è supportata solo nelle impostazioni cultura `en-us`. 

## <a name="resolution-for-prebuilt-email"></a>Risoluzione per il numero predefinito
L'esempio seguente illustra la risoluzione dell'entità **builtin.email**.

```json
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