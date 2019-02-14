---
title: Entità predefinite numero di telefono
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita numero di telefono in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: fa2163b37bda5beba7c36bfdff0778c4062c5546
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863415"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>Entità predefinita numero di telefono per un'app LUIS
L'entità`phonenumber` consente di estrarre una serie di numeri di telefono che includono il codice nazionale. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio all'applicazione. L'entità `phonenumber` è supportata solo nelle impostazioni cultura `en-us`. 

## <a name="types-of-phonenumber"></a>Tipi di numero di telefono
Il numero di telefono viene gestito dal repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Risoluzione per l'entità numero di telefono predefinita
L'esempio seguente illustra la risoluzione dell'entità **builtin.phonenumber**.

```json
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
