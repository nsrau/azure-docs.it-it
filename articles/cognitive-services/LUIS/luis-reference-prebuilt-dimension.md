---
title: Entità predefinite della dimensione-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita dimensioni in Language Understanding, ovvero LUIS.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 15ba8ad4d3bcf00024a0c7b14b004de08d37621a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677659"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Entità predefinita dimension per un'app LUIS
L'entità predefinita dimensioni rileva i diversi tipi di dimensioni, indipendentemente dalle impostazioni cultura dell'app LUIS. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti le dimensioni per le finalità dell'applicazione. L'entità dimensioni è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Tipi di dimensioni

L'entità dimension viene gestita nel repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml).


## <a name="resolution-for-dimension-entity"></a>Risoluzione per l'entità dimensioni

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 risposta endpoint di stima](#tab/V2)

L'esempio seguente illustra la risoluzione dell'entità **builtin.dimension**.

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Risposta dell'endpoint di stima V3](#tab/V3)

Il codice JSON seguente è con il parametro `verbose` impostato su `false`:

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ]
        }
    }
}
```

Il codice JSON seguente è con il parametro `verbose` impostato su `true`:

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ],
            "$instance": {
                "dimension": [
                    {
                        "type": "builtin.dimension",
                        "text": "10 1/2 miles",
                        "startIndex": 19,
                        "length": 12,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [endpoint di stima V3](luis-migration-api-v3.md).

Informazioni sulle entità [posta elettronica](luis-reference-prebuilt-email.md), [numero](luis-reference-prebuilt-number.md) e [ordinale](luis-reference-prebuilt-ordinal.md). 
