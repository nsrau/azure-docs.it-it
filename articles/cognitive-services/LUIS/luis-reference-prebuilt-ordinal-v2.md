---
title: Ordinale V2 entità predefiniti
titleSuffix: Language Understanding - Azure Cognitive Services
description: Questo articolo contiene ordinali V2 le informazioni sulle entità predefinite in LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 862b962f5642e01d7ed8250f49d51a6132447083
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486140"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Ordinale V2 entità predefinite per un'app LUIS
Numero ordinale di V2 espande [ordinale](luis-reference-prebuilt-ordinal.md) specificare come i riferimenti relativi `next`, `last`, e `previous`. Questi non vengono estratti tramite l'entità predefinito ordinale.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Risoluzione per entità di V2 ordinali predefiniti

### <a name="api-version-2x"></a>Versione dell'API 2.x

L'esempio seguente illustra la risoluzione del **builtin.ordinalV2** entità.

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

### <a name="preview-api-version-3x"></a>Versione di anteprima API 3.x

Il codice JSON seguente è con il `verbose` parametro è impostato su `false`:

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

Il codice JSON seguente è con il `verbose` parametro è impostato su `true`:

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Scopri le [percentuale](luis-reference-prebuilt-percentage.md), [numero di telefono](luis-reference-prebuilt-phonenumber.md), e [temperatura](luis-reference-prebuilt-temperature.md) entità. 
