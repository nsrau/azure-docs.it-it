---
title: Entità predefinita keyPhrase
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita keyphrase in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: a2bb486de0999ad61cf2fdfdfb57dd91d376f597
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206598"
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
