---
title: Tipo di entità composita-LUIS
titleSuffix: Azure Cognitive Services
description: Un'entità composita è costituita da altre entità, ad esempio entità predefinite, semplici, espressioni regolari ed elenchi. Le entità separate formano un'entità intera.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 97bd079034a39f158e3ca438c484929cb6f00363
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542258"
---
# <a name="composite-entity"></a>Entità composita

Un'entità composita è costituita da altre entità, ad esempio entità predefinite, semplici, espressioni regolari ed elenchi. Le entità separate formano un'entità intera.

> [!CAUTION]
> Questa entità è **deprecata**. Eseguire la migrazione all' [entità Machine Learning](reference-entity-machine-learned-entity.md).

**Questa entità è idonea quando i dati:**

* Sono correlati tra loro.
* Sono correlati tra loro nel contesto dell'espressione.
* Usano vari tipi di entità.
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.
* Hanno una serie di espressioni utente che richiedono l'apprendimento automatico.

![entità composita](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>JSON di esempio

Si consideri un'entità composita di predefinita `number` e `Location::ToLocation` con l'espressione seguente:

`book 2 tickets to cairo`

Si noti che tra `2`, il numero, `cairo` e ToLocation sono presenti delle parole che non fanno parte di nessuna delle entità. La sottolineatura verde, usata in un'espressione etichettata nel sito Web [LUIS](luis-reference-regions.md), indica un'entità composita.

![Entità composita](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

Le entità composite vengono restituite in una matrice `compositeEntities` e tutte le entità nell'entità composita vengono restituite nella matrice `entities`:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)

Si tratta del codice JSON se `verbose=false` è impostato nella stringa di query:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Si tratta del codice JSON se `verbose=true` è impostato nella stringa di query:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *


|Oggetto dati|Nome dell'entità|Valore|
|--|--|--|
|Entità predefinita - numero|"builtin.number"|"2"|
|Entità predefinita-GeographyV2|"Location::ToLocation"|Cairo|

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle entità:

* [Concetti](luis-concept-entity-types.md)
* [Modalità di creazione](luis-how-to-add-entities.md)
