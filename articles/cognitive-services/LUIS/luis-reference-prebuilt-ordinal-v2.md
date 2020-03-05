---
title: Entità precompilata ordinale V2-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinite della versione 2 ordinale in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270493"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Entità precompilata ordinale V2 per un'app LUIS
Il numero ordinale V2 espande il numero [ordinale](luis-reference-prebuilt-ordinal.md) per fornire riferimenti relativi, ad esempio `next`, `last`e `previous`. Non vengono estratti utilizzando l'entità predefinita ordinale.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Risoluzione per l'entità predefinita ordinale V2

Per la query vengono restituiti gli oggetti entità seguenti:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Risposta V3](#tab/V3)

Il codice JSON seguente è con il parametro `verbose` impostato su `false`:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Risposta dettagliata V3](#tab/V3-verbose)

Il codice JSON seguente è con il parametro `verbose` impostato su `true`:

```json
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
```
#### <a name="v2-response"></a>[Risposta V2](#tab/V2)

Nell'esempio seguente viene illustrata la risoluzione dell'entità **Builtin. ordinalV2** .

```json
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
```
* * *

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

Informazioni sulle entità [percentuale](luis-reference-prebuilt-percentage.md), [numero di telefono](luis-reference-prebuilt-phonenumber.md)e [temperatura](luis-reference-prebuilt-temperature.md) .
