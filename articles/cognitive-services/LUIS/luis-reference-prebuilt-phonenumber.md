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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 43d0b855c25ed10b074d99b247ee56dc2ba7769b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146188"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Entità predefiniti numeri di telefono per un'app LUIS
L'entità`phonenumber` consente di estrarre una serie di numeri di telefono che includono il codice nazionale. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio all'applicazione. L'entità `phonenumber` è supportata solo nelle impostazioni cultura `en-us`. 

## <a name="types-of-a-phone-number"></a>Tipi di un numero di telefono
`Phonenumber` è gestito dal [riconoscitori testo](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) repository GitHub

## <a name="resolution-for-this-prebuilt-entity"></a>Risoluzione per questa entità predefinita

### <a name="api-version-2x"></a>Versione dell'API 2.x

L'esempio seguente illustra la risoluzione dell'entità **builtin.phonenumber**.

```json
{
  "query": "my mobile is 1 (800) 642-7676",
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
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Versione di anteprima API 3.x

Il codice JSON seguente è con il `verbose` parametro è impostato su `false`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

Il codice JSON seguente è con il `verbose` parametro è impostato su `true`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ],
            "$instance": {
                "phonenumber": [
                    {
                        "type": "builtin.phonenumber",
                        "text": "1 (800) 642-7676",
                        "startIndex": 13,
                        "length": 16,
                        "score": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [percentuale](luis-reference-prebuilt-percentage.md), [numero](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md). 
