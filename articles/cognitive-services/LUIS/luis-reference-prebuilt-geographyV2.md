---
title: Entità predefinita geographyV2
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita geographyV2 in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: a81c97cf8883d2a3629d1eb1b9f6137609c6edda
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135101"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>Entità predefinita geographyV2 per un'app LUIS
L'entità predefinita geographyV2 rileva le località. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti geographyV2 per le finalità dell'applicazione. L'entità geographyV2 è supportata solo nelle [impostazioni cultura](luis-reference-prebuilt-entities.md) inglese.

## <a name="subtypes"></a>Sottotipi
Le località geografiche hanno dei sottotipi:

|Subtype|Scopo|
|--|--|
|`poi`|punto di interesse|
|`city`|nome della città|
|`countryRegion`|nome del paese o dell'area geografica|
|`continent`|nome del continente|
|`state`|nome dello stato o della provincia|


## <a name="resolution-for-geographyv2-entity"></a>Risoluzione per l'entità geographyV2
L'esempio seguente illustra la risoluzione dell'entità **builtin.geographyV2**.

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [posta elettronica](luis-reference-prebuilt-email.md), [numero](luis-reference-prebuilt-number.md) e [ordinale](luis-reference-prebuilt-ordinal.md). 