---
title: Entità predefinita keyPhrase
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita keyphrase in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5ef7ccb58533161d8397ad42e70de1999908dc36
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338315"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Entità predefinita keyPhrase per un'app LUIS
keyPhrase estrae una serie di frasi chiave da un'espressione. Non è necessario aggiungere espressioni di esempio contenente frasi chiave all'applicazione. L'entità keyPhrase è supportata in [molte culture](luis-language-support.md#languages-supported) come parte delle funzionalità di [analisi del testo](../text-analytics/overview.md). 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Risoluzione per l'entità keyPhrase predefinita
L'esempio seguente illustra la risoluzione dell'entità **builtin.keyPhrase**.

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [percentuale](luis-reference-prebuilt-percentage.md), [numero](luis-reference-prebuilt-number.md) ed [età](luis-reference-prebuilt-age.md).
