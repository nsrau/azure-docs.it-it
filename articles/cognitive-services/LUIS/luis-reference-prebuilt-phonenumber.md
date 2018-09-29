---
title: Riferimento al numero di telefono per le entità predefinite LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita numero di telefono in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: bacb09b50c4b1d82daa6be1ef4fc79c88269cf7a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035454"
---
# <a name="phonenumber-entity"></a>Entità numero di telefono
L'entità`phonenumber` consente di estrarre una serie di numeri di telefono che includono il codice nazionale. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio all'applicazione. L'entità `phonenumber` è supportata solo nelle impostazioni cultura `en-us`. 

## <a name="types-of-phonenumber"></a>Tipi di numero di telefono
Il numero di telefono viene gestito dal repository Github [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Risoluzione per l'entità numero di telefono predefinita
L'esempio seguente illustra la risoluzione dell'entità **builtin.phonenumber**.

```JSON
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [percentuale](luis-reference-prebuilt-percentage.md), [numero](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md). 