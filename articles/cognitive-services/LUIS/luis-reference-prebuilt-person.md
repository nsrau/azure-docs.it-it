---
title: Entità predefinita personName
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita personName in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 31dce870e99cbe74e2795a3ba661b0caf92dd48e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140235"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Entità predefinita personName per un'app LUIS
L'entità predefinita personName rileva i nomi di persona. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti personName per le finalità dell'applicazione. L'entità personName è supportata nelle [impostazioni cultura](luis-reference-prebuilt-entities.md) inglese e cinese.

## <a name="resolution-for-personname-entity"></a>Risoluzione per l'entità personName
L'esempio seguente illustra la risoluzione dell'entità **builtin.personName**.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [posta elettronica](luis-reference-prebuilt-email.md), [numero](luis-reference-prebuilt-number.md) e [ordinale](luis-reference-prebuilt-ordinal.md). 