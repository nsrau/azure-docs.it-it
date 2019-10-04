---
title: Tipo di entità composita-LUIS
titleSuffix: Azure Cognitive Services
description: Un'entità composita è costituita da altre entità, ad esempio entità predefinite, semplici, espressioni regolari ed elenchi. Le entità separate formano un'entità intera.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695159"
---
# <a name="composite-entity"></a>Entità composita 

Un'entità composita è costituita da altre entità, ad esempio entità predefinite, semplici, espressioni regolari ed elenchi. Le entità separate formano un'entità intera. 

**Questa entità è una scelta ottimale quando i dati:**

* Sono correlati tra loro. 
* Sono correlati tra loro nel contesto dell'espressione.
* Usano vari tipi di entità.
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.
* Hanno una serie di espressioni utente che richiedono l'apprendimento automatico.

![entità composita](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>JSON di esempio

Si consideri un'entità composita di `number` e `Location::ToLocation` precompilata con l'espressione seguente:

`book 2 tickets to cairo`

Si noti che tra `2`, il numero, `cairo` e ToLocation sono presenti delle parole che non fanno parte di nessuna delle entità. La sottolineatura verde, usata in un'espressione etichettata nel sito Web [LUIS](luis-reference-regions.md), indica un'entità composita.

![Entità composita](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 risposta endpoint di stima](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Risposta dell'endpoint di stima V3](#tab/V3)

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


|Oggetto dati|Nome dell'entità|Value|
|--|--|--|
|Entità predefinita - numero|"builtin.number"|"2"|
|Entità predefinita-GeographyV2|"Location::ToLocation"|Cairo|

## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](luis-tutorial-composite-entity.md), aggiungere un' **entità composita** per raggruppare i dati estratti di vari tipi in un'unica entità contenitore. Aggregando i dati, l'applicazione client può estrarre facilmente i dati correlati in diversi tipi di dati.
