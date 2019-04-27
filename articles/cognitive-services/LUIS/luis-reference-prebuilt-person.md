---
title: Entità predefinita personName
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita personName in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 7b748c507d5c848cc83a8a0c55cb7b05903bc542
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473128"
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
